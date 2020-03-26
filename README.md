# Vagrant Kubernetes Cluster

This is a challenge that depecits the deployment of a Kubernetes cluster on virtual machines (Vagrant and Virtualbox). The webapp is deployed onto the cluster and is accessible from a web browser.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Pre-requisites
#### Macos
1. Install [Homebrew](https://brew.sh/)

2. Install Virtualbox
   ```
   $ brew cask install virtualbox
   ```
3. Install Vagrant
   ```
   $ brew cask install vagrant
    ```
#### Linux
- Use the appropriate package manager for your distribution
1. Install Virtualbox
   ```
   $ sudo apt install virtualbox
   ```

2. Install Vagrant
   ```
   $ sudo apt update
   $ curl -O https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.deb
   $ sudo apt install ./vagrant_2.2.6_x86_64.deb
   ```


 To ensure everything is all set up, run
    ```
    $ vagrant --version
    $  vboxmanage --version
    ```

## Installing
* Run Vagrant to setup the cluster from your terminal.
  ```sh
  $ vagrant up
  ```

* Apply the deployment Kubernetes manifest, to create the application pod.
  ```sh
  $ vagrant ssh master -c 'kubectl apply -f /srv/app/k8s/hello-world-deployment.yaml'
  ```

* Apply the service Kubernetes manifest, to create a network service that exposes the application.
  ```sh
  $ vagrant ssh master -c 'kubectl apply -f /srv/app/k8s/hello-world-service.yaml'
  ```

* To view the application, visit [hello-world-spring-boot](http://localhost:8080/)

## Running the tests

* To run tests run the following
    ```
    $ ./hello-world/gradlew test
    ```

## Linting
* To run linting run the following
    ```
    $ ./hello-world/gradlew ktlintCheck
    ```

## Built With

* [Springboot](https://spring.io/projects/spring-boot) - The web framework used
* [Gradle](https://gradle.org/) - Dependency Management

## About the project

I enjoyed this challenge and learned a lot along the way. I'm well versed with Kubernetes for containter orcherstration, Jenkins, CirclCI, GitLabCI, Github Actions for countinous integration, Spinnaker for continous delivery,
and Terraform for resource provisioning using code. This challenge has expanded my devops skills and allowed me to visualize how this setup would work on Vagrant and VirtualBox.

## Further enhancements
In this project, I did the following:

1. Created a Vagrant script that provisions three virtual machines for the Kubernetes cluster, one for master and the remaining two for worker nodes.
2. Created a basic SpringBoot application that returns {'hello': 'world'}.
3. Created a Dockerfile to containerize the application.
4. Created Kubernetes manifests to deploy the application.
5. Created Github Actions workflows for pull-requests and pushes to master. 

I believe the project can be further enhanced by creating a CD pipeline that grabs the image pushed to the registry and deploys it to a production cluster.
