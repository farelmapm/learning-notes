# LEARNING OBJECTIVE 3

1. Enumerate all OU

```
Get-DomainOU | select name

name
----
Domain Controllers
StudentMachines
Applocked
Servers
DevOps
```

2. Enumerate all computers in DevOps OU

```
(Get-DomainOU -Identity DevOps).distinguishedname | %{Get-DomainComputer -SearchBase $_} | select name

name
----
DCORP-CI
```

3. List all GPOs

```
Get-DomainGPO | select displayname

displayname
-----------
Default Domain Policy
Default Domain Controllers Policy
Applocker
Servers
Students
DevOps Policy
```

4. Enumerate the GPO applied in the DevOps OU

```
PS C:\Users\student214> Get-DomainOU | select name,gplink

name               gplink
----               ------
Domain Controllers [LDAP://CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=dollarcorp,DC=moneycorp,DC=local;0]
StudentMachines    [LDAP://cn={7478F170-6A0C-490C-B355-9E4618BC785D},cn=policies,cn=system,DC=dollarcorp,DC=moneycorp,DC=local;0]
Applocked          [LDAP://cn={0D1CC23D-1F20-4EEE-AF64-D99597AE2A6E},cn=policies,cn=system,DC=dollarcorp,DC=moneycorp,DC=local;0]
Servers            [LDAP://cn={308279C1-FFB6-4D52-948C-660B07AC77FB},cn=policies,cn=system,DC=dollarcorp,DC=moneycorp,DC=local;0]
DevOps             [LDAP://cn={0BF8D01C-1F62-4BDC-958C-57140B67D147},cn=policies,cn=system,DC=dollarcorp,DC=moneycorp,DC=local;0]


PS C:\Users\student214> Get-DomainGPO -Identity "{0BF8D01C-1F62-4BDC-958C-57140B67D147}"


flags                    : 0
displayname              : DevOps Policy
gpcmachineextensionnames : [{35378EAC-683F-11D2-A89A-00C04FBBCFA2}{D02B1F72-3407-48AE-BA88-E8213C6761F1}][{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}]
whenchanged              : 12/24/2024 7:09:01 AM
versionnumber            : 3
name                     : {0BF8D01C-1F62-4BDC-958C-57140B67D147}
cn                       : {0BF8D01C-1F62-4BDC-958C-57140B67D147}
usnchanged               : 296496
dscorepropagationdata    : {12/18/2024 7:31:56 AM, 1/1/1601 12:00:00 AM}
objectguid               : fc0df125-5e26-4794-93c7-e60c6eecb75f
gpcfilesyspath           : \\dollarcorp.moneycorp.local\SysVol\dollarcorp.moneycorp.local\Policies\{0BF8D01C-1F62-4BDC-958C-57140B67D147}
distinguishedname        : CN={0BF8D01C-1F62-4BDC-958C-57140B67D147},CN=Policies,CN=System,DC=dollarcorp,DC=moneycorp,DC=local
whencreated              : 12/18/2024 7:31:22 AM
showinadvancedviewonly   : True
usncreated               : 293100
gpcfunctionalityversion  : 2
instancetype             : 4
objectclass              : {top, container, groupPolicyContainer}
objectcategory           : CN=Group-Policy-Container,CN=Schema,CN=Configuration,DC=moneycorp,DC=local
```

Alternate:
`Get-DomainGPO -Identity (Get-DomainOU -Identity DevOps).gplink.substring(11,(Get-DomainOU -Identity DevOps).gplink.length-72)`

5. Enumerate ACLs for the Applocker and DevOps GPOs

Official guide leads to Bloodhound CE and use Inbound object control for Applocker and DevOps

Conclusion: 
- RDPUsers group have GenericAll over Applocker policy
- devopsadmin has WriteDACL on DevOps Policy

`Get-DomainGPO` to get CNs 

```
Get-DomainObjectAcl -Identity "{0BF8D01C-1F62-4BDC-958C-57140B67D147}"
```
```
Get-DomainObjectAcl -Identity "{0D1CC23D-1F20-4EEE-AF64-D99597AE2A6E}"
```
