# Docker-Course

1. Introduction and Installation

```
Introduction
verifying requirements for Docker Installation
Installing Docker  on Ubuntu, Centos
Installing Docker on linux wth automated script
Installing Docker for windows
Pulling image and running a container
Adding a nonroot user to administer Docker
Finding help with the Docker command line
```

2. Working with Docker Containers

```
Working with Docker Containers
Introduction
Listing/searching for an image
Pulling an image
listing images
starting a container
listing containers
looking at the container logs
stopping a container
removing a container
removing all stopped containers
setting the restart policy on a container
getting privilege access inside a container
accessing the host device inside a container
injecting a new process into a running container
Reading container's metadata
labelling and filtering containers
Reaping a a zombie inside a container
```

3. Working with Docker Images

```
Introduction
Creating an image from the container
creating an account with DockerHub
Logging in and out of the Docker image registry
publishing an image to a registry
looking at the history of an image
removing an image
exporting an image
importing an image
building an image using a docker file
building an apache image - a Dockerfile example
setting a private index/registry
automated builds -with github and bitbucket
creating a custom base image
creating a minimal image using a scratch base image
building images in multiple stages
visualizing the image hierarchy
```

4. Network and Data Management for Containers

```
Network and data management for containers
Introduction
accessing containers from outside
attaching containers to a host network
launching containers with no network
sharing ip addresses with other containers
creating a user defined bridge network
discovering and loadbalancing containers
persisting data using volumes
sharing data between the host and the container
```

5. Docker Use Cases
```
Docker Usecases
Introduction
Testing with Docker
Performing CI/CD with shippable and heroku
performing CI/CD with TravisCI
Setting up Paas with Openshift origin
Building and Deploying an app on Openshift from sourcecode
```

6. Docker APIs and SDKs
```
Docker APIs and SDKs
Introduction
Working with images using APIs
Building images using APIs
Launching containers using APIs
Performing Container operations using APis
Exploring Docker remote API client libraries
configuring the Docker Daemon for remote connectivity
Securing the Docker Daemon's remote connectivity
```

7. Docker Performance
```
Docker Performance
Introduction
Benchmarking CPU performance
Benchmarking Disk Performance
Benchmarking network performance
getting container resource usage using the stats feature
setting up performance monitoring
```

8. Docker Orchestration and Hosting a platform
```
Docker orchestration and hosting a platform
Introduction
Running applications with Docker compose
setting up a cluster with Docker swarm
using secrets with Docker swarm
seeting up a kubernetes cluster
using secrets with kubernetes
scaling up and down in kubernetes cluster
setting up wordpress with kubernetes clusters
```
9. Docker Security
```
Docker Security
Introduction
Setting mandatory access control (MAC) with Selinux
Allowing writes to volumes mounted from the host with Selinux ON
Removing capabilities to break down the power of a root user inside a container
sharing namespaces between the host and the container
```
10. Tips and Tricks
```
Introduction
starting docker in debug mode
building a docker binary from the source
building images without using cached layers
building  your own bridge for container communication
changing the default OCI runtime
selecting the loggin driver for containers
getting realtime docker events for containers

```

11. Docker on the Cloud
```
Docker on the cloud
Introduction
Docker for AWS
Deploying Wordpress on Docker for AWS
Docker for Azure
Deploying Joomla on Docker for Azure
```
