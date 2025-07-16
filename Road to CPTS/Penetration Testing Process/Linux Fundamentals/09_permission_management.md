# PERMISSION MANAGEMENT

## Changing permissions
Modify permissions using the `chmod` command, permission group references (`u` - owner, `g` - group, `o` - others, `a` - all users) and either a `+` or `-` to add or remove the designated permissions. For example:

```
cry0l1t3@htb[/htb]$ ls -l shell

-rwxr-x--x   1 cry0l1t3 htbteam 0 May  4 22:12 shell
```

```
cry0l1t3@htb[/htb]$ chmod a+r shell && ls -l shell

-rwxr-xr-x   1 cry0l1t3 htbteam 0 May  4 22:12 shell
```
Or, using the octal value:
```
cry0l1t3@htb[/htb]$ chmod 754 shell && ls -l shell

-rwxr-xr--   1 cry0l1t3 htbteam 0 May  4 22:12 shell
```
## Changing owner
Change owners using `chown` command like the following:
```
cry0l1t3@htb[/htb]$ chown root:root shell && ls -l shell

-rwxr-xr--   1 root root 0 May  4 22:12 shell
```

## SUID & SGID
Linux allows special permission configuration on files through the Set User ID (SUID) and Set Group ID (SGID) bits. These bits act like temporary access passes, enabling users to run certain programs with the privileges of another user or group. 

These permissions are indicated by an `s` in place of the usual `x` in the file's permission set. When a program with the SUID or SGID bit set is executed, the program runs with the permissions of the file's owner or group. 

## Sticky bits
Sticky bits are like locks on files within shared spaces. When set on a directory, the sticky bit adds an extra layer of security, ensuring that only certain individuals can modify or delete files, even if others have access to the directory. For example:
```
cry0l1t3@htb[/htb]$ ls -l

drw-rw-r-t 3 cry0l1t3 cry0l1t3   4096 Jan 12 12:30 scripts
drw-rw-r-T 3 cry0l1t3 cry0l1t3   4096 Jan 12 12:32 reports
```
If the sticky bit is capitalized (`T`), all other users do not have the execute (`x`) permissions, therefore not being able to see the contents of the folder nor run any programs in it. The lowercase sticky bit (`t`) is the sticky bit where the execute (`x`) permissions have been set. 
