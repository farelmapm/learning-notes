# Firewall Setup

## Iptables
`iptables` provides a flexible set of rules for filtering network traffic based on various criteria such as source and destination IP addresses, port numbers, protocols, and more. `nftables` also provides a more modern syntax and improved perfomrance over `iptables`, but the syntax of `nftables` rules is not compatible with `iptables`. 

`UFW` stands for "Uncompilcated Firewall", and provides a simple and user-friendly interface for configuring firewall rules. 

FirewallID provides a dynamic and flexible firewall solution that can be used to manage complex firewall configurations.

The main components of `iptables` are:

| Component | Description                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tables    | Tables are used to organize and categorize firewall rules.                                                                                                                 |
| Chains    | Chains are used to group a set of firewall rules applied to a specific type of network traffic.                                                                            |
| Rules     | Rules define the criteria for filtering network traffic and the actions to take for packets that match the criteria.                                                       |
| Matches   | Matches are used to match specific criteria for filtering network traffic, such as source or destination IP addresses, ports, protocols, and more.                         |
| Targets   | Targets specify the action for packets that match a specific rule. For example, targets can be used to accept, drop, or reject packets or modify the packets in another way. |

#### Tables
Tables in `iptables` are used to categorize and organize firewall rules based on the type of traffic that they are designed to handle. These tables are used to organize and categorize firewall rules. 
| Table Name | Description                                                                                     | Built-in Chains                               |
|------------|-------------------------------------------------------------------------------------------------|------------------------------------------------|
| filter     | Used to filter network traffic based on IP addresses, ports, and protocols.                     | INPUT, OUTPUT, FORWARD                         |
| nat        | Used to modify the source or destination IP addresses of network packets.                       | PREROUTING, POSTROUTING                        |
| mangle     | Used to modify the header fields of network packets.                                            | PREROUTING, OUTPUT, INPUT, FORWARD, POSTROUTING |

#### Chains
In `iptables`, chains organize rules that define how network traffic should be filtered or modified. There are two types of chains in iptables:
- Built-in chains 
- User-defined chains


