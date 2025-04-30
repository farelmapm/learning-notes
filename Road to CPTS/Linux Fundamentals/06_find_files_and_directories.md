# FIND FILES AND DIRECTORIES

## `which`

Returns the path to the file or link that should be executed. 

Usage:
```
farel@MBAM3FMAPM> which python3
/opt/homebrew/bin/python3
```


## `find`
Find files and folders, filter the results by using filter parameters for size or dates. 

Example of usage:
```
farelmusyaffa@htb[/htb]$ find / -type f -name "*.conf" -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 2>/dev/null

-rw-r--r-- 1 root root 136392 Apr 25 20:29 /usr/src/linux-headers-5.5.0-1parrot1-amd64/include/config/auto.conf
-rw-r--r-- 1 root root 82290 Apr 25 20:29 /usr/src/linux-headers-5.5.0-1parrot1-amd64/include/config/tristate.conf
-rw-r--r-- 1 root root 95813 May  7 14:33 /usr/share/metasploit-framework/data/jtr/repeats32.conf
-rw-r--r-- 1 root root 60346 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dynamic.conf
-rw-r--r-- 1 root root 96249 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dumb32.conf
-rw-r--r-- 1 root root 54755 May  7 14:33 /usr/share/metasploit-framework/data/jtr/repeats16.conf
-rw-r--r-- 1 root root 22635 May  7 14:33 /usr/share/metasploit-framework/data/jtr/korelogic.conf
-rwxr-xr-x 1 root root 108534 May  7 14:33 /usr/share/metasploit-framework/data/jtr/john.conf
-rw-r--r-- 1 root root 55285 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dumb16.conf
-rw-r--r-- 1 root root 21254 May  2 11:59 /usr/share/doc/sqlmap/examples/sqlmap.conf
-rw-r--r-- 1 root root 25086 Mar  4 22:04 /etc/dnsmasq.conf
-rw-r--r-- 1 root root 21254 May  2 11:59 /etc/sqlmap/sqlmap.conf
```
- `-type f`   
Defines the type of the search object. In this case, `f` stands for *file*.

| Type | Description                        |
|------|------------------------------------|
| `b`  | Block special file                 |
| `c`  | Character special file             |
| `d`  | Directory                          |
| `p`  | Named pipe (FIFO)                  |
| `f`  | Regular file                       |
| `l`  | Symbolic link                      |
| `s`  | Socket                             |
| `D`  | Door (only on some Unix systems, e.g., Solaris) |

- `name *.conf` <br>
Defines the name to search, in this case search for all files that has the `.conf` extension. Uses **glob-style pattern matching**. `-iname` is also valid for case insensitive matches. Examples:
```
find . -name "*.txt"         # matches all .txt files (e.g., file.txt, notes.txt)
find . -name "file?.txt"     # matches file1.txt, fileA.txt but not file10.txt
find . -name "[a-z]*.sh"     # matches files starting with lowercase letters and ending in .sh
```
for ***regex*** matching, use `-regex` or `-iregex`.

- `-user root` <br>
Search for files that is owned by the root user.

- `-size +20k` <br>
Filter and only show files with size over 20k. Other examples:
```
find . -size +100k       # files larger than 100 KB
find . -size -1M         # files smaller than 1 MB
find . -size 4c          # files exactly 4 bytes in size
find . -size +2G         # files larger than 2 GB
```

- `-newermt 2020-03-03` <br>
Only new files modified newer than 2020-03-03 will be presented.

| Argument        | Description                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| `-newermt`      | File was modified **more recently than** the given **date string**          |
| `-newerat`      | File was **accessed** more recently than the given date string              |
| `-newerct`      | File’s **status was changed** more recently (ctime) than the date           |
| `-newerXt`      | Generic form: replace `X` with `a` (access), `c` (change), or `m` (modify)  |
| `-newer`        | File was modified more recently than another **file’s mtime**               |
| `-anewer`       | File was accessed more recently than another **file’s atime**               |
| `-cnewer`       | File’s ctime is more recent than another file’s **ctime**                   |

Examples of valid date formats:
```
2023-04-01
2023-04-01 10:00:00
yesterday, today, tomorrow
2023-04-01 +0200 (with timezone offset)
```

To find files ***older*** than a specific date, use `!` to negate the condition. Example:
```
find . ! -newer /tmp/ref     # Modified on or before /tmp/ref file
```

To find files ***exactly*** at a timestamp, use something like:
```
find . -newermt "2023-04-01 00:00:00" ! -newermt "2023-04-01 00:01:00"
```

- `-exec ls -al {} \;` <br>
Executes the specified command on the results, using the curly brackets as placeholders for each result. The backslash escapes the next character from being interpreted by the shell because otherwise, the semicolon would terminate the command and not reach the redirection.

- `2>/dev/null` <br>
`STDERR` redirection to the null device, basically meaning that every error received when running the command is silenced by redirecting the output on `STDERR` to the null device.

## `locate`
`locate` is similar to `find`, but `locate` uses a database to search. As such, searching with `locate` is faster than `find`, but the database needs to be updated by using `updatedb`. 
