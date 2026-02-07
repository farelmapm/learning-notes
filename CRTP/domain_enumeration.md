# Domain Enumeration

## Tools
- ActiveDirectory PowerShell module
    - [Microsoft PowerShell Module](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2025-ps) 
    - [ADModule](https://github.com/samratashok/ADModule)
    ```
    Import-Module C:\AD\Tools\ADModule-master\Microsoft.ActiveDirectory.Management.dll
    Import-Module C:\AD\Tools\ADModule-master\ActiveDirectory\ActiveDirectory.ps1
    ```
- [BloodHound (Legacy)](https://github.com/SpecterOps/BloodHound-Legacy)
- [BloodHound (Community)](https://github.com/SpecterOps/BloodHound)
- [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)
- [SharpView](https://github.com/tevora-threat/SharpView)

## Commands
Run Invisi-Shell first to evade detection
- Running with admin privileges:
`RunWithPathAsAdmin.bat`
- Running with non-admin privileges:
`RunWithRegistryNonAdmin.bat`
### PowerView
- `. PowerView.ps1` -> Run PowerView
##### Domain General Information
- `Get-Domain` -> Get current domain
- `Get-DomainSID` -> Security Identifier. Each domain has an SID, can be used to forge tickets(?)
- `Get-Domain -Domain moneycorp.local` -> Get object of another domain
- `Get-DomainPolicyData` -> Get domain policy for current domain
- `(Get-DomainPolicyData -domain moneycorp.local).systemaccess` -> Get domain policy for another domain
- `Get-DomainController` -> Get domain controllers for the current domain
- `Get-DomainController -Domain moneycorp.local` -> Get domain controllers for another domain
##### Domain Users
- `Get-DomainUser` -> Get list of users in current domain
- `Get-DomainUser -Identity student1` -> Get user from current domain
- `Get-DomainUser -Identity student1 -Properties *` -> Get list of all properties for users in the current domain
- `Get-DomainUser -Properties samaccountname,logonCount` -> Get certain properties
- `Get-DomainUser | select samaccountname,logonCount` -> Get certain properties with piping in PowerShell <br>
`NOTE: Check logonCount. If logonCount is 0, DO NOT TRY TO ATTACK IT. ALWAYS TARGET ACTIVE USERS`
- `Get-DomainUser -LDAPFilter "Description=*built*" | Select name,Description` -> Search for a particular string in a user's attributes
##### Domain Computers
`NOTE: Domain users can add up to 10 computers (domain objects) to the domain`
- `Get-DomainComputer | select Name,dnshostname,logonCount` -> Get a list of computers in the current domain
- `Get-DomainComputer -OperatingSystem "*Server 2022*"`
- `Get-DomainComputer -Ping`
##### Domain Groups
`NOTE: Enterprise Admins is a group that exists in the forest root. It won't show up in a child domain.`
- `Get-DomainGroup | select Name`
- `Get-DomainGroup -Domain <targetdomain>`
- `Get-DomainGroup *admin*`
##### Domain Group Members
`NOTE: Built-in Administrator account can be identified by MemberSID ending with -500` <br>
`NOTE: User-created Administrator accounts can be identifed by MemberSID ending in the 1000s` <br>
`NOTE: Enterprise admin is only present in the forest root and has administrative access on all domain controllers. Domain admin does not have direct access to other domains. If a domain admin of a child domain is compromised, pivoting is possible to the parent domain/forest root, but no direct access.` <br>
`NOTE: If a domain controller is compromised, the forest root is compromised as well.` <br>
`NOTE: Stay away/Do not rush from domain admins, as it is well protected and monitored`
- `Get-DomainGroupMember -Identity "Domain Admins" -Recurse` -> Get all members of the Domain Admins group
- `Get-DomainGroup -UserName "student1"` -> Get the group membership for a user
##### Domain Local Groups
`NOTE: Domain local groups and local groups are different.`
- `Get-NetLocalGroup -ComputerName dcorp-dc` -> List domain local groups on a machine
- `Get-NetLocalGroupMember -ComputerName dcorp-dc -GroupName Administrators` -> Get members of the local group "Administrators" on a machine
- `Get-NetLoggedon -ComputerName dcorp-adminsrv` -> Get actively logged users on a computer
- `Get-LoggedonLocal -ComputerName dcorp-adminsrv` -> Get locally logged users on a computer
- `Get-LastLoggedOn -ComputerName dcorp-adminsrv` -> Get last logged user on a computer
##### Domain Shares and Files
- `Invoke-ShareFinder -Verbose` -> Find shares on hosts in current domain
- `Invoke-FileFinder -Verbose` -> Find sensitive files on computers in the domain
- `Get-NetFileServer` -> Get all fileservers of the domain

### ActiveDirectory Module
##### Domain General Information
- `Get-ADDomain` -> Get current domain
- `(Get-ADDomain).DomainSID` -> Get domain SID for the current domain
- `Get-ADDomain -Identity moneycorp.local` -> Get object of another domain
- `(Get-DomainPolicyData).systemaccess` -> Get domain policy for current domain
- `Get-ADDomainController` -> Get domain controllers for the current domain
- `Get-ADDomainController -DomainName moneycorp.local` -> Get domain controllers for another domain
##### Domain Users
- `Get-ADUser -Filter * -Properties *` -> Get list of users
- `Get-ADUser -Identity student1 -Properties *` -> Get certain user
- `Get-ADUser -Filter * -Properties * | select -First 1 | Get-Member -
MemberType *Property | select Name` -> Get list of all properties for users in the current domain
- `Get-ADUser -Filter * -Properties * | select name,logoncount,@{expression={[datetime]::fromFileTime($_.pwdlastset)}}` -> Get certain properties
- `Get-ADUser -Filter 'Description -like "*built*"' -Properties Description | select name,Description` -> Search for a particular string in a user's attributes
##### Domain Computers
- `Get-ADComputer -Filter * | select Name`
- `Get-ADComputer -Filter * -Properties *`
- `Get-ADComputer -Filter 'OperatingSystem -like "*Server 2022*"' -Properties OperatingSystem | select Name,OperatingSystem`
- `Get-ADComputer -Filter * -Properties DNSHostName | %{TestConnection -Count 1 -ComputerName $_.DNSHostName`
###### Domain Groups
- `Get-ADGroup -Filter * | select Name`
- `Get-ADGroup -Filter * -Properties *`
- `Get-ADGroup -Filter 'Name -like "*admin*"' | select Name`
###### Domain Group Members
- `Get-ADGroupMember -Identity "Domain Admins" -Recursive` -> Get all the members of the Domain Admins group
- `Get-ADPrincipalGroupMembership -Identity student1 `

### PowerHuntShares
For enumerating shares, [PowerHuntShares](https://github.com/NetSPI/PowerHuntShares) can also be used. Can discover shares, sensitive files, ACLs for shares, networks, computers, identities, etc. and generates a nice HTML report. <br>
`NOTE: Noisy and prone to detection`
- `Invoke-HuntSMBShares -NoPing -OutputDirectory C:\AD\Tools -HostList C:\AD\Tools\servers.txt` <br>
`NOTE: When using C:\AD\Tools\servers.txt, always remember to remove domain controllers.`

### Access Control List - ACL
Enables access control based on:
- Access Tokens (identity and privileges of the user)
- Security Descriptors (object description, contains SID of the onwer, Discretionary ACL (DACL), and System ACL (SACL))
    - DACL -> Defines permissions on an object
    - SACL -> Logs audit messages on object access

An ACL is a list of ACE (Access Control Entries).
<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/acl.png">
In the example above, thread A gets <b>access denied</b>, while thread B has <b>read, write, and execute</b> access.
<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/connection_acl.png">
In the example above, <b>GenericWrite</b> allows access to <b>reset password, targeted kerberoasting, shadow credentials, and logon scripts</b> on a <b>user</b>.
<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/dacl.png">
In the example above, this is a DACL, where highlighted is an <b>allow entry that allows SYSTEM to have full control on the Domain Admins group.</b> 

Full control here means <b>GenericAll</b> access.
<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/sacl.png">
The example above shows a SACL.

#### Domain Enumeration - ACL
- `Get-DomainObjectAcl -SamAccountName student1 -ResolveGUIDs` -> Get the ACLs associated with the specified object
- `Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs` -> Get ACLs for a certain group
- `Get-DomainObjectAcl -SearchBase "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose` -> Get the ACLs associated with the specified prefix to be used for search
- `(Get-Acl 'AD:\CN=Administrator,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access` -> Enumerate ACLs using ActiveDirectory module without resolving GUIDs
- `Find-InterestingDomainAcl -ResolveGUIDs` -> Search for interesting ACEs
- `Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"` -> Get the ACLs associated with the specified path

### Group Policy and Group Policy Object - GPO
Group Policy provides ability to manage configurations and changes. Group policy settings are contained in a <b>Group Policy Object (GPO)</b>

Overly permissive GPOs can be abused for attacks like privilege escalation, backdoors, persistence, etc.

### Organizational Units - OU
Organizations use OU for delegating administration. An OU is the lowest-level AD container to which GPO can be applied.

#### Domain Enumerations - GPO & OU

- `Get-DomainGPO` -> Get list of GPO in current domain
- `Get-DomainGPO | select displayname`
- `Get-DomainGPO -ComputerIdentity dcorp-student1` 
- `Get-DomainGPOLocalGroup` -> Get GPO(s) which use Restricted Groups or groups.xml for interesting users
- `Get-DomainGPOComputerLocalGroupMapping -ComputerIdentity dcorp-student1` -> Get users which are in a local group of a machine using GPO
- `Get-DomainGPOUserLocalGroupMapping -Identity student1 -Verbose` -> Get machines where the given user is member of a specific groups
- `Get-DomainOU`
- `Get-DomainOU | select -ExpandProperty name`
- `(Get-DomainOU -Identity DevOps).distinguishedname | %{Get-DomainComputer -SearchBase $_} | select name` -> Get computers in the DevOps OU 
- `Get-ADOrganizationalUnit -Filter * -Properties *` -> Get OUs in a domain
- `Get-DomainGPO -Identity "{0D1CC23D-1F20-4EEE-AF64-D99597AE2A6E}"` -> Get GPO applied on an OU. Read GPOname from gplink attribute from Get-NetOU

### Trusts
Relationship between two domains or forests which allows users of one domain or forest to access resources in another domain/forest. Trust can be automatic (parent-child, same forest) or established (forest, external). 
- One-way trust -> users in the trusted domain can access resources in the trusting domain
- Two-way trust -> Users of both domains can access resources in each other's domain

#### Trust Transitivity 
- Transitive -> Can be extended to establish trust relationsihp with other domains
    - If domain A has a two-way trust with domain B and domain B has a two-way trust with domain C, domain A has a two way trust with domain C
- Non-transitive -> Cannot be extended to other domains in the forest
    - Default trust (external trust) between two domains in different forests
#### Default/Automatic Trusts
- Parent-child trust -> created automatically between the new domain and the domain that precedes it in the namespace hierarchy. Example: dollarcorp.moneycorp.local is a child of moneycorp.local
    - Always two-way transitive
- Tree-root trust -> created automatically whenever a new domain tree is added to a forest roort
    - Always two-way transitive

<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/forest-trust.png">
In the example above, domain B has two-way access to domain Z and vice-versa

#### External Trusts
- Can be one-way or two-way and is non-transitive
- Between two domains in different forests

<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/external-trust.png">

In the example above, Z can access Y, but Z can only access resources explicitly shared by B. Even though Z and B is two-way, it's an external trust, therefore non-transitive

#### Forest Trusts
- Between forest root domains
- Cannot be extended to a third forest-trust
- can be one-way or two-way transitive

<img src="/home/farel/Documents/Personal Projects/Learning Notes/CRTP/resources/forest-trust2.png">

#### Domain Enumeration - Trust
- `Get-DomainTrust` -> Get a list of all domain trusts for the current domain
- `Get-DomainTrust -Domain us.dollarcorp.moneycorp.local`
- `Get-ADTrust`
- `Get-ADTrust -Identity us.dollarcorp.moneycorp.local`
- `Get-Forest` -> Get details about the current forest
- `Get-Forest -Forest eurocorp.local`
- `Get-ADForest`
- `Get-ADForest -Identity eurocorp.local`
- `Get-ForestDomain` -> Get all domains in the current forest
- `Get-ForestDomain -Forest eurocorp.local`
- `(Get-ADForest).Domains`
- `Get-ForestGlobalCatalog` -> Get all global catalogs for the current forest
- `Get-ForestGlobalCatalog -Forest eurocorp.local`
- `Get-ADForest | select -ExpandProperty GlobalCatalogs`
- `Get-ForestTrust` -> Map trusts of a forest
- `Get-ForestTrust -Forest eurocorp.local`
- `Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"'`

#### Domain Enumeration - User Hunting
- `Find-WMILocalAdminAccess.ps1`
- `Find-PSRemotingLocalAdminAccess.ps1`
- Can also be done with remote administration tools like WMI and PowerShell remoting
- Not silent
    - `4624` -> Log-on
    - `4634` -> Log-off
    - `4672` -> Log-in success
- `Find-DomainUserLocation -Verbose` -> Find computers where a domain admin (or specified user/group) has sessions
- `Find-DomainUserLocation -UserGroupIdentity "RDPUsers"`
- `Find-DomainUserLocation -CheckAccess` -> Find computers where a domain admin session is available and current user 
has admin access (uses Test-AdminAccess).
- `Find-DomainUserLocation -Stealth` -> Find computers (File Servers and Distributed File servers) where a domain 
admin session is available.
- [SessionHunter](https://github.com/Leo4j/Invoke-SessionHunter)
    - `Invoke-SessionHunter -FailSafe`
    - `Invoke-SessionHunter -NoPortScan -Targets C:\AD\Tools\servers.txt` -> Opsec friendly command (avoid connecting to all target machines by specifying targets)

