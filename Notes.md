

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



