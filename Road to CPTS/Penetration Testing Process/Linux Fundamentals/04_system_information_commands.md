# SYSTEM INFORMATION

- `whoami`   
Displays current username
- `id`   
Returns user uid, gid, etc. This can be of interest when pentesting to see what access a user have. Look for non-standard groups. `adm` group means that the user can read log files in `/var/log/`, membership in `sudo` group means user can run some or all commands with sudo as root. 
- `hostname`   
Prints the hostname of the current system
- `uname`   
Prints basic information about the system. `-a` flag gives full information about kernel name, hostname, kernel release, kernel version, machine hardware name, and operating system. `-r` gives information about kernel release
- `pwd`   
Prints full path of current directory
- `ifconfig`   
View network configuration like IP address, etc
- `ip`   
Show or manipulate routing, network devices, interfaces and tunnels
- `netstat`   
Shows network status
- `ss`   
Investigate sockets
- `ps`   
Show process status
- `who`   
Display who is logged in
- `env`   
Prints environment or sets and executes command
- `lsblk`   
List block devices
- `lsusb`   
List USB devices
- `lsof`   
List opened files
- `lspci`   
List PCI devices
