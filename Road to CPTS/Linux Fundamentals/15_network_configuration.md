# NETWORK CONFIGURATION

## Editing Interfaces
`/etc/network/interfaces`
```
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```
To apply this setting, restart the `networking` service.

## Network Access Control
Key Netwrok Access Control (`NAC`) models include:

| Type                        | Description                                                                                                             |
|-----------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Discretionary Access Control (DAC) | This model allows the owner of the resource to set permissions for who can access it.                                 |
| Mandatory Access Control (MAC)     | Permissions are enforced by the operating system, not the owner of the resource, making it more secure but less flexible. |
| Role-Based Access Control (RBAC)   | Permissions are assigned based on roles within an organization, making it easier to manage user privileges.              |

Configuring Linux network devices for NAC involves setting up security policies like SElinux (Security-Enhanced Linux), AppArmore policies for application security, and using TCP wrappers to control acesss to services based on IP addresses.

#### Discretionary Access Control (DAC)
DAC is an access control system that enables users to manage access to their resources by granting resource owners the responsibility of controlling access permissions to their resources. Users and groups who own a specific resource can decide who can access their resources and what actions they are authorized to perform. 

#### Mandatory Access Control (MAC)
Defines rules that determine resources access based on the resource's security level and the user's security level or process requesting access. Each resource is assigned a security label that identifies its level, and each user or process is assigned a security clearance level. Access is only granted if the user or process' security level is equal to or greater than the security level of the resource. 

#### Role-Based Access Control (RBAC)
Assigns permissions to users based on their roles within an organization. Users are assigned roles based on their job responsibilities or other criteria, and each role is granted a set of permissions that determine the actions that they can perform. Compared to DAC, RBAC provides a more flexible and scalable approach. Each user is assigned one or more roles, and each role is assigned to a set of permissions. 

## Netstat
`netstat` is used to display active network connections and their associated ports. 
```
farelmusyaffa@htb[/htb]$ netstat -a

Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 localhost:5901          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:sunrpc          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:http            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
...SNIP...
```

## Security-Enhanced Linux
`SELinux` is a MAC system integrated into the Linux kernel. SELinux operates at a low level, and though it offers strong security, it can be complex to configure and manage due to its granular controls.

## AppArmor
`AppArmor` is a MAC system that controls access to system resources and applications, but it operates in a simpler, more user-friendly manner. AppArmor is implemented as a Linux Security Module (LSM) and uses application profiles to define what resources an application can access. AppArmor is easier to configure and is generally considered more straightforward for day-to-day use, but less granular than SELinux.


## TCP wrappers
TCP wrappers are a host based network access control tool that restricts access to network services based on the IP address of incoming connections. When a network request is made, TCP wrappers intercepts it, checking the request against a list of allowed or denied IP addresses. 
