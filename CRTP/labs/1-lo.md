# LEARNING OBJECTIVE 1

1. Enumerate:

a. Users
```
Get-DomainUser | select samaccountname

 samaccountname
--------------
Administrator
Guest
krbtgt
sqladmin
websvc
srvadmin
appadmin
svcadmin
testda
mgmtadmin
ciadmin
sql1admin
studentadmin
devopsadmin
student201
student202
student203
student204
student205
student206
student207
student208
student209
student210
student211
student212
student213
student214
student215
student216
student217
student218
student219
student220
Control201user
Control202user
Control203user
Control204user
Control205user
Control206user
Control207user
Control208user
Control209user
Control210user
Control211user
Control212user
Control213user
Control214user
Control215user
Control216user
Control217user
Control218user
Control219user
Control220user
Support201user
Support202user
Support203user
Support204user
Support205user
Support206user
Support207user
Support208user
Support209user
Support210user
Support211user
Support212user
Support213user
Support214user
Support215user
Support216user
Support217user
Support218user
Support219user
Support220user
VPN201user
VPN202user
VPN203user
VPN204user
VPN205user
VPN206user
VPN207user
VPN208user
VPN209user
VPN210user
VPN211user
VPN212user
VPN213user
VPN214user
VPN215user
VPN216user
VPN217user
VPN218user
VPN219user
VPN220user
```
b. computers
```
Get-DomainComputer | select Name

name
----
DCORP-DC
DCORP-ADMINSRV
DCORP-APPSRV
DCORP-CI
DCORP-MGMT
DCORP-MSSQL
DCORP-SQL1
DCORP-STDADMIN
DCORP-STD201
DCORP-STD202
DCORP-STD203
DCORP-STD204
DCORP-STD205
DCORP-STD206
DCORP-STD207
DCORP-STD208
DCORP-STD209
DCORP-STD210
DCORP-STD211
DCORP-STD212
DCORP-STD213
DCORP-STD214
DCORP-STD215
DCORP-STD216
DCORP-STD217
DCORP-STD218
DCORP-STD219
DCORP-STD220
```
c. domain administrators
```
Get-DomainGroupMember -Identity "Domain Admins" -Recurse | select MemberName

MemberName
----------
svcadmin
Administrator
```
d. enterprise administrators
```
Get-DomainGroupMember -Domain moneycorp.local -Identity "Enterprise Admins" -Recurse | select MemberName

MemberName
----------
Administrator
```

2. bloodhound to identify shortest path to domain admins in the dollarcorp domain

`Run neo4j after privesc in LO 5`

```
neo4j.bat install-service
neo4j.bat start
```

Go to `localhost:7474` and reset password

Open Bloodhound from `C:\AD\Tools\BloodHound-win32-x64\BloodHound-win32-x64`

`NOTE: for some reason cant get any data :(`

3. find a file share where studentx has write permissions

`NOTE: PowerHuntShares and PowerView conflicts, dont run both at the same time`

`servers.txt`

```
DCORP-ADMINSRV
DCORP-APPSRV
DCORP-CI
DCORP-MGMT
DCORP-MSSQL
DCORP-SQL1
DCORP-STDADMIN
DCORP-STD2
DCORP-STUDENT214
```

```
PS C:\Users\student214> Invoke-HuntSMBShares -NoPing -OutputDirectory C:\AD\Tools\ -HostList C:\AD\Tools\servers.txt
 ===============================================================
 INVOKE-HUNTSMBSHARES
 ===============================================================
  This function automates the following tasks:

  o Determine current computer's domain
  o Enumerate domain computers
  o Check if computers respond to ping requests
  o Filter for computers that have TCP 445 open and accessible
  o Enumerate SMB shares
  o Enumerate SMB share permissions
  o Identify shares with potentially excessive privielges
  o Identify shares that provide read or write access
  o Identify shares thare are high risk
  o Identify common share owners, names, & directory listings
  o Generate last written & last accessed timelines
  o Generate html summary report and detailed csv files

  Note: This can take hours to run in large environments.
 ---------------------------------------------------------------
 |||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
 ---------------------------------------------------------------
 SHARE DISCOVERY
 ---------------------------------------------------------------
 [*][01/21/2026 22:13] Scan Start
 [*][01/21/2026 22:13] Output Directory: C:\AD\Tools\\SmbShareHunt-01212026221334
 [*][01/21/2026 22:13] Importing computer targets from C:\AD\Tools\servers.txt
 [*][01/21/2026 22:13] 9 systems will be targeted
 [*][01/21/2026 22:13] - Skipping ping scan.
 [*][01/21/2026 22:13] Checking if TCP Port 445 is open on 9 computers
 [*][01/21/2026 22:13] - 7 computers have TCP port 445 open.
 [*][01/21/2026 22:13] Getting a list of SMB shares from 7 computers
 [*][01/21/2026 22:13] - 23 SMB shares were found.
 [*][01/21/2026 22:13] Getting share permissions from 23 SMB shares
 [*][01/21/2026 22:13] - 35 share permissions were enumerated.
 [*][01/21/2026 22:13] Identifying potentially excessive share permissions
 [*][01/21/2026 22:13] - 14 potentially excessive privileges were found on 4 shares across 3 systems.
 [*][01/21/2026 22:13] Getting directory listings from 4 SMB shares
 [*][01/21/2026 22:13] - Targeting up to 3 nested directory levels
 [*][01/21/2026 22:13] - 15 files and folders were enumerated.
 [*][01/21/2026 22:13] Scan Complete
 ---------------------------------------------------------------
 SHARE ANALYSIS
 ---------------------------------------------------------------
 [*][01/21/2026 22:13] Analysis Start
 [*][01/21/2026 22:13] - 4 shares can be read across 3 systems.
 [*][01/21/2026 22:13] - 3 shares can be written to across 3 systems.
 [*][01/21/2026 22:13] - 4 shares are considered non-default across 3 systems.
 [*][01/21/2026 22:13] - 2 shares are considered high risk across 1 systems.
 [*][01/21/2026 22:13] - Identified top 200 owners of excessive shares.
 [*][01/21/2026 22:13] - Identified top 200 share groups.
 [*][01/21/2026 22:13] - Identified top 200 share names.
 [*][01/21/2026 22:13] - Identified shares created in last 90 days.
 [*][01/21/2026 22:13] - Identified shares accessed in last 90 days.
 [*][01/21/2026 22:13] - Identified shares modified in last 90 days.
 [*][01/21/2026 22:13] - Identified 3 subnets hosting shares configured with excessive privileges.
 [*][01/21/2026 22:13] Finding interesting files...
 [*][01/21/2026 22:13] Grabbing secrets for parsing...
select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-ADMINSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-ADMINSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-ADMINSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-CI}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-CI}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-CI}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-CI}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-ADMINSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-ADMINSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-STDADMIN}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-STDADMIN}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-STDADMIN}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-STDADMIN}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2265 char:92
+ ... ComputerName | select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-STDADMIN}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2712 char:93
+ ... getComputers | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-ADMINSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2712 char:93
+ ... getComputers | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-CI}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2712 char:93
+ ... getComputers | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-STDADMIN}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2812 char:89
+ ... arget445Comp | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-ADMINSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2812 char:89
+ ... arget445Comp | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-APPSRV}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2812 char:89
+ ... arget445Comp | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-CI}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2812 char:89
+ ... arget445Comp | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-MGMT}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2812 char:89
+ ... arget445Comp | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-MSSQL}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2812 char:89
+ ... arget445Comp | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-SQL1}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

Select : Property "OperatingSystem" cannot be found.
At C:\AD\Tools\PowerHuntShares.psm1:2812 char:89
+ ... arget445Comp | Select OperatingSystem -ExpandProperty OperatingSystem
+                    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (@{ComputerName=DCORP-STDADMIN}:PSObject) [Select-Object], PSArgumentException
    + FullyQualifiedErrorId : ExpandPropertyNotFound,Microsoft.PowerShell.Commands.SelectObjectCommand

 [*][01/21/2026 22:14] Creating ShareGraph nodes and edges...
 [*][01/21/2026 22:14] Analysis Complete
 ---------------------------------------------------------------
 SHARE REPORT SUMMARY
 ---------------------------------------------------------------
 [*][01/21/2026 22:14] Domain: SmbHunt
 [*][01/21/2026 22:14] Start time: 01/21/2026 22:13:34
 [*][01/21/2026 22:14] End time: 01/21/2026 22:14:01
 [*][01/21/2026 22:14] Run time: 00:00:27.1070331
 [*][01/21/2026 22:14]
 [*][01/21/2026 22:14] COMPUTER SUMMARY
 [*][01/21/2026 22:14] - 9 domain computers found.
 [*][01/21/2026 22:14] - 0 (0.00%) domain computers responded to ping. (No Ping)
 [*][01/21/2026 22:14] - 7 (77.78%) domain computers had TCP port 445 accessible.
 [*][01/21/2026 22:14] - 3 (33.33%) domain computers had shares that were non-default.
 [*][01/21/2026 22:14] - 3 (33.33%) domain computers had shares with potentially excessive privileges.
 [*][01/21/2026 22:14] - 3 (33.33%) domain computers had shares that allowed READ access.
 [*][01/21/2026 22:14] - 3 (33.33%) domain computers had shares that allowed WRITE access.
 [*][01/21/2026 22:14] - 1 (11.11%) domain computers had shares that are HIGH RISK.
 [*][01/21/2026 22:14]
 [*][01/21/2026 22:14] SHARE SUMMARY
 [*][01/21/2026 22:14] - 23 shares were found. We expect a minimum of 14 shares
 [*][01/21/2026 22:14]   because 7 systems had open ports and there are typically two default shares.
 [*][01/21/2026 22:14] - 4 (17.39%) shares across 3 systems were non-default.
 [*][01/21/2026 22:14] - 4 (17.39%) shares across 3 systems are configured with 14 potentially excessive ACLs.
 [*][01/21/2026 22:14] - 4 (17.39%) shares across 3 systems allowed READ access.
 [*][01/21/2026 22:14] - 3 (13.04%) shares across 3 systems allowed WRITE access.
 [*][01/21/2026 22:14] - 2 (8.70%) shares across 1 systems are considered HIGH RISK.
 [*][01/21/2026 22:14]
 [*][01/21/2026 22:14] SHARE ACL SUMMARY
 [*][01/21/2026 22:14] - 35 ACLs were found.
 [*][01/21/2026 22:14] - 35 (100.00%) ACLs were associated with non-default shares.
 [*][01/21/2026 22:14] - 14 (40.00%) ACLs were found to be potentially excessive.
 [*][01/21/2026 22:14] - 8 (22.86%) ACLs were found that allowed READ access.
 [*][01/21/2026 22:14] - 3 (8.57%) ACLs were found that allowed WRITE access.
 [*][01/21/2026 22:14] - 5 (14.29%) ACLs were found that are associated with HIGH RISK share names.
 [*][01/21/2026 22:14]
 [*][01/21/2026 22:14] - The most common share names are:
 [*][01/21/2026 22:14] - 4 of 4 (100.00%) discovered shares are associated with the top 200 share names.
 [*][01/21/2026 22:14]   - 1 ADMIN$
 [*][01/21/2026 22:14]   - 1 std100-gp
 [*][01/21/2026 22:14]   - 1 C$
 [*][01/21/2026 22:14]   - 1 AI
 [*] -----------------------------------------------
 [*][01/21/2026 22:14]   - Generating HTML Report
 [*][01/21/2026 22:14]   - Estimated generation time: 1 minute or less
 [*][01/21/2026 22:14]   - All files written to C:\AD\Tools\\SmbShareHunt-01212026221334
 [*][01/21/2026 22:14]   - Done.
```
