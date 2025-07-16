# TASK SCHEDULING

## Systemd Timer

### Create a Timer
To create a timer, create a directory where the timer script will be stored. For example, create a directory in `/etc/systemd/system/mytimer.timer.d`. and create a script in `/etc/systemd/system/mytimer.timer`.

A systemd timer script must contain `[Unit]`. `[Timer]`, and `[Install]`.
- `[Unit]` <br>
Specified a description for the timer.
- `[Timer]` <br>
Specifies when to start the timer and when to activate it.
- `[Install]` <br>
Specifies where to install the timer.

For example:
```
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min                  #PICK ONE
OnUnitActiveSec=1hour           #PICK ONE

[Install]
WantedBy=timers.target
```

`OnBootSec` indicates that the script will be run after system boot only, meaning that with this setting, the script will only run once after 3 minutes after system boot. 

`OnUnitActiveSec` indicates that the script will run at the specified interval. So with this setting, the script will run every 1 hour.

### Create a Service
An example service is `/etc/systemd/system/mytimer.service`. Here, set a description and specify the full path to the script. 

```
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
```
`WantedBy=multi-user.target` is the unit system that is activated when starting a normal multi-user mode. 

### Reload Systemd
To apply this service, reload `systemd` by performing `sudo systemctl daemon-reload`. After that, start or enable the service by using `sudo systemctl start mytimer.service` or `sudo systemctl enable mytimer.service`

## Cron
To set up a task in the Cron daemon, we need to store the tasks in a file called `crontab`. The structure of Cron consists of the following components:
| Time Frame           | Description                                                   |
|----------------------|---------------------------------------------------------------|
| Minutes (0-59)       | This specifies in which minute the task should be executed.   |
| Hours (0-23)         | This specifies in which hour the task should be executed.     |
| Days of month (1-31) | This specifies on which day of the month the task should be executed. |
| Months (1-12)        | This specifies in which month the task should be executed.    |
| Days of the week (0-7)| This specifies on which day of the week the task should be executed. |

For example, a `crontab` looks looks like this:
```
# System Update
0 */6 * * * /path/to/update_software.sh

# Execute Scripts
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups
0 0 * * 7 /path/to/scripts/backup.sh
```
`System Update` will be executed once every 6 hours. <br>
`Execute Scripts` will be executed every first day of the month at midnight. <br>
`Cleanunp DB` will be executed every sunday at midnight.
`Backups` will also be executed every sunday at midnight.

## Systemd vs Cron
With Systemd, you need to create a timer and services script that telss the operating system when to run the tasks. 

With Cron, you need to create a `crontab` file that tells the Cron daemon when to run the tasks. 
