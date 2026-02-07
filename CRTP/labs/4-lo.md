# LEARNING OBJECTIVE 4

1. Enumerate all domains in the moneycorp.local forest.

```
Get-ForestDomain | select name

Name
----
dollarcorp.moneycorp.local
moneycorp.local
us.dollarcorp.moneycorp.local
```

2. Map the trusts of the dollarcorp.moneycorp.local domain.

```
Get-DomainTrust


SourceName      : dollarcorp.moneycorp.local
TargetName      : moneycorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : WITHIN_FOREST
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 5:59:01 AM
WhenChanged     : 1/19/2026 9:03:27 PM

SourceName      : dollarcorp.moneycorp.local
TargetName      : us.dollarcorp.moneycorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : WITHIN_FOREST
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 6:22:51 AM
WhenChanged     : 1/22/2026 10:06:15 PM

SourceName      : dollarcorp.moneycorp.local
TargetName      : eurocorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : FILTER_SIDS
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 8:15:23 AM
WhenChanged     : 1/22/2026 10:06:10 PM
```

3. Map External trusts in moneycorp.local forest.

`NOTE: Indicated by TrustAttributes: FILTER_SIDS`

```
Get-ForestDomain | %{Get-DomainTrust -Domain $_.Name} | ?{$_.TrustAttributes -eq "FILTER_SIDS"}


SourceName      : dollarcorp.moneycorp.local
TargetName      : eurocorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : FILTER_SIDS
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 8:15:23 AM
WhenChanged     : 1/22/2026 10:06:10 PM
```

4. Identify external trusts of dollarcorp domain. Can you enumerate trusts for a trusting forest?

`Because there is an external trust to eurocorp.local and it is bidirectional, we can enumerate trust for this domain as well`

`NOTE: answer below only enumerates eurocorp.local, so NOT REALLY THE CORRECT ANSWER`

```
get-domaintrust -Domain eurocorp.local


SourceName      : eurocorp.local
TargetName      : eu.eurocorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : WITHIN_FOREST
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 5:49:08 AM
WhenChanged     : 1/22/2026 10:06:25 PM

SourceName      : eurocorp.local
TargetName      : dollarcorp.moneycorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : FILTER_SIDS
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 8:15:23 AM
WhenChanged     : 1/22/2026 10:06:10 PM
```

Official documentation:
```
Get-ForestDomain -Forest eurocorp.local | %{Get-DomainTrust -Domain $_.Name}

SourceName      : eurocorp.local
TargetName      : eu.eurocorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : WITHIN_FOREST
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 5:49:08 AM
WhenChanged     : 3/3/2023 10:15:16 AM

SourceName      : eurocorp.local
TargetName      : dollarcorp.moneycorp.local
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : FILTER_SIDS
TrustDirection  : Bidirectional
WhenCreated     : 11/12/2022 8:15:23 AM
WhenChanged     : 2/24/2023 9:10:52 AM

Exception calling "FindAll" with "0" argument(s): "A referral was returned from the server.
[snip]

```

`Notice the error above. It occurred because PowerView attempted to list trusts even for eu.eurocorp.local. Because external trust is non-transitive it was not possible!`
