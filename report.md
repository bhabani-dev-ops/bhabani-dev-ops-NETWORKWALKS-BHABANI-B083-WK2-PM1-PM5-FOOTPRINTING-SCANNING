# PENETRATION TESTING REPORT
## FOOTPRINTING & NETWORK SCANNING PHASES
### W2-PM-FINAL | CYBERSECURITY | NETWORKWALKS

---

| Field | Details |
|---|---|
| **Pentester Name (Cybersecurity Professional)** | Bhabani Priyadarshini Panda |
| **Program/Batch** | B083 – Networkwalks |
| **Date** | 17 September 2026 |
| **Modules completed** | W2-PM1 (Multiple Kali Tools)<br>W2-PM5 (Zenmap Scanning) |
| **Client/Target** | 1. Networkwalks (secured written permission already)<br>2. My own local LAN network |
| **Permission secured from client?** | Yes |
| **Phases covered** | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery<br>Phase 3–5: In Progress |

---

## 1. Liability Disclaimer

I have performed these activities only on the systems & devices where I had secured written permission or the devices/systems that I own myself. All these materials are for education and research purpose only. Do not use anything from here to break the law. The instructor, the authors and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job and a permanent record. In most countries unauthorised access is a crime even when nothing is damaged.

---

## 2. Introduction

This report covers footprinting the networkwalks.com domain using multiple Kali Linux tools (W2-PM1) and scanning my own local network with Zenmap (W2-PM5). One module covers the footprinting phase and the other covers the scanning phase, so together they show how an attacker moves from gathering public information to mapping live hosts on a network. It is the Week 2 part of my ongoing internship program at Networkwalks.

All commands were run in Kali Linux, for both the footprinting and the scanning activity. Every step below includes the exact command used, the result I observed, a screenshot as evidence, and a short note on why the finding matters from an attacker's point of view.

---

## 3. Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux | Operating system used for all reconnaissance and scanning activities |
| WHOIS | Find domain registration details (owner, dates, name servers) |
| whatweb | Fingerprint web technologies (server, CMS, plugins, IP) |
| nslookup | Resolve the domain name to its IP address using DNS |
| curl -I | Read the HTTP response headers of the website |
| wafw00f | Detect whether a Web Application Firewall protects the site |
| dnsrecon | Enumerate all DNS records (NS, MX, SPF, TXT, SRV) |
| Zenmap (Nmap GUI) | Scan the local subnet to find live hosts, IPs and MAC addresses |
| ip a / ifconfig | Local IP and subnet identification on Kali |

---

## 4. Activities Performed

### 4.1 Footprinting & Reconnaissance

I performed reconnaissance against the networkwalks.com domain using six Kali Linux tools: WHOIS, WhatWeb, Nslookup, Curl, Wafw00f and DNSRecon. Each tool was used to collect a different type of information about the target.

First, I used WHOIS to obtain publicly available domain registration information and identify the domain's name servers. The results showed the domain is registered with GoDaddy.com, LLC, created on 6 November 2019 and expiring on 6 November 2027, with name servers pointing to HostGator (NS6135/NS6136.HOSTGATOR.COM) and DomainControl (NS29/NS30.DOMAINCONTROL.COM). The registrant identity is privacy-protected via Domains By Proxy, LLC.

I then used WhatWeb to identify technologies used by the website. The results identified WordPress 7.1 and WP Download Manager 3.3.58, running on Apache at IP 192.232.216.135, along with Bootstrap 7.1, jQuery 3.7.1 and Google Tag Manager.

Using Nslookup, I resolved the domain name to its IP address. The result confirmed 192.232.216.135.

I used Curl with the -I option to inspect the HTTP response headers. The response was HTTP/2 200 from an Apache server, and exposed the WordPress REST API endpoint /wp-json/, along with caching headers (x-nginx-cache, x-endurance-cache-level) indicating an Endurance/HostGator hosting stack.

Next, I used Wafw00f to determine whether a Web Application Firewall was protecting the website. The result identified ModSecurity (SpiderLabs).

Finally, I used DNSRecon to enumerate DNS records. The results identified a mail server (mail.networkwalks.com), DNS software BIND 9.16.23, an SPF record, and 8 SRV records pointing to cPanel email autodiscovery hosts — confirming cPanel-based hosting.

### 4.2 Network Scanning with Zenmap

For the second activity, I used Zenmap (Nmap GUI) on Kali Linux to perform network discovery on my own local lab network. The practical required me to identify my local IP address and subnet, discover live hosts, identify their IP and MAC addresses, and generate a network topology.

I first used the `ip a` command on Kali to identify my local IP address (10.0.0.2) and LAN subnet (10.0.0.0/24, VirtualBox NAT Network). I then entered the subnet into Zenmap and selected Ping Scan to identify active hosts.

The scan (`nmap -sn 10.0.0.0/24`) identified 2 live hosts:

- 10.0.0.1 (gateway) — MAC address 52:54:00:12:35:00 (QEMU virtual NIC)
- 10.0.0.2 (my own Kali VM)

The scan completed in 2.92 seconds, checking all 256 addresses in the subnet.

After completing the scan, I opened the Topology section in Zenmap, which displayed a star topology with localhost at the center connected to both live hosts, and captured it as evidence.

---

## 5. Risk Analysis / Impact

Based on the information collected during the footprinting and network scanning activities, I identified the following potential risks.

Risk level key: ● Critical &nbsp;&nbsp; ● Medium &nbsp;&nbsp; ● Low

| # | Risk / Finding | Evidence / Observation | Potential Impact | Risk Level |
|---|---|---|---|---|
| 1 | Web technology information exposed | WhatWeb identified WordPress 7.1 and WP Download Manager 3.3.58 | Attackers may use exposed technology/version information to identify software requiring further security review | ● Medium |
| 2 | Server IP address identifiable | Nslookup resolved the domain to 192.232.216.135 | Provides information about the network location of the web service | ● Low |
| 3 | HTTP technical information exposed | Curl returned HTTP response headers and exposed /wp-json/ | May assist technology fingerprinting and further enumeration | ● Low |
| 4 | WAF technology identifiable | Wafw00f identified ModSecurity (SpiderLabs) | Reveals information about the web application's security architecture | ● Low |
| 5 | DNS infrastructure information exposed | DNSRecon identified DNS, mail and service-related records (8 SRV records, cPanel hosting) | DNS information can help build a broader infrastructure profile | ● Medium |
| 6 | Live hosts visible on local network | Zenmap identified 2 live hosts on the scanned subnet | Unknown or unauthorized devices may potentially be present on a network | ● Low |

The risks above are observations from the footprinting and scanning exercises, not confirmed vulnerabilities.

The practical exercises primarily involved information gathering and host discovery. No exploitation or vulnerability validation was performed as part of these two modules.

Therefore, the presence of information such as a software version, IP address or DNS record does not by itself mean that the system is vulnerable. Further authorized security testing would be required to confirm any actual vulnerability.

---

## 6. Recommendations

Based on the observations from these activities, I recommend the following security improvements:

**Review publicly exposed technology information**
Organizations should regularly review what information about their web technologies, CMS and plugins is publicly visible.

**Keep software updated**
CMS platforms, plugins and other web technologies should be regularly updated and reviewed against current security advisories.

**Review HTTP headers**
HTTP response headers should be reviewed to determine whether unnecessary technical information is being exposed.

**Review DNS records regularly**
DNS records should be checked periodically to ensure that only required information and services are publicly exposed.

**Properly configure and monitor the WAF**
Keep the WAF (ModSecurity) enabled and tuned, since it already blocks naive attacks.

**Perform regular internal network discovery**
Organizations should periodically scan their own networks to identify active devices.

**Investigate unknown devices**
Any unexpected device discovered during network scanning should be investigated and verified.

**Maintain network documentation**
Network topology and device information should be documented and updated regularly.

**Perform security testing with authorization**
Reconnaissance and scanning should only be performed against systems and networks where appropriate authorization has been provided.

---

## 7. Conclusion

During Week 2 of my Cybersecurity & Ethical Hacking internship, I completed practical activities covering footprinting, reconnaissance and network scanning.

In the footprinting activity, I used six Kali Linux tools to collect information about the target domain. I learned how WHOIS can provide domain information, WhatWeb can identify web technologies, Nslookup can resolve domain names, Curl can inspect HTTP headers, Wafw00f can identify a WAF, and DNSRecon can provide additional DNS information.

In the network scanning activity, I used Zenmap to identify my local network configuration and discover active hosts. I also collected IP and MAC address information and created a network topology.

The exercises showed me that information gathering is an important part of cybersecurity. Even before attempting to exploit a system, a security professional can learn a significant amount about an environment by carefully analyzing publicly available information and network responses.

I also learned that technical findings should be documented clearly. A good cybersecurity report should explain what was performed, what was discovered, what the observation means, what risk it may create, and what can be done to reduce that risk.

Finally, I learned that reconnaissance and scanning must always be performed within an authorized scope. These activities were completed as part of the assigned educational cybersecurity lab.

---

## 8. Evidences Collected

All screenshots referenced in this report are included in this repository:

| Task | Screenshot |
|---|---|
| WHOIS output | `01-whois-1.png`, `01-whois-2.png` |
| WhatWeb output | `02-whatweb.png` |
| Nslookup output | `03-nslookup.png` |
| Curl -I output | `04-curl.png` |
| Wafw00f output | `05-wafw00f.png` |
| Dnsrecon output | `06-dnsrecon.png` |
| Zenmap Nmap Output (ping scan) | `07-zenmap-output.png` |
| Zenmap Topology | `08-zenmap-topology.png` |

-End-

## 👤 Author

**Bhabani Priyadarshini Panda**
Cybersecurity Professional — Batch B083
LinkedIn: <https://linkedin.com/in/bp69>

## 📌 Project Information

Program Name: Cybersecurity program at Networkwalks | Week: 02 | Repository: GitHub
