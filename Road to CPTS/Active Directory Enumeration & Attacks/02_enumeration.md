# External Recon and Enumeration Principles

## What to Look For
| Data Point | Description |
|---|---|
| `IP Space` | Valid ASN for our target, netblocks in use for the organization's public-facing infrastructure, cloud presence and the hosting providers, DNS record entries, etc. |
| `Domain Information` | Based on IP data, DNS, and site registrations. Who administers the domain? Are there any subdomains tied to our target? Are there any publicly accessible domain services present? (Mailservers, DNS, Websites, VPN portals, etc.) Can we determine what kind of defenses are in place? (SIEM, AV, IPS/IDS in use, etc.) |
| `Schema Format` | Can we discover the organization's email accounts, AD usernames, and even password policies? Anything that will give us information we can use to build a valid username list to test external-facing services for password spraying, credential stuffing, brute forcing, etc. |
| `Data Disclosures` | For data disclosures we will be looking for publicly accessible files (.pdf, .ppt, .docx, .xlsx, etc.) for any information that helps shed light on the target. For example, any published files that contain intranet site listings, user metadata, shares, or other critical software or hardware in the environment (credentials pushed to a public GitHub repo, the internal AD username format in the metadata of a PDF, for example). |
| `Breach Data` | Any publicly released usernames, passwords, or other critical information that can help an attacker gain a foothold. |

## Where to Look
| Resource | Examples |
|---|---|
| `ASN / IP registrars` | [IANA](https://www.iana.org/), [arin](https://www.arin.net/) (Americas), [RIPE](https://www.ripe.net/) (Europe), [BGP](https://bgp.he.net/) |
| `Domain Registrars & DNS` | [Domaintools](https://www.domaintools.com/), manual DNS record requests against the domain in question or against well known DNS servers, such as 8.8.8.8. |
| `Social Media` | Searching Linkedin, Twitter, Facebook, your region's major social media sites, news articles, and any relevant info you can find about the organization. |
| `Public-Facing Company Websites` | Often, the public website for a corporation will have relevant info embedded. News articles, embedded documents, and the "About Us" and "Contact Us" pages can also be gold mines. |
| `Cloud & Dev Storage Spaces` | GitHub, [AWS S3 buckets & Azure Blog storage containers](https://grayhatwarfare.com/), [Google searches using "Dorks"](https://www.exploit-db.com/google-hacking-database) |
| `Breach Data Sources` | [HaveIBeenPwned](https://haveibeenpwned.com/) to determine if any corporate email accounts appear in public breach data, Dehashed to search for corporate emails with cleartext passwords or hashes we can try to crack offline. We can then try these passwords against any exposed login portals (Citrix, RDS, OWA, 0365, VPN, VMware Horizon, custom applications, etc.) that may use AD authentication. |

## Finding Address Spaces
[BGP-Toolkit](https://bgp.he.net/) is a resource for researching what address blocks are assigned to an organization and what ASN they reside in. 

## DNS
[domaintools](https://whois.domaintools.com/) and [viewdns.info](https://viewdns.info/) can retrieve records and other data ranging from DNS resolution to testing for DNSSEC and if the site is accessible in more restricted countries.

## Public Data
LinkedIn, Indeed.com, and Glassdoor can reveal information about the target organization, infrastructure, etc. 

## Example Enumeration Process
```
farelmusyaffa@htb[/htb]$ nslookup ns1.inlanefreight.com

Server:		192.168.186.1
Address:	192.168.186.1#53

Non-authoritative answer:
Name:	ns1.inlanefreight.com
Address: 178.128.39.165

nslookup ns2.inlanefreight.com
Server:		192.168.86.1
Address:	192.168.86.1#53

Non-authoritative answer:
Name:	ns2.inlanefreight.com
Address: 206.189.119.186
```

### Google Dorking
```
filetype:pdf inurl:inlanefreight.com

intext:"@inlanefreight.com" inurl:inlanefreight.com
```

## Username Harvesting
[linkedin2username](https://github.com/initstring/linkedin2username) scrapes data from a company's LinkedIn page and creates various mashups of usernames

## Credential Hunting
[dehashed](https://dehashed.com/) is a tool for hunting cleartext credentials and password hashes in breach data.
```
farelmusyaffa@htb[/htb]$ sudo python3 dehashed.py -q inlanefreight.local -p

id : 5996447501
email : roger.grimes@inlanefreight.local
username : rgrimes
password : Ilovefishing!
hashed_password : 
name : Roger Grimes
vin : 
address : 
phone : 
database_name : ModBSolutions

id : 7344467234
email : jane.yu@inlanefreight.local
username : jyu
password : Starlight1982_!
hashed_password : 
name : Jane Yu
vin : 
address : 
phone : 
database_name : MyFitnessPal

<SNIP>
```


