# Linux Security

If firewall rules are not set at the network level, Linux firewall and/or `iptables` can be used to restrict traffic into/out of the host. 

If SSH is open on the server:
- Disallow password login
- Disallow root user from logging in via SSH
- Avoid logging into and administering the system as the root user whenever possible
- Adequately managing access control
- Configure `sudoers`
- Utilize `fail2ban`, which coutns the number of failed login attempts

It is also important to periodically audit the system for <b> out of date kernels, user permission issues, world-writable files, and misconfigured cron jobs, or misconfigured services. </b>. 

Some security settings should be made, such as:
- Removing or disabling all unnecessary services and software
- Removing all services that rely on unencrypted authentication mechanisms
- Ensure NTP is enabled and Syslog is running
- Ensure that each user has its own account
- Enforce the use of strong passwords
- Set up password aging and restrict the use of previous passwords
- Locking user accounts after login failures
- Disable all unwanted SUID/SGID binaries

## TCP Wrappers
TCP wrappers use the following configuration files:
- `/etc/hosts.allow` <br>
```
farelmusyaffa@htb[/htb]$ cat /etc/hosts.allow

# Allow access to SSH from the local network
sshd : 10.129.14.0/24

# Allow access to FTP from a specific host
ftpd : 10.129.14.10

# Allow access to Telnet from any host in the inlanefreight.local domain
telnetd : .inlanefreight.local
```
- `/etc/hosts.deny`
```
farelmusyaffa@htb[/htb]$ cat /etc/hosts.deny

# Deny access to all services from any host in the inlanefreight.com domain
ALL : .inlanefreight.com

# Deny access to SSH from a specific host
sshd : 10.129.22.22

# Deny access to FTP from hosts with IP addresses in the range of 10.129.22.0 to 10.129.22.255
ftpd : 10.129.22.0/24
```
Orders of the rules in the files is important. The first rule that matches the requested service and host is the one that will be applied. 

