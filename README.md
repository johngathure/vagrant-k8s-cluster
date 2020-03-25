# vagrant Kubernetes Cluster

This is a challenge that depecits the deployment of a Kubernetes cluster on virtual machines (Vagrant and Virtualbox). The webapp is deployed onto the cluster and is accessible from a web browser.


## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

1. [Virtualbox](https://www.virtualbox.org/wiki/Downloads)

2. [Vagrant](https://www.vagrantup.com/downloads.html)

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

## Running the tests

* To run tests run run the following
```
$ ./hello-world/gradlew test
```

## Linting
* To run linting run run the following
```
$ ./hello-world/gradlew ktlintCheck
```

## Built With

* [Springboot](https://spring.io/projects/spring-boot) - The web framework used
* [Gradle](https://gradle.org/) - Dependency Management

## About the project.

I enjoyed this challenge and learned a lot along the way. I'm well versed with Kubernetes for containter orcherstration, Jenkins, CirclCI, GitLabCI, Github Actions for countinous integration, Spinnaker for continous delivery,
and Terraform for resource provisioning using code. This challenge has expanded my devops skills and allowed me to visualize how this setup would work on Vagrant and VirtualBox.

## Further enhancements.
- In this project, I did the following:

1. Created a Vagrant script that provisions three virtual machines for the Kubernetes cluster, one for master and the remaining two for worker nodes.
2. Created a basic SpringBoot application that returns {'hello': 'world'}.
3. Created a Dockerfile to containerize the application.
4. Created Kubernetes manifests to deploy the application.

I believe the project can be further enhanced by creating a CI pipeline that continously tests new features added to the SpringBoot application and checks code quality using a linting tool (ktlint).
