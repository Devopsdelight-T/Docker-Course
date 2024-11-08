

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

- ![image](https://github.com/user-attachments/assets/40f36412-d5cf-4470-9425-f0d2a47919a4)

- 

