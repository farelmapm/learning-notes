# Containerization
Containerization is the process of packaging and running applications in isolated environments. 

## Linux Containers (LXC)
LXC is a lightweight virtualization technology that allows multiple isolated Linux systems to run on a single host. LXC uses key resource isolation features, such as control groups (`cgroups`) and `namespaces` to ensure that each container operates independently. 

## LXC vs Docker

| Category        | Description                                                                                                                                                                                                                                                                                                                                                   |
|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Approach        | LXC is often seen as a more traditional, system-level containerization tool, focusing on creating isolated Linux environments that behave like lightweight virtual machines. Docker, on the other hand, is application-focused, meaning it is optimized for packaging and deploying single applications or microservices.                                       |
| Image building  | Docker uses a standardized image format (Docker images) that includes everything needed to run an application (code, libraries, configurations). LXC, while capable of similar functionality, typically requires more manual setup for building and managing environments.                                                                                     |
| Portability     | Docker excels in portability. Its container images can be easily shared across different systems via Docker Hub or other registries. LXC environments are less portable in this sense, as they are more tightly integrated with the host system’s configuration.                                                                                               |
| Ease of use     | Docker is designed with simplicity in mind, offering a user-friendly CLI and extensive community support. LXC, while powerful, may require more in-depth knowledge of Linux system administration, making it less straightforward for beginners.                                                                                                                |
| Security        | Docker containers are generally more secure out of the box, thanks to additional isolation layers like AppArmor and SELinux, along with its read-only filesystem feature. LXC containers, while secure, may need additional configurations to match the level of isolation Docker offers by default. When misconfigured, both Docker and LXC can be vectors for local privilege escalation. |

In LXC, images are manually built by creating a root filesystem and installing necessary packages and configurations. These containers are tied to the host system, may not be easily portable, and may require more technical expertise to configure and manage. 

## Securing LXC
In order to configure `cgroups` for LXC and limit the CPU and memory, a container can create a new configuration file in the `/usr/share/lcx/config/<container name>.conf` file.
```
lxc.cgroup.cpu.shares = 512
lxc.cgroup.memory.limit_in_bytes = 512M
```
`lxc.cgroup.cpu.shares` determines the CPU time a container can use in relation to the other containers on the system. By default this value is set to 1024. <br>
`lxc.cgroup.memory.limit_in_bytes` sets the maximum amount of memory a container can use. <br>
To apply changes, LXC service must be restarted. 

LXC uses `namespaces` to provide an isolated environment for processes, networks, and file systems from the host system. 

`namespaces` are a feature of the Linux kernel that allows for creating isolated environments by providing an abstraction of system resources. Namespaces are a crucial aspect of containerization as they provide a high degree of isolation for the container's processes, network instances, routing tables, and firewall rules. Each container is allocated a unique `pid` number space, isolated from the host system's `pid`s. Each container has its own network interfaces (`net`), routing tables, and firewall rules. Containers also come with their own root file system. 
