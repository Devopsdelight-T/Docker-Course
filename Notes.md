

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

#### Exporting an image
- Let's say you have a customer who has very strict policies that do not allow them to use images from the public domain. In such cases, you can share one or more images through tarballs, which can then later be imported on another system.

- the `docker image save` command lets you save or export images as tarballs. The command syntax as follows `docker image save [-0|--output]=file.tar IMAGE [IMAGE...]` ex: `docker image save --output=myapache2.tar myapache2`
- we can also export the container's filesystem using the command `docker container export --output=myapache2_cont.tar <containerid>`
To get help related to it, use `docker image save --help` or `docker container export --help`

#### Importing an image

- To get a local copy of the image, we either need to pull it from the accessible registry or import it from the already exported image. syntax is `docker image import <options> file|url|-<repository:tag>`
- `docker image import myapache2.tar apache2:imported`- Here, we have imported our `myapache2.tar` file as an `apache2:imported` image
- we can also import TAR files stored in remote location by specifying their URL
- To get help related to importing, we can use `docker image import --help`

### BUilding an image using a Dockerfile

- The `Dockerfile` is a text-based build instruction file that enables us to define the content of the docker image and automate image creation. The docker build engine reads the instrution in the `dokerfile` line by line and constructs the image as prescribed.
- The images created using `dockerfiles` are immutable

- first let me create an empty directory. `mkdir sample_image, cd sample_image` and then create a `Dockerfile` with the following content

```
# use ubuntu as the base image
FROM ubuntu

# Add author's name
LABEL maintainer="Devopsdelight"

# Add the command to run at the start of the container

CMD date

```
- Now run the following command inside the directory where we created `dockerfile` to build the image `docker image build .`
- To create our own repository name using the `-t` option of the `docker image build` command, as follows `docker image build -t sample .`
- Docker re-uses the intermediate layers from the image that was build. i.e; it uses cache.
- if we don't want the build system to reuse the intermediate layers, then add `--no-cache` option to the build

- if there is a file named `.dockerignore` in the current working directory with the list of files and directories (new-line separated), then those files and directories will be ignored in the build context

- The format of Dockerfile is `INSTRUCTION arguments`. Generally instructions are given in uppercase, but they are not case-sensitive. They are evaluated in order.
- `#` code phrase in the beginning is treated like a comment
**Different types of instructions**
- `FROM:` This must be first instruction of any Dockerfile, and sets the base image for subsequent instructions. `FROM <image>` or `FROM <images>:<tag>`
- There can be more than one `FROM` instruction in one Dockerfile to create multiple images
-  if we want to use private or third-party images, then we have to include them as follows `[registry_hostname[:port]/][user_name/](repository_name:version_tag) ` ex: `FROM registry-host:5000/cookbook/apache2
        FROM  <images>:<tag> AS <build stage> `
   
- `RUN :` we can execute the `RUN` instruction in two ways. First, we can run it in the shell ( shell -c): `RUN <command> <param1>...<paramN>` second, we can directly run as executable: `RUN ["executable", "param1",.."paramN" ]
- `LABEL:` From Docker 1.6, we have a new feature to the attached arbitrary key-value pair of Docker images and containers.To give a label to an image, we use `LABEL` instruction in Dockerfile -for ex: `LABEL distro=ubuntu`
- `CMD:` The `CMD` instruction provides a default executable while starting a container. if the `CMD` instruction doesnot have any executable (param2) then it will provide argument to `ENTRYPOINT`

```
CMD  ["executable", "param1",...,"paramN" ]
CMD ["param1", ... , "paramN"]
CMD <command> <param1> ... <pamamN>  
```
- only one `CMD` instruction is allowed in Dockerfile. If more than one is specified, then only the last one will be honored.
- `ENTRYPOINT:` This help us configure the container as an executable. Simmilar to `CMD`, there can be maximum of one instruction for `ENTRYPOINT`; if more than one is specified, then only the last one will be honored.

`ENTRYPOINT  ["executable", "param1",...,"paramN" ]
        ENTRYPOINT <command> <param1> ... <pamamN>`

- once parameters are defined with the `ENTRYPOINT` instruction, they cannot be overwritten at runtime. However, `ENTRYPOINT` can be used as `CMD`, if we want to use different parameters for `ENTRYPOINT`

- `EXPOSE:` This exposes network ports on the container, on which it will listen at runtime: `EXPOSE <ports> [<port> ..]`

- `ENV:` This will set the environment variable `<key>` to `<value`. It will be passed to all the subsequent instructions and will persist when a container is run from the resulting image: `ENV <key> <value>`
- `ADD:` This copies files from the source to the destination. `ADD <src> <dest>`
- if path containing white spaces then we can use `ADD ["<src>"... "dest"]`

- `COPY:` This is also simmilar to `ADD`. The difference between `ADD` and `COPY` is that `ADD` will extract URLs from src to dest but `COPY` can't able to do it. `COPY` can create more layers but `ADD` can't able to create that many layers when compared to `COPY`
- `COPY` Instruction optionally supports a `--from` option for multistage building.
- `VOLUME:` This instruction will create a mount point with the given name and flag it as mounting then external volume using the following syntax: `VOLUME ["/data"]` alternatively, we can use the code `VOLUME /data`
- `USER:` This sets the username for any of the following run instruction using the following syntax: `USER <username>/<UID>`
- `WORKDIR:` This sets the working directory for any `RUN`, `CMD` and `ENTRYPOINT` instructions that follow it. It can hav e multiple entries in the same Dockerfile. A relative path can be given that will be relative to the earlier `WORKDIR` instruction using the following syntax: `WORKDIR <path>`

- `ONBUILD:` This adds trigger instruction to the image that will be executed later, when this image will be used as the base image for another image. This trigger will run as part of the `FROM` instruction in a downstream Dockerfile using the syntax: `ONBUILD [Instruction]`
- To get help on image build use `docker image build --help`

#### Setting up a private index/registry
- Earlier, we used Docker-Hosted registry to push and pull images. Now, let's see how to host a private registry in our infrastructure.
- First, let's launch a local registry on the container by using command `docker container run -d -p 5000:5000 --name registry registry:2`
- To push an image to the local registry, we need to prefix the repository name with the `localhost` hostname or the ip address `127.0.0.1` and the registry port `5000` using the docker image tag command `docker tag apache2 localhost:5000/apache2`
- Now, let's push it `docker image push localhost:5000/apache2`

#### Automated Builds with Github and Bitbucket

- Dockerhub allows us to create automated images from a Github or Bitbucket repository using its build clusters. The Github/Bitbucket repository should contain the Dockerfile and also the content that is to be copied/added inside the image.

```
1. Login to Dockerhub at https://hub.docker.com
2. Link our Github/Bitbucket account to Dockerhub at Dockerhub ui and going over to settings
3. Followup the wizard
```

- These automated build process works, when we select a Github repository for an automated build, github enables the docker service for that repository. We can confirm this by looking at the integrations & services section of the settings tab in the gihub repository
- Now, whenever we make changes to any sourcecode and commit to the Github Repository, the automated build gets triggered and builds the Docker image using Dockerfile that is reside int e github repository

#### Creating a minimal image using a scratch base image

```
1. clone the repository ` https://github.com/docker-cookbook/scratch.git`
2. Change directory `cd scratch`
3. Build a static executable demo from the demo.c file using gcc:7.2 runtime container
     $ docker container run --rm \ 
     -v ${PWD}:/src \
     -w /src \
     gcc:7.2 \
     gcc -static -o demo demo.c
4. verify binary `ls -lh demo`
5. Let's build and image from the scratch base image `docker image build -t scratch-demo .`
6. Finally, let's verify the image by spinning a container from the preceding image and checking the image size. `docker container run --rm scratch-demo`
7. let's check the history of the image by using `docker image history scratch-demo`
```

#### Building images in multiple stages
- From Docker version 17.05, we have a cool feature called `Multistage builds`
- Docker's multistage build enables us to orchestrate complex build stages in a single Dockerfile.
- In the Dockerfile, we can define one or more intermediate stages with the appropriate parent image and the build ecosystem to build the artifact.
- The Dockerfile provides the essential previously mentioned primitives in building the image using Dockerfile to copy the artifacts to subsequent stages and finally compose a Docker image with just enough runtime and artifacts
- Before begin this first clone the repo at `https://github.com/docker-cookbook/multistage.git`
```
1. change directory `cd multistage`
2. Build the image using Docker image `docker build -t multistage .`
3. spinup the container from the image `docker run --rm multistage`
```

#### Visualizing the image hierarchy

- Run to get visual form of image hierarchy  `$ docker run --rm \
                 -v /var/run/docker.sock:/var/run/docker.sock \
                 nate/dockviz \
                 images --dot  | dot -Tpng -o images-graph.png`
  
- Run to visualize the container dependencies: `    $ docker run --rm \
                 -v /var/run/docker.sock:/var/run/docker.sock \
                 nate/dockviz \
                 containers --dot  | dot -Tpng -o containers-graph.png
  `


### Networking and Data Management for containers
-  When Docker Daemon starts, it creates a virtual ethernet bridge with the name `docker0`. perhaps, we can get more details of it using `ip addr` command on the system that runs the docker daemon `ip addr show docker0`
- Docker randomly chooses an address and subnet from a private range defined in RFC 1918. Using this bridged interface, containers can communicate with each other and with the host system
- By Default, Everytime Docker starts a container, it creates a pair of virtual run on Ethernet interface and then performs the following with the pair:

```
- Ties one end of the `veth` pair to the `docker0` bridge interface in the dockerhost -let's call this end the host end
- Ties the other end of the `veth` pair to the newly created container as its `eth0` interface-let's call this end of the `veth` pair the container end
```
- let's use `--network=host` option of the `docker container run` command to connect to the docker host's network stack. Since, the `alpine` image is packed with the `brctl` utility command. we will choose to spin our container with `alpine` image and run the `brctl show` command to display the bridge details, `docker container run --rm --network=host alpine brctl show`
- All the host ends of the `veth` pairs are bound to the default docker bridge `docker0`
- Apart from setting up the `docker0` bridge, Docker also creates `iptables` NAT rules, so that all containers can talk to the external world by default, but the external world cannot talk to the containers. `sudo iptables -t nat -l -n`
- `POSTROUTING` rule enables the docker containers to connect to the external world. `docker run --rm alpine traceroute -m 3 -n 8.8.8.8`

#### Accessing containers from outside
- In the mircroservice architecture, multiple smaller services are used to provide a meaningful enterprise-grade application.
- By default, the docker container allows outgoing data traffic, but there is no path for external world to connect to the services running inside the container.
- Docker provides a solution to selectively enable the external world to communicate with the service running inside the container, using `--publish` options of the `docker container run` command.

```
--publish/-p  | publish a container's port to the host

--publish-all / -P | publish all exposed ports to random ports
```
- Both these options allow the external world to connect to the services running inside the containers through the ports of the Docker host
- To look at the port mapping between the container and the docker host port use `docker container port <containerid>`
- These whole things works, when a container is launched with `-p <host port>:<container port>`, the Docker Engine configures the `iptables` destination NAT rule.
- This destination NAT rule is responsible for forwarding all the packets it receives on the Docker host port to the container's port `sudo iptables -t nat -l -n`
- The typical port range is from `32768` to `61000`, which is defined in `/proc/sys/net/ipv4/ip_local_port_range`

#### Attaching containers to a host network
- first, let's run `docker container run -it --rm alpine sh`
- Now, let's launch an alpine container by attaching it to the Docker host's network stack by using `--net=host` as an argument and executing the `ipaddress` command. use the command `docker container run -it --rm --net=host alpine sh`
- Here, in Step 1, Docker created a network namespace for the container and assigned and IP address for the container
- In Step 2, Docker attached the container to the host network stack, hence the container has full access to the host's network stack

#### Launching containers with no network
- Docker supports 3 types of network (`bridge, hosts and none`), check it with `docker network ls` command
- `none` network mode comes in handy when you package any utility inside the container that doesn't need any network connectivity
- `docker container run --rm --net=none alpine ip address`
- when we launch a container with the network mode set to `none`. Docker only creates a `loopback` interface for that container. Since, there is no Ethernet interface defined for this container, the container is isolated from the network

#### Sharing IP address with other containers
![image](https://github.com/user-attachments/assets/8cd206d7-1d0c-47ac-ae3f-431f79f0aa41)

- In essence, The Docker Engine assigns an IP address for the IP container and then the IP address is inherited by the service1, service 2, and service 3 containers.
- let's launch a container in the background. `docker container run -itd --name=ipcontainer alpine`
- look at IP address `docker container exec ipcontainer ip addr`
- finally, launch a container by attaching its network to the `ipcontainer` by using `docker container run --rm --net container:ipcontainer alpine ip addr`
- when containers share the network namespace, the original container that is created the namespace must be in a running state until the other containers are running. If the original container is stopped before the other contianers, it will put the other containers in an unusable state.

#### Creating a user-defined bridge network
- The containers that are connected through the default bridge can communicate with each other using IP address, but not the container's name.
- The container Ip address is assigned during the container start time,and hence it does not work well for multicontainer orchestration. Docker made an attempt to address this issue by linking containers statistically using the `--link` option of the `docker container run` command.
- Unfortunately, the linked containers have a tightly coupled container life cycle, so a restart of a container might prevent/delay the complete solution, and it will also not be scalable
- Later in between version 1.9 and 1.12, docker introduced user-defined network that solves many of the multicontainer orchestration and communication
- The user-defined bridge network's functionality is very much simmilar to the default bridge with some additional features:

```
1. service discovery through an embedded DNS server, which is an excellent fit for multicontianer orchestration and communication
2. DNS-based load balancing, another cool feature that complements multicontainer orchestration and communication. This feature allows us to seamlessly and transparently scale cotnainers
3. Optionally, we can configure our own subnet to the bridge
4. Optionally, we can manually assign an IP address to the containers from the bridge's subnet
```

- let's create a new user-defined bridge network using `docker network create` command `docker network create docker-learning` . Here user-defined network is named `docker-learning`
- Inspect the user-defined bridge network `docker-learning` using `docker network inspect docker-learning` command
- To look at the interfaces, use `ip addr` command
- look at the `iptables` to understand the NATing behavior of the user-defined bridge. `sudo iptables -t nat -t -n`
- with `docker network create` command we can have dozens of options to customize network according to our business requirements. `docker network create <name of the network> --subnet <range of subnet>`
- To get help of it use, `docker network create --help`

  #### Discovering and load balancing containers
  ![image](https://github.com/user-attachments/assets/fa26c209-1e0f-40e8-8ba1-be24ae4ea272)
- Here, we will launch `container1` and `container2` like a service and use a transient container to demonstrate `service discovery through an embedded DNS server`, `DNS based load balancing` capabilities of the user-defined bridge network

- let's start by spinning two containers, `container1` and `container2`, by connecting them to teh user-defined bridge network `docker-learning` using the `docker container run` command `docker container run -itd --name container1 --network-alias netalias --net docker-learning alpine` and `docker container run -itd --name container2 --network-alias netalias --net docker-learning alpine`

- Here `--network-alias` option helps us to group multiple containers with a single alias name and load balance using the embedded DNS. The embedded DNS provides round-robin load balancing
- Look at the Ip address of the both containers using the `docker container inspect ` command. `docker container inspect --format `{{ .Networksettings.Networks.docker-learning.IPAddress }}` container1` and `docker container inspect --format `{{ .Networksettings.Networks.docker-learning.IPAddress }}` container2`
- Now let's use transient containers to understand the service discovery capability of user-defined networks. `docker container run --rm --net docker-learning alpine ping -c1 container1`
- Now the containers can communicate with each other by using the container's names. This service discovery capabilities simplifies the multicontainer orchestration
- let's gain more understanding of the DNS loadbalancing capability of the user-defined network by pinging the network alias `netalias` use `docker container run --rm --net docker-learning alping ping -c1 netalias` Here, the embedded DNS load balancer resolved the `netalias` using a round-robin algorithm
- When a container is connected to a user-defined network, the Docker Engine adds the container's name and its network alias (if present) to the user-defined network's DNS record. Then Docker shares these details to the other containers connected to the same user-defined network through the embedded DNS hosted at 127.0.0.11

- To get help of dig, use command `dig -h`

#### persisting data using volume
- As we are aware, the container's read=write layer is temporary. and is destroyed when the container is removed.
- There are use cases where the container wherein you will have to preserve the application data beyond the lifecycle of the container.
- The recommended way to persist the application data outside of the container's filesystem, using volumes or a bind mound.
- **Docker volumes** are a special directory in the docker host, and are created and managed by Docker itself. we can mount these volumes to out container and have the application store its data to the volume.
- The Docker volumes are either named or anonymous. Functionally, Both anonymous and named volumes both are same. However, the anonymous volumes name is randomly generated

- Create a named volume by using `docker volume create devopsdelightvol`
- List the volume using `docker volume ls`
- Launch an interactive container using `docker container run` command and `-v` option to mount the `devopsdelightvol` volume to the container. `docker container run -it --rm -v devopsdelight:/data alpine / *`
- Now, let's create a new file at `/data` directory of the container and write some text to it. `echo "This is a named volume demo" > /data/demo.txt`
- Exit from the container and remove the container using the `docker container rm` command
- Now let's launch a new container by mounting the `datavol` volume again and print the content of the `demo.txt`. `docker container run --rm \ -v datavol:/data ubuntu`
- Here default `docker volume create` command creates a directory for the volume under the `/var/lib/docker/volume` directory of the docker host. Docker internally creates one more directory named `-data` for each volume under the directory it created for that volume. We can confirm the directory path by running the `docker volume inspect` command.
- when we mount the volume to a container, docker internally bind mounts the `-data` directory of the volume to the container.
- Docker allows us to share the volume with more than one container.

- To get help, type `docker volume create --help` or `docker volume ls --help` or `docker volume inspect --help`

#### Sharing data between the host and the container
- Bind mount is a linux capability that is used to take an existing directory structure and map it to a different location
- when a container is launched with the `-v <hostpath>:<containerpath>` option, the docker engine bindmounts the host path into the container's filesystem on the specified container path.

```
- create a new directory.By using command `mkdir $HOME/data_share`
- create a new file in the above directory of the docker host and write some text in it. `$ echo "data sharing demo" > $HOME/data_share/demo.txt`
- Launch a container by mounting the above directory and print its content. `$ docker container run --rm \ 
            -v $(HOME)/data_share:/data \ 
            ubuntu cat /data/demo.txt 
data sharing demo`

```
- Above we mounted a directory to a container. Simmilarly, we can also mount individual files to the container.  `$ docker container run --rm \
          -v $(HOME)/data_share/demo.txt:/demo.txt \
          ubuntu cat /demo.txt 
data sharing demo`
- By default, when we mount a directory or a file into a container, it gets mounted in read-write mode. So that the container can alter the content of the mounted directory or file.
- we can prevent the containers from modifying the content of the mounted directory or file using `ro` flag.

```
$ touch file 
$ docker container run -rm \ 
                    -v ${PWD}/file:/file:rw \ 
                    ubuntu sh -c "echo rw mode >> /file" 
$ cat file
rw mode 
$ docker container run -rm \ 
                    -v ${PWD}/file:/file:rw \
                    ubuntu sh -c "echo ro >> /file" 
sh: 1: cannot create /file: Read-only file system
```

### Docker use cases

#### Testing with Docker
- create a `/tmp` directory and then clone sample python code here ` git clone https://github.com/pallets/flask`
- Create a `Dockerfile_2.7` file, as follows then build an image from it :
```
FROM python:2.7
RUN pip install flask pytest
ADD flask/ /flask
WORKDIR /flask/examples/tutorial
RUN pip install -e .
cmd ["/usr/local/bin/pytest"]
```

- To build `python2.7test` image, run the command `docker image build -t python2.7test -f /tmp/Dockerfile_2.7`
- Simmilarly, create a Dockerfile with `python:3.7` as base image and build the `python3.7test` image.
```
FROM python:3.7
RUN pip install flask pytest
ADD flask/ /flask
WORKDIR /flask/exmples/tutorial
RUN pip install -e .
CMD ["/usr/local/bin/pytest"]
```
- To build the `python3.7test` image, run the command `docker image build -t python3.7test -f /tmp/Dockerfile_3.7 .`
- Check the images using `docker image ls`
- `docker container run python2.7test` simmilarly, to test with 3.7, `docker container run python3.7test`

### Performing CI/CD with Shippable and Heroku
- 
