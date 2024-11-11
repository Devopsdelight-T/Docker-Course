

## Introduction and Installation


![image](https://github.com/user-attachments/assets/cd88e229-3220-44a7-96df-b33e510152f4)

```
Traditional application Deployment

single userspace
Runtime was shared between applications
Stable Deployment
Hardware centric
long maintainance cycle
Less flexibllity to developers
Underutilization of resources
```


![image](https://github.com/user-attachments/assets/8000a667-f727-415b-b4e5-f08d74fa6322)

- virtualization was invented. With hypervisors, such as KVM, XEN, ESX, Hyper-V, and so on, we emulated the hardware for virtual machines (VMs) and deployed a guest OS on each virtual machine.

```
Application Deployment in a virtualized Environment

- VMs can have a different OS than their host; this means that we are responsible for managing patches, security, and the performance of that VM.
- flexible Deployments
- Better utilization of resources
- Better ROI
- High flexibility at the cost of increased complexity and redundancy
- With Virtualization applications are isolated at VM level and are defined by the lifecycle of VMS.

```

- openvz, solaris zones and LXC are the few examples of containerization technology.

  ![image](https://github.com/user-attachments/assets/d518df02-be02-462d-bc5e-5622ac928a6a)

- Docker was started as an internal project in dotcloud by its founder soloman hykes. It was released as an opensource in march 2013 under the apache 2.0 license. With Dotcloud's platform as a service experience , the founders and engineers of docker were aware of challenges of running containers. So with docker, they developed a standard way to manage containers.

- Docker uses os's underlyuing kernel features, which enables containerization. The following diagram depicts the docker platform and kernel features used by docker

 ![image](https://github.com/user-attachments/assets/40f36412-d5cf-4470-9425-f0d2a47919a4)

- Docker Platform and the key features used by Docker


### Namespaces

- Namespaces are building blocks of the containers. There are different types of namespaces, each one of them isolates applications from the others. They are created from a clone system call. We can also attach to existing namespaces. Below are the some of the namespaces used by the Docker

#### 1. PID Namespace
- It allows each container to have its own process numbering. Each PID forms its own process hierarchy. A parent namespace can see the children namespaces and affect them, but a child can never see the parent namespace nor affect it.

#### 2. Net Namespace

- With PID Namespace, we can run same program multiple times in different isolated environments; for ex, we can run different instances of apache on different containers. But, without `net namespace` we would not be able to listen on port `80` on each one of them. The net namespace allows us to have different network interfaces on each container. loopback interfaces would be different in each container as well.

- To enable networking in containers, we create pairs of special interfaces in two different net namespaces and allow them to talk to each other.
- One end of the special interface resides inside the container and other resides on the host system.
- Generally the interfaces resides inside the container is called as `eth0` and on the host system, it is given a random name, such as `veth516cc33`. These special interfaces are then linked through a bridge (docker0) on the host to enable communication between the containers and the route packets.
- Each net namespace has its own routing table and firewall

#### 3. IPC namespace
- The `Inter-process Communication (IPC)` namespace provides semaphores, message queues and shared memory segments.
- If the IPC resource created by one container is consumed by another container, then the application running on the first container could fail.
- With IPC namespace, processes running in one namespace cannot access resources from another namespace

#### 4. mnt Namespace
- using only  `chroot`, you can inspect the relative paths of the system from a `chrooted` directory/namespace.
- The `mnt` namespace takes the idea of chroots to the next level. With mnt namespace, a container can have its own set of mounted filesystems and root directories.
- Processes in one `mnt` namespace cannot see the mounted file systems of another mnt namespace.

#### 5. UTS Namespace
- With the UTS Namespace, we can have different hostnames for each container

#### 6. User Namespace
- with user namespace support, we can have users who have a nonzero ID on the host, but who can have a zero ID inside the container. This is because the user namespace allows mappings of users and groups IDs per namespace.

#### 7. cgroups
- Control Groups (cgroups) provide resource limitations and accounting for containers.
- In simple terms, they can be compared to the `ulimit` shell command or the `setrlimit` system call. Instead of setting the resource limit to a single process, cgroups allow you to limit resources to a group of processes.
- Control groups are split into different subsystems, such as CPU, CPU sets, memory block I/O, and so on. Each subsystem can be used independently, or can be grouped with others. The features that cgroups provide are as follows :

```
Resource limiting
Prioritization
Accounting
Control
```

Some of the subsystems that can be managed by cgroups are as follows :
```
blkio: Sets I/O access to and from block devices, such as disks, SSDs, and so on
Cpu: Limits access to CPU
Cpuacct: Generates CPU resource utilization
Cpuset: Assigns CPUs on a multicore system to tasks in a cgroup
Devices: Grants devices access to a set of tasks in a group
Freezer: Suspends or resumes tasks in a cgroup
Memory: Sets limits on memory use by tasks in a cgroup
```
- There are multiple ways to control work with cgroups. Two of the most popular ones are accessing the cgroup virtual filesystem manually and accessing it with the libcgroup library.
- To use libcgroup on linux, run the following command to install the required packages on ubuntu or debian

`sudo apt-get install cgroup-tools`

- To install the required packages on CentOs, Fedora, or Redhat use the following code :
`$ sudo yum install libcgroup libcgroup-tools`

- once installed, you can get the list of subsystems and their mountpoint in the pseudo filesystem with the command `lssubsys -M`
- let's assume that we are running a few containers and want to get the `cgroup` entries for a container. To get those, first we need to get the containerID and then use the `lscgroup` command to get cgroup entries of a container, which we can get using `docker container ps`


#### Union filesysem

- The union file system allows the files and directories of separate filesystems, known as layers, to be transparently overlaid to create a new virtual filesystem.
- While starting a container docker overlays all the layers attached to an image and creates a read-only filesystem. On to p of that docker creates a read/write layer that is used by the container's runtime environment.
- Docker can use several union filesystem variants, including AUFs, Btrfs, zfs, overlay, overlay2 and DeviceMappers
- Docker also has a virtual file system (VFS) storage driver.A VFS doesn't support copy-on-write (COW) and is not a union file system. This means that each layer is a directory on the disk, and each time a new layer is created, it requires a deep copy of its parent layer. For these reasons, it has lower performance and requires more disk space, but it is a robust and stable option that works in every environment.

#### Container format

- The Docker engine combines the namespaces, control groups and UnionFS into a wrapper called a `container format`.
- In 2015, Docker donated its container format and runtime to an organization called the `open container initiative (OCI). The OCI is a lightweight, open goverance structure formed under the linux foundation by docker and other industry leaders. The purpose of OCI is to create industry standards around container formats and runtimes. There are currently two specifications: The runtime specification and the image specification.

- `The runtime spcification  outlines how to run an OCI runtime filesystem bundle`
- Docker donated its Docker V2 image format to the OCI to form the basis of the OCI image specification
- There are currently two container engines that support the OCI Runtime and Image specifications : Docker and rkt.

#### Verifying requirements for Docker installation
- login as a root user and give ` uname -i` to verify the os orchitecture . Because docker doesnot support 32-bit architecture.
- check kernel version using `uname -r`, docker is supported on kernel 3.8 or later.
- Running kernel should support on appropriate storage backend. Some of the options for such a backend are VFS, DeviceMapper, AUFS, Btrfs, Zfs and Overlayfs.
   For ubuntu default storage backend or driver is `overlay2`, another popular one is `DeviceMapper` thin provisioning module to implement layers. It should be installed by default on a majority of linux platforms.
- To check device mapper, we can use `grep device-mapper /proc/devices`
- Use below commands to check the support for cgroups and namespaces have been in the kernel for sometime, and should be enabled by default. To check their presence use the below :
```
    $ grep -i namespaces /boot/config-4.15.0-29-generic
    CONFIG_NAMESPACES=y

    $ grep -i cgroups /boot/config-4.15.0-29-generic
    CONFIG_CGROUPS=y
```

```
$ sudo apt-get remove docker docker-engine docker.io # to uninstall older versions of docker
$ sudo apt-get update  #update the apt package index
$ sudo apt-get install \    #Install the packages to allow apt to use a repository over HTTPS
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    OK #add docker's official GPG Key
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    OK #verify the key installation
$ sudo add-apt-repository \ #Add the Docker apt repository using the stable channel
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"
$ sudo apt-get update # update the packages
$ sudo apt-get install docker-ce # install the latest version of Docker CE
$ sudo docker container run hello-world # verify the installation
$ sudo systemctl start docker #To start the services
$ docker info # To verify the installation
$ sudo apt-get update # To update the package
$ sudo systemctl enable docker # To enable start of the service at bootime
$ sudo systemctl stop docker # To stop the services
```

- For more details visit `https://docs.docker.com/install/linux/docker-ce/`

#### Pulling an image and running a container

```
docker image pull alpine # To pull an image
docker image ls # To list the existing images
docker container run -id --name demo alpine ash #create a container using the pulled image and list the containers

```

#### Docker Architecture

- Docker has client-server architecture. It's binary consists of both docker client and server daemon and can reside on the same host.
- The client can communicate via sockets or restful api to either local or remote docker daemon.
- The Docker daemon builds, runs and distributes containers.
- The Docker client sends the command to the docker daemon running on the host machine.
- The docker daemon also connects to either a public or local registry to get images requested by the client.

![image](https://github.com/user-attachments/assets/bb3ada3a-d84a-41e2-adcc-616d6388952e)

the docker client sends requeest to the daemon running on the local system, which then connects to public docker registry and downloads the image. once it is downloaded, we can run it.

![image](https://github.com/user-attachments/assets/15fb130f-3851-43a2-a933-ad41ec421fa1)

/*some keywords*/

**images**: Docker images are read-only templates, and they give us containers during runtime. They are based on the idea of base image and layers resting on top of it.  For ex: we can have a base image of alpine or ubuntu and then we can install packages or make modifications over the base image to create a new layer. The base image and new layer can be treated as a new image.  For ex: in the above fig, Debian is the base image and emacs, apache are two layers added on top of it. They are highly portable and shared easily.

- layers are transparently laid on top of the base image to create single coherent file system.

**Registries**: Registries holds docker images. It can be public or private, depending on the location from which you can download or upload images. The public docker registry is called **dockerhub**

**index**: An index manages user accounts, permissions, searches, tagging, and all that nice stuff that's in the public web interface of the docker registry

**containers**: containers run the images that are created on top of base image and layers on top of it. They contain everything that is needed to run an application. a temporary layer is also added while starting the container, and it will be discarded if it is not commited after the container is stopped and deleted. If it is committed, then it would create another layer.

**Repository**: Different versions of an image that can be managed by multiple tags, which are saved with different GUID. A repository is a collection of images tracked by GUIDs.

#### Adding nonroot user to administer Docker

- for ease of use, we can allow a nonroot user to administer Docker by adding it to docker group. This is not required when using Docker on mac or windows

```
sudo groupadd docker
sudo useradd dockertest #create user to whom you want to give permissions to administer docker
sudo usermod -aG docker dockertest
```

```
man docker
man docker ps
man docker-ps
```

#### Working with Docker containers

- Docker's primary objective is running containers. we'll see the different operations that can be performed with containers, such as starting, stopping, listing, deleting and so on.. This will help us to use docker for different usecases, such as testing, CICD, setting up PAAS, and so on..

let's verify the docker installation by running the following command : `docker version`

- we need image to get the container started. let's see how images are searched for on the docker registry
- Ensure that docker daemon is running on the host and can be connected through docker client
- `docker search [options] TERM` - Docker search command lets you search for an image on the docker registry.
- `docker search --help` to look at the help on the docker search

**Pulling an image**
```
docker image pull <options> name[:TAG|@DIGEST]
docker pull [options] NAME[:TAG|@DIGEST] //legacy commmand
docker pull ubuntu //To pull the image
```

- the `pull` command downloads all the layers from the docker registry that are required to create that image locally.
- Image tags group images of same type. `docker image pull centos:centos7`
- By default, the image with latest tag gets pulled. To pull all images corresponding to all tags, use the command `docker image pull --all-tags alpine`
- from docker 1.6, we can build and refer to images by a new content addressable identifier called `digest`. it is very useful feature when we wanted to work with specific images rather than tags. To pull an image with specific digest, we can use the following syntax `docker image pull <image>@sha256:<digest>` ex: `    $ docker image pull nginx@sha256:788fa27763db6d69ad3444e8ba72f947df9e7e163bad7c1f5614f8fd27a311c3 `
- once an image gets pulled, it resides on the local cache (storage) so subsequent pulls will be very fast. This feature plays a very important role in building docker layered images
- `docker image pull --help` to get help related to docker images

#### listing images
- we can list the images that are available on the system by running docker daemon. These images might have pulled from registry, imported through `docker image pull` command or created through a dockerfile
```
docker image ls
docker images
```
- The docker client talks to the docker engine and gets a list of images that are downloaded (pulled) to the docker host
- To get help with docker images `docker image ls --help`

#### starting containers
- once we have images, we can use them to start containers.
- To start container we can use any of the following commands
```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

- /*docker container run command is recommended over docker run because, in version 1.13 docker logically grouped container operations under the docker container management command and so docker run might be obsolete  in the future*\

- `$ docker container run -i -t --name mycontainer ubuntu /bin/bash`

```
The -interactive or -i option starts the container in interactive mode by keeping the STDIN open
The --tty or -t option allocates a pseudo-tty and attaches it to the standard input
```
- if the name is not specified then a random string will be assigned as the name
- if the image is not available locally,then it will be downloaded from the registry first and then run

- Under the hood docker will :
```
Merge all the layers that makeup that image using UnionFS
Allocate a unique ID to a container, which is refered as containerID
Allocate a filesystem and mount read/write layer for the container. Any changes on this layer will be temporary and will be discarded if they are not committed.
Allocate a bridge network interface
Assign a IP address to the container
Execute the process specified by the user
```
- Also with default docker configuration, it creates a directory (with container id inside /var/lib/docker/containers) which has the container specific information such as hostname, configurationdetails, logs and /etc/host )
- To exit from the container, press `ctrl+D` or type `exit` it is simmilar to exiting from the shell, but this will stop the container. Alternatively to detach from the container press `ctrl + P + Q`
- The detached container will detach itself from the terminal, give control back to docker host shell, and wait for the `docker container attach` command to attach back to the container.
- the `run` command creates and starts the container. The container can be started in the background, and then we can attach to it whenever needed. we need to use `-d` option  to start the container in the background `docker container run -d -i -t ubuntu /bin/bash`
- we can run a non-interactive process, and run it in the background to make a deamonized container, like the following
```
docker container run -d ubuntu \
      /bin/bash -c \
      "while [true]; do date; sleep 1; done"
```
- To remove the container after it exits, start the container with `--rm` option, `docker run --rm ubuntu date`
- `--read-only` option of the `run` command will mount the root filesystem in `read-only` mode
```
docker container run --read-only --rm \
   ubuntu touch file
touch: cannot touch 'file': Read-only file system
```
- A container can be refered to in three ways; by name, by its short container id, by its container id
- `docker container run --help` to look at the help option of docker run

#### listing containers

- to list containers `docker container ls <options>` or legacy way `docker ps <options>`
-  The docker daemon will look at the metadata associated with the containers and list them . By default command returns the following :
```
container id, the image from which it was created, the command that was run after starting the container, the details about when it was created, the current status, the ports that are exposed from the container, the name of the container

we can use --filter/f option to ps, to do filtering of containers
```

```
docker container ls -a # To list both running and stopped containers
docker container ls -aq # To return just the container IDs of all containers
docker container ls -l # To show last created container, including non-running containers
```
- To get help of the listing of container, we can use `docker container ls --help`

#### logs
- if the container emmits logs or output on `STDOUT`/`STDERR` then we can get them without logging into the container
- To get logs from the container, run the command `docker container logs <options> container` or `docker logs <options> container`
- Docker will look at the container's specific log file from `/var/lib/docker/containers/<container id> /<containerid>-json.log`
- with `-t` option, we can get the timestamp with each log line, and with `-f` we can get tail-like behavior
- To get help `docker container logs --help`

#### stopping a container
- To stop a container `docker container stop <options> container [container..]` or `docker stop [options] container [CONTAINER..]`
- To stop a container after waiting for sometime, use the `--time/t` option
- To stop all running containers, run the following command `docker stop $(docker ps -q)`
- To get help of stopping containers, `docker container stop --help`

#### Removing a container
- To remove container permanently we must have to stop the container or use the force option
- `docker container rm [options] CONTAINER [CONTAINER]` or `docker rm [options] container [container]`
- Here docker daemon will remove the read/write layer, which was created while starting the container
- `docker container rm --help` - To get help related to removing docker container

- we can remove all stopped containers with a single command. i.e; `docker container prune <options>`
- To get any help related to prune, `docker container prune --help`

- from docker 1.2, a policy-based restart capability was added to the docker engine to automate restarting containers. The feature is activated using `--restart` option of the `run` command and it supports container restart at docker host boot time, as well as when container failure occur
- syntax: `docker container run --restart=policy <options> image[:tag] [command] [arg...]`

- There are 3 restart policies to choose from :
```
no: This doesnot start the container if it dies
on-failure: this restart the container if it fails with a non-zero exit code
always: This will always restart the container without worrying about the return code
```
- we can also get an optional restart count with the `on-failure` policy as follows : `docker container run --restart=on-failure:3 -d -i -t ubuntu /bin/bash`
- To get help `docker container run --help`
- if restart policies doesnot meet your requirements, then use process managers such as `systemd`, supervisor, or upstart

- from  docker 1.2 onwards, we can give access to the host device to a container with the `--device` option.
- `docker container run --device=<host-device>: <container device mapping>:<permissions> [options] Image [command] [arg..]`
- from docker 1.3, we can inject a new process inside a running container `docker exec [options] container command [arg...]`

- while doing debugging, automation, and so on.. we will need the containers configuration details. Docker provides `container inspect` command to get those easily. `    $ docker container inspect [OPTIONS] CONTAINER [CONTAINER...]`
- with `-f | --format` option, we can use the Go (programming language) template to get this specific information. The following command will give us an IP address for the container `    $ docker container inspect \
         --format='{{.NetworkSettings.IPAddress}}'  $ID
    172.17.0.2`
- `docker container inspect --help` to get help

#### Labeling and filtering containers
- from docker 1.6, a feature has been added so that we can label containers and images through which we can attach arbitrary key-value pairs as metadata.
- These are like environmental variables, these are not available for applications running inside containers, but they are available to docker clients that manage the images and containers.
- labels attached to images also get applied to containers that are started using those images. we can also attach labels to containers while starting them.

- syntax for labeling `docker container run --label <key>:<value> <command>`
- all the labels attached to a container can be listed through the `docker container inspect` command.
- You can apply labels to a container from a file by using the --label-file option. The file should have a list of labels separated by a new EOL

#### Reaping a zombie inside a container
- On Linux (and all Unix-like) operating systems, when a process exits, all the resources associated with that process are released with the exception of its entry in the process table
-  This entry in the process table is kept until the parent process reads the entry to learn about the exit status of its child. This transient state of a process is called a `zombie`
- As soon as the parent process reads the entry, the zombie process is removed from the process table, and this is called reaping
-   If the parent process exits before the child process, the init process (PID 1) adopts the child process (PID 1) and it eventually reaps adopted child processes when they exit

`systemd` is a variant of the `init` system and is adopted by many linux distributions

- Docker is designed to run one process per container and usually the process running inside the containers doesn't create child processes. However, if the process inside the container creates child processes, then an init system is needed to reap zombie processes
- we can launch a container with `init` process using the `--init` option of the docker container run command `docker container run --int [options] image [command] [arg..]`

### working with docker images

- As we know, docker containers are created from docker images, according to your application requirements, you can choose to create complex services using docker images that are built and offered by the folks at docker or third parties.
- if the existing images don't fit your requirements, you can also extend the existing images or custom-build your own images.

  - There are couple of ways to create images. 1. Manually making changes inside a container and then committing the image and 2. using dockerfile
 
  - we can make new image from the running/stopped container using `docker container commit` command syntax for it is `docker container commit <options> container <repository:tag>`
  - `docker container diff <containername/id>` command lists all the changes to the contqiner filesystem from its image

```
A : This is for when a file/directory has been added
C: This is for when a file/directory has been modified
D: This is for when a file/directory has been deleted
```

- By default, a container gets paused while doing the commit, you can change its behavior by passing `--pause=false` to commit
- To get help related to it use, `docker container commit --help`

#### Creating an Account with Docker Hub

- Docker hub is a cloud-based public registry service to host both public and private images, share them , and collaborate with others. It has integration with github and bitbucket and can trigger automated builds
- To host your image in dockerhub, you need to create your dockerid. This gives you the ability to create any number of public repositories in dockerhub
- Dockerhub also gives one free private repository, A repository can hold different versions of an image
- To signup visit `https://hub.docker.com`

#### logging in and out of docker image registry
- in the world of containerization, oftentimes, you will be publishing your images for public /private consumption of sharing images using docker regitry.
- To push an image to public repository, you must log into a docker registry and you must be the owner of the repository.
- In the case of private repository, both pulling and pushing are allowed, but only once you logged in.

- By default, both `docker login` and `docker logout` comamnds assume `https://hub.docker.com/` as the default registry, but this can be changed.

```
docker login
docker info | egrep ^\(username\|Registry\)
docker login registry.gitlab.com
cat ~/.docker/config.json
docker logout
```

- By default, `docker login` command interactively prompts the user for their username and password, we can change this behaviour by supplying the username with `-u` or `--username` option and the password using `-p` or `--password`

- to get help of it we cqn use `docker login --help` or `docker logout --help`

  
#### push a docker image to a registry

```
$ docker image push [OPTIONS] NAME[:TAG]
$ docker push [OPTIONS] NAME[:TAG] 
```

- In order to push an image to a docker registry, we begin by tagging the image using `docker image tag` command with appropriate user or organization in the docker hub. `docker image tag <name of the image> <name of the user/organization/name of the image>`
- To push the image to the docker hub , use `docker image push <username/orgainzation name/name of the image>`
- once above two steps done, we can login to the docker hub and see the images in the dashboard

- let's say u want to push the image to a registry that is hosted locally. for this first need to tag the image with registry host's name or IP address with the port number on which registry is running, and then push the images.
- For example, let's say our registry is configured on shadowfax.example.com. To tag the image, we would use the following command:
```
    $ docker tag myapache2 \ 
       shadowfax.example.com:5000/cookbook/myapache2

       $ docker push shadowfax.example.com:5000/cookbook/myapache2   # to push an image
```

- To get help of push use, `docker push --help`

- `docker image history` command helps us to find all the layers in the image, its image ID, when it was created, how it was created, the size and nay additional commnets associated with that layer

`docker image history <options> image` ex: `docker image history myapache2`


- when we build a docker image, the docker engine preserves the build instruction in the image metadata. later `docker image history` command recursively gathers these build instruction from the prescribed image to the base image and present it in the nice format


#### Removing an image

-  the `docker image rm` command lets you remove images from the docker host. This command can remove one or more images and you can specify images using any one of the below
```
image short id
image long id
image digest
image name along with tag. If the tag is not specified then the latest tag is assumed by default
```

- if the image happens to have more than one tag associated with it, then those tags must be removed before removing the image. Alternatively, we can forcefully remove them using `-f` or `--force` options of the `docker image rm`

```
docker container stop $(docker container ls -q) # To stop all containers
docker container rm $(docker container ls -a -q)` # To delete all containers
docker image rm $(docker image ls -q)` # To delete all images
```

- To get help of it use `docker image rm --help`

- 
