# NETWORK SERVICES

## SSH
SSH is a network protocol that allows the secure transmission of data and commands over a network. The most commonly used SSH server is OpenSSH, which is a free and open-source implementation of SSH. 

#### Server Status
Check the server status by running `systemctl status ssh`.

#### Logging In
OpenSSH can be configured and customized by editing the file `/etc/ssh/sshd_config`, adjusting things such as the maximum number of concurrent connections, the use of passwords or keys for logins, host key checking, and more. 

For example, we can use SSH to securely log in to a remote system and execute commands or use tunneling and port forwarding to tunnel data over an encrypted connection to verify network settings and other system settings without the possibility of third parties intercepting the transmission of data and commands.

## NFS
Network File System (`NFS`) is a network protocol that allows us to store and manage files on remote systems as if they were stored on the local system. Administrators use NFS to store and manage files centrally to enable easy collaboration and management of data. For Linux, there are several NFS servers, such as NFS-UTILS, NFS-Ganesha, and OpenNFS.

#### Server Status
Check to see if NFS is running with `systemctl status nfs-kernel-server`. We can configure NFS via the configuration file `/etc/exports`. This file specifies which directories should be shared and the access rights for users and systems. It is also possible to configure settings such as the transfer speed and the use of encryption. 

| Permissions      | Description                                                                                                           |
|------------------|-----------------------------------------------------------------------------------------------------------------------|
| `rw`             | Gives users and systems read and write permissions to the shared directory.                                           |
| `ro`             | Gives users and systems read-only access to the shared directory.                                                     |
| `no_root_squash` | Prevents the root user on the client from being restricted to the rights of a normal user.                           |
| `root_squash`    | Restricts the rights of the root user on the client to the rights of a normal user.                                  |
| `sync`           | Synchronizes the transfer of data to ensure that changes are only transferred after they have been saved on the file system. |
| `async`          | Transfers data asynchronously, which makes the transfer faster, but may cause inconsistencies in the file system if changes have not been fully committed. |

#### Create NFS Share
```
cry0l1t3@htb:~$ mkdir nfs_sharing
cry0l1t3@htb:~$ echo '/home/cry0l1t3/nfs_sharing hostname(rw,sync,no_root_squash)' >> /etc/exports
cry0l1t3@htb:~$ cat /etc/exports | grep -v "#"

/home/cry0l1t3/nfs_sharing hostname(rw,sync,no_root_squash)
```
#### Mount NFS Share
```
cry0l1t3@htb:~$ mkdir ~/target_nfs
cry0l1t3@htb:~$ mount 10.129.12.17:/home/john/dev_scripts ~/target_nfs
cry0l1t3@htb:~$ tree ~/target_nfs

target_nfs/
├── css.css
├── html.html
├── javascript.js
├── php.php
└── xml.xml

0 directories, 5 files
```

## Web Server
A web server is software that delivers data, documents, applications, and various functions over the internet. It utilizes the Hypertext Transfer Protocol (`HTTP`) to transmit data to clients such as web browsers and to receive requests from these clients. The received data is then rendered as Hypertext Markup Language (`HTML`).

Among the most widely used web servers is Apache. For Apache2, the file `/etc/apache2/apache.conf` contains the global settings. 
```
<Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</directory>
```
This section specifies that the default `/var/www/html` folder is accessible, that users can use the `Indexes` and `FollowSymLinks` options, that changes to files in this directory can be overriden with `AllowOverride All`, and that `Require all granted` grants all users access to this direcctory.

`.htaccess` file can be used to customize individual settings at the directory level.


