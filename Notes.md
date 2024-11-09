

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
