# -*- mode: ruby -*-
# vi: set ft=ruby :

servers = [
    {
        :name => "master",
        :type => "master",
        :box => "ubuntu/xenial64",
        :box_version => "20200320.0.0",
        :network_ip => "192.168.50.10",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "node-1",
        :type => "node",
        :box => "ubuntu/xenial64",
        :box_version => "20200320.0.0",
        :network_ip => "192.168.50.11",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "node-2",
        :type => "node",
        :box => "ubuntu/xenial64",
        :box_version => "20200320.0.0",
        :network_ip => "192.168.50.12",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "node-3",
        :type => "node",
        :box => "ubuntu/xenial64",
        :box_version => "20200320.0.0",
        :network_ip => "192.168.50.13",
        :mem => "2048",
        :cpu => "2"
    }
]

# This script to install k8s using kubeadm will get executed after a box is provisioned
$configureBox = <<-SCRIPT
    apt-get update
    apt-get install -y apt-transport-https ca-certificates curl software-properties-common

    # Install docker
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
    apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io

    # run docker commands as vagrant user (sudo not required)
    usermod -aG docker vagrant

    # change docker cgroup driver.
    cat > /etc/docker/daemon.json <<EOF
    {
      "exec-opts": ["native.cgroupdriver=systemd"],
      "log-driver": "json-file",
      "log-opts": {
        "max-size": "100m"
      },
      "storage-driver": "overlay2"
    }
EOF

    # Restart docker.
    systemctl daemon-reload
    systemctl restart docker

    # install kubeadm, kubeadm, kubectl
    apt-get install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
    apt-get update
    apt-get install -y kubelet kubeadm kubectl
    apt-mark hold kubelet kubeadm kubectl

    # kubelet requires swap off
    swapoff -a
    # keep swap off after reboot
    sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

    # Set nodeip of the box
    IP_ADDR=`ifconfig enp0s8 | grep Mask | awk '{print $2}'| cut -f2 -d:`
    KUBELET_CONFIG_FILE="/etc/default/kubelet"
    if [ ! -f "$KUBELET_CONFIG_FILE" ]
    then
        touch "$KUBELET_CONFIG_FILE"
        echo KUBELET_EXTRA_ARGS=--node-ip="$IP_ADDR" >> "$KUBELET_CONFIG_FILE"
    else
        sed -i "/^[^#]*KUBELET_EXTRA_ARGS=/c\KUBELET_EXTRA_ARGS=--node-ip=$IP_ADDR" "$KUBELET_CONFIG_FILE"
    fi

    # Restart kubectl
    systemctl daemon-reload
    systemctl restart kubelet
SCRIPT

$configureMaster = <<-SCRIPT
    echo "This is master"

    # ip of this box
    IP_ADDR=`ifconfig enp0s8 | grep Mask | awk '{print $2}'| cut -f2 -d:`
    # install k8s master
    HOST_NAME=$(hostname -s)
    kubeadm init --apiserver-advertise-address=$IP_ADDR --apiserver-cert-extra-sans=$IP_ADDR  --node-name $HOST_NAME --pod-network-cidr=192.168.0.0/16

    #copying credentials to regular user - vagrant
    mkdir -p /home/vagrant/.kube
    cp -i /etc/kubernetes/admin.conf /home/vagrant/.kube/config
    chown $(id -u vagrant):$(id -g vagrant) /home/vagrant/.kube/config -R

    # install Calico pod network addon
    kubectl apply -f https://docs.projectcalico.org/v3.11/manifests/calico.yaml

    # create join command script
    kubeadm token create --print-join-command >> /etc/kubeadm_join_cmd.sh
    chmod +x /etc/kubeadm_join_cmd.sh

    # required for setting up password less ssh between guest VMs
    sed -i "/^[^#]*PasswordAuthentication[[:space:]]no/c\PasswordAuthentication yes" /etc/ssh/sshd_config
    service sshd restart
SCRIPT

$configureNode = <<-SCRIPT
    echo "This is worker"

    # Install sshpas
    apt-get install -y sshpass

    # copy join command script to node and run it for node to join cluster
    sshpass -p "vagrant" scp -o StrictHostKeyChecking=no vagrant@192.168.50.10:/etc/kubeadm_join_cmd.sh .
    /bin/bash ./kubeadm_join_cmd.sh
SCRIPT

Vagrant.configure("2") do |config|

    servers.each do |opts|
        config.vm.define opts[:name] do |config|

            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]
            config.vm.network "private_network", ip: opts[:network_ip]
            config.vm.synced_folder "./hello-world", "/srv/app/"

            config.vm.provider "virtualbox" do |v|

                v.name = opts[:name]
                v.customize ["modifyvm", :id, "--groups", "/K8sCluster"]
                v.customize ["modifyvm", :id, "--memory", opts[:mem]]
                v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]

            end

            config.vm.provision "shell", inline: $configureBox

            if opts[:type] == "master"
                config.vm.provision "shell", inline: $configureMaster
            else
                config.vm.provision "shell", inline: $configureNode
            end

        end

    end

end
