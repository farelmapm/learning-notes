# System Logs

## Kernel Logs
`/var/log/kern.log` contains information about the system kernel, including hardware drivers, system calls, and kernel events.

## System logs
`/var/log/syslog` contains information about system-level events, such as service starts and stops, login attempts, and system reboots. 

## Authentication logs
`/var/log/auth.log` contains informatio about user authentication attempts, including successful and failed attempts.

## Application logs
Contains information about the activities of specific applications running on the system.

| Service     | Description                                                                                                                   |
|-------------|-------------------------------------------------------------------------------------------------------------------------------|
| Apache      | Access logs are stored in the `/var/log/apache2/access.log` file (or similar, depending on the distribution).                |
| Nginx       | Access logs are stored in the `/var/log/nginx/access.log` file (or similar).                                                  |
| OpenSSH     | Access logs are stored in the `/var/log/auth.log` file on Ubuntu and in `/var/log/secure` on CentOS/RHEL.                    |
| MySQL       | Access logs are stored in the `/var/log/mysql/mysql.log` file.                                                                |
| PostgreSQL  | Access logs are stored in the `/var/log/postgresql/postgresql-version-main.log` file.                                        |
| Systemd     | Access logs are stored in the `/var/log/journal/` directory.                                                                  |

## Security logs
Recorded in a variety of log files, depending on the specific security application in use. 
- `fail2ban` -> `/var/log/fail2ban.log`
- `ufw` -> `/var/log/ufw.log`
