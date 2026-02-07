# MINDMAP

## LO 1
- User enumeration
- Computer enumeration
- Domain admin enumeration -> svcadmin
- Enterprise enumeration

File share where i have write permissions
- AI share on dcorp-ci

## LO 2
- ACL for domain admins group
- where i have interesting permissions
    - Own user, nothing interesting
    - RDP user (i am part of RDP user) `look for GenericAll`<br>
    `GenericAll rights on Control214User objectDN`
        - Full control/generic all over supportx and controlx users
        - Enrollment permissions on multiple certificate templates
        - Full control/generic all on the applocked group policy

## LO 3 
- OU enumeration -> Enumerate computers in OUs
- Computer enumeration in DevOps OU 
- GPO enumeration
- Enumerate GPO on DevOps OU
- Enumerate ACL on Applocker and Devops GPO
    - RDPUsers group have GenericAll over Applocker policy
    - devopsadmin has WriteDACL on DevOps Policy
