# FILTER CONTENTS

## `sort`

Sorts the input alphanumerically

## `grep`

Search for specific results that match the defined pattern. 

Example:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep "/bin/bash"

root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
cry0l1t3:x:1001:1001::/home/cry0l1t3:/bin/bash
htb-student:x:1002:1002::/home/htb-student:/bin/bash
```

To exclude something with a pattern, use `-v` argument. For example, to exclude users who have disabled the standard shell with the name `/bin/false` or `/usr/bin/nologin`:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin"

root:x:0:0:root:/root:/bin/bash
sync:x:4:65534:sync:/bin:/bin/sync
postgres:x:111:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
user6:x:1000:1000:,,,:/home/user6:/bin/bash
```

## `cut`

To remove specific delimiters and show the words on a line in a specified position, `cut` can be used as such:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | cut -d":" -f1

root
sync
postgres
mrb3n
cry0l1t3
htb-student
```
`-d` sets the delimiter to the colon character `(:)`, and `-f1` defines that the first position is to be taken from the process.

## `tr`

To replace a character, `tr` can be used as such:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " "

root x 0 0 root /root /bin/bash
sync x 4 65534 sync /bin /bin/sync
postgres x 111 117 PostgreSQL administrator,,, /var/lib/postgresql /bin/bash
mrb3n x 1000 1000 mrb3n /home/mrb3n /bin/bash
cry0l1t3 x 1001 1001  /home/cry0l1t3 /bin/bash
htb-student x 1002 1002  /home/htb-student /bin/bash
```

## `column`

To display a result in tabular form, use `column` as such:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | column -t

root         x  0     0      root               /root        		 /bin/bash
sync         x  4     65534  sync               /bin         		 /bin/sync
postgres     x  111   117    PostgreSQL         administrator,,,    /var/lib/postgresql		/bin/bash
mrb3n        x  1000  1000   mrb3n              /home/mrb3n  	     /bin/bash
cry0l1t3     x  1001  1001   /home/cry0l1t3     /bin/bash
htb-student  x  1002  1002   /home/htb-student  /bin/bash
```

## `awk`

`awk` can be used to format an output. For example:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}'

root /bin/bash
sync /bin/sync
postgres /bin/bash
mrb3n /bin/bash
cry0l1t3 /bin/bash
htb-student /bin/bash
```
`$1` prints the first result, and `$NF` printes the last result of the line.
## `sed`

`sed` can be used to substitute text. `sed` looks for patterns that have been defined in the form of regex and replace them with another pattern. Example:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | sed 's/bin/HTB/g'

root /HTB/bash
sync /HTB/sync
postgres /HTB/bash
mrb3n /HTB/bash
cry0l1t3 /HTB/bash
htb-student /HTB/bash
```

The `s` character at the beginning stands for the substitute command. The string after that until `/` indicates the string that is going to be replaced, and the string after that until `/` indicates the string that replaces the original string. `g` tells `sed` to replace all matches.

## `wc`

Can be used to count lines as such:
```
farelmusyaffa@htb[/htb]$ cat /etc/passwd | grep -v "false\|nologin" | tr ":" " " | awk '{print $1, $NF}' | wc -l

6
```
