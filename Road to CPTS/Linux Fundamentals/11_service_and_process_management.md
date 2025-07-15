# SERVICE AND PROCESS MANAGEMENT

Services run silently in the background without direct user interaction. Generally, services can be categorized into two types:

## System services
These are internal services required during system startup. 

## User-installed services
These services are added by users, typically include server applications and other background processes that provide specific features or capabilities. 

Daemons are often identified by the letter `d` at the end of their program names, such as `sshd` (SSH daemon) or `systemd`. In general, there are just a few goals users have when dealing with a service or process:

- Start/restart a service/process
- Stop a service/process
- See what is/was happening with a service/process
- Enable/disable a service/process on boot
- Find a service/process

Most modern Linux systems have adopted `systemd` as their init system. It is the first process that starts on boot. All processes are assigned a Process ID (`PID`) and can be viewed under the `/proc/` directory, which contains information about each process. Processes may also have a Parent Process ID (`PPID`), indicating that they were started by another process, making them a child process.

For example. to add `ssh` service and run it on a system, use:
```
farelmusyaffa@htb[/htb]$ systemctl enable ssh

Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh
```

To list all services:
```
farelmusyaffa@htb[/htb]$ systemctl list-units --type=service

UNIT                                                       LOAD   ACTIVE SUB     DESCRIPTION              
accounts-daemon.service                                    loaded active running Accounts Service         
acpid.service                                              loaded active running ACPI event daemon        
apache2.service                                            loaded active running The Apache HTTP Server   
apparmor.service                                           loaded active exited  AppArmor initialization  
apport.service                                             loaded active exited  LSB: automatic crash repor
avahi-daemon.service                                       loaded active running Avahi mDNS/DNS-SD Stack  
bolt.service                                               loaded active running Thunderbolt system service
```
Use `journalctl` to view logs:
```
farelmusyaffa@htb[/htb]$ journalctl -u ssh.service --no-pager

-- Logs begin at Wed 2020-05-13 17:30:52 CEST, end at Fri 2020-05-15 16:00:14 CEST. --
Mai 13 20:38:44 inlane systemd[1]: Starting OpenBSD Secure Shell server...
Mai 13 20:38:44 inlane sshd[2722]: Server listening on 0.0.0.0 port 22.
Mai 13 20:38:44 inlane sshd[2722]: Server listening on :: port 22.
Mai 13 20:38:44 inlane systemd[1]: Started OpenBSD Secure Shell server.
Mai 13 20:39:06 inlane sshd[3939]: Connection closed by 10.22.2.1 port 36444 [preauth]
Mai 13 20:39:27 inlane sshd[3942]: Accepted password for master from 10.22.2.1 port 36452 ssh2
Mai 13 20:39:27 inlane sshd[3942]: pam_unix(sshd:session): session opened for user master by (uid=0)
Mai 13 20:39:28 inlane sshd[3942]: pam_unix(sshd:session): session closed for user master
Mai 14 02:04:49 inlane sshd[2722]: Received signal 15; terminating.
Mai 14 02:04:49 inlane systemd[1]: Stopping OpenBSD Secure Shell server...
Mai 14 02:04:49 inlane systemd[1]: Stopped OpenBSD Secure Shell server.
-- Reboot --
```

## Kill a process
A process can be in the following states:
- Running
- Waiting (waiting for an event or system resource)
- Stopped
- Zombie (stopped but still has an entry in the process table)

Processes can be controlled using `kill`, `pkill`, `pgrep`, and `killall`. 

To interact with a process, we can send signals to it. List of signals:
```
farelmusyaffa@htb[/htb]$ kill -l

 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```

Most commonly signals:
| Signal | Name     | Description                                                                                      |
|--------|----------|--------------------------------------------------------------------------------------------------|
| 1      | SIGHUP   | Sent to a process when the terminal that controls it is closed.                                  |
| 2      | SIGINT   | Sent when a user presses [Ctrl] + C in the controlling terminal to interrupt a process.          |
| 3      | SIGQUIT  | Sent when a user presses [Ctrl] + D to quit.                                                     |
| 9      | SIGKILL  | Immediately kill a process with no clean-up operations.                                          |
| 15     | SIGTERM  | Program termination.                                                                             |
| 19     | SIGSTOP  | Stop the program. It cannot be handled anymore.                                                  |
| 20     | SIGTSTP  | Sent when a user presses [Ctrl] + Z to suspend a process; it can be resumed or handled later.    |

For example, force killing a process:
```
farelmusyaffa@htb[/htb]$ kill 9 <PID>
```

## Background a process
To send a process to the backgroun, first use `CTRL + Z` to send `SIGTSTP` signal to the kernel, which suspends the current process. 
```
 farel@musyaffa  ~  ping 8.8.8.8      
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=56.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=48.1 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=115 time=27.7 ms
^Z
[1]  + 187283 suspended  ping 8.8.8.8
 ✘ farel@musyaffa  ~  jobs                                       
[1]  + suspended  ping 8.8.8.8
 farel@musyaffa  ~ 
```
To keep it running in the background, enter the command `bg` to put the process in the background.

## Foreground a process
To send a background process into the foreground, use `fg <ID>` command by first obtaining the ID using `jobs`.
```
farelmusyaffa@htb[/htb]$ jobs

[1]+  Running                 ping -c 10 www.hackthebox.eu &
```
```
farelmusyaffa@htb[/htb]$ fg 1
ping -c 10 www.hackthebox.eu

--- www.hackthebox.eu ping statistics ---
10 packets transmitted, 0 received, 100% packet loss, time 9206ms
```
