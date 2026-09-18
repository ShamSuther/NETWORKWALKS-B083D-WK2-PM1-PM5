# PENETRATION TESTING REPORT
## FOOTPRINTING & NETWORK SCANNING PHASES
### W2-PM-FINAL | CYBERSECURITY | NETWORKWALKS

---

| Field | Details |
|---|---|
| **Pentester Name** | *Sham Sunder* |
| **Program/Batch** | *B083 – Networkwalks* |
| **Date** | *18 September 2026* |
| **Modules completed** | *W2-PM1<br>W2-PM5* |
| **Client/Target** | *1. Networkwalks<br>2. My own local LAN network* |
| **Permission secured from client?** | *Yes* |
| **Phases covered** | *Phase 1: Reconnaissance/Footprinting<br>Phase 5: Network Scanning<br>Phase 2-4: In Progress* |

---

## 1. Liability Disclaimer

I have performed these activities only on the systems & devices where I had secured written permission or the devices/systems that I own myself. All these materials are for education and research purpose only. Do not use anything from here to break the law. The instructor, the authors and Networkwalks are not responsible for what you do with this knowledge. Every action you take is your own responsibility. Misuse can lead to criminal charges, heavy fines, loss of your job and a permanent record. In most countries unauthorised access is a crime even when nothing is damaged.

---

## 2. Introduction

This report covers footprinting the `networkwalks.com` domain using multiple Kali Linux tools (W2-PM1) and scanning my own local network with Zenmap (W2-PM5). One module covers the footprinting phase and the other covers the scanning phase, so together they show how an attacker moves from gathering public information to mapping live hosts on a network. It is the Week 2 part of my ongoing internship program at Networkwalks.

---

## 3. Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux | *Operating system used for PM-1* |
| Windows 10 | *Operating system used for PM-5* |
| WHOIS | F*ind domain registration details (owner, dates, name servers)* |
| whatweb | *Fingerprint web technologies (server, CMS, plugins, IP)* |
| nslookup | *Resolve the domain name to its IP address using DNS* |
| curl -I | *Read the HTTP response headers of the website* |
| wafw00f | *Detect whether a Web Application Firewall protects the site* |
| dnsrecon | *Enumerate all DNS records* |
| Zenmap | *Scan the local subnet to find live hosts, IPs and MAC addresses* |
| `ipconfig` | *Local IP and subnet identification on Kali* |

---

## 4. Activities Performed

### 4.1 Footprinting/Reconnaissance

I performed reconnaissance against the networkwalks.com domain using six Kali Linux tools: WHOIS, WhatWeb, Nslookup, Curl, Wafw00f and DNSRecon. Each tool was used to collect a different type of information about the target.

- First, I used WHOIS to obtain publicly available domain registration information and identify the domain's name servers. The results showed the domain is registered with GoDaddy.com, LLC, created on 6 November 2019 and expiring on 6 November 2027, with name servers pointing to HostGator `NS6135/NS6136.HOSTGATOR.COM` and DomainControl `NS29/NS30.DOMAINCONTROL.COM`. The registrant identity is privacy-protected via Domains By Proxy, LLC.

- I then used WhatWeb to identify technologies used by the website. The results identified WordPress `7.1` and WP Download Manager `3.3.58`, running on Apache at IP `192.232.216.135`.

- Using Nslookup, I resolved the domain name to its IP address. The result confirmed `192.232.216.135`.

- I used Curl with the -I option to inspect the HTTP response headers. The response was `HTTP/2 200` from an Apache server, and exposed the WordPress REST API endpoint `/wp-json/`, along with caching headers `x-nginx-cache, x-endurance-cache-level` indicating an Endurance/HostGator hosting stack.

- Next, I used Wafw00f to determine whether a Web Application Firewall was protecting the website. The result identified ModSecurity (SpiderLabs).

- Finally, I used DNSRecon to enumerate DNS records. The results identified a mail server `mail.networkwalks.com`, DNS software BIND 9.16.23, an SPF record, and 8 SRV records pointing to cPanel email autodiscovery hosts — confirming cPanel-based hosting.

### 4.2 Network Scanning with Zenmap

For the second activity, I used Zenmap (Nmap GUI) on Windows 10 VM to perform network discovery on my own local lab network. The practical required me to identify my local IP address and subnet, discover live hosts, identify their IP and MAC addresses, and generate a network topology.

I first used the `ipconfig` commandline to identify my local IP address `10.0.0.3` and LAN subnet `10.0.0.0/24`. Then I entered the subnet into Zenmap and selected `ping scan` to identify active hosts.

The scan command `nmap -sn 10.0.0.0/24` identified 3 live hosts:

- `10.0.0.1` (Default gateway) — MAC address `52:55:0A:00:00:01`
- `10.0.0.2` (Kali Linux) — MAC address `08:00:27:DB:A3:A6`
- `10.0.0.3` (Local Windows 10 VM) — MAC address `08-00-27-EB-AD-02`

After completing the scan, I opened the Topology section in Zenmap, which displayed a star topology with localhost at the center connected to both live hosts, and captured it as evidence.

---

## 5. Risk Analysis / Impact

Based on the information collected during the footprinting and network scanning activities, I identified the following potential risks.


| # | Risk/Finding | Evidence/Observation | Potential Impact |Risk Level |
|---|---|---|---|---|
| 1 | Web technology information exposed | WhatWeb identified WordPress `7.1` and WP Download Manager `3.3.58` | Attackers may use exposed technology/version information to identify software requiring further security review | Medium |
| 2 | Server IP address identifiable | Nslookup resolved the domain to `192.232.216.135` | Provides information about the network location of the web service | Low |
| 3 | HTTP technical information exposed | Curl returned HTTP response headers and exposed /wp-json/ | May assist technology fingerprinting and further enumeration | Low |
| 4 | WAF technology identifiable | Wafw00f identified ModSecurity (SpiderLabs) | Reveals information about the web application's security architecture | Low |
| 5 | DNS infrastructure information exposed | DNSRecon identified DNS, mail and service-related records (8 SRV records, cPanel hosting) | DNS information can help build a broader infrastructure profile | Medium |
| 6 | Live hosts visible on local network | Zenmap identified 2 live hosts on the scanned subnet | Unknown or unauthorized devices may potentially be present on a network | Low |

The risks above are observations from the footprinting and scanning exercises, not confirmed vulnerabilities.

The practical exercises primarily involved information gathering and host discovery. No exploitation or vulnerability validation was performed as part of these two modules.

Therefore, the presence of information such as a software version, IP address or DNS record does not by itself mean that the system is vulnerable. Further authorized security testing would be required to confirm any actual vulnerability.

---

## 6. Recommendations

Based on the observations from these activities, I recommend the following security improvements:

1. **Perform regular internal network discovery:**
   Organizations should periodically scan their own networks to identify active devices.

2. **Review HTTP headers:**
   HTTP response headers should be reviewed to determine whether unnecessary technical information is being exposed.

3. **Investigate unknown devices:**
   Any unexpected device discovered during network scanning should be investigated and verified.

4. **Keep software updated:**
   CMS platforms, plugins, and other web technologies should be regularly updated and checked against current security advisories.

5. **Maintain network documentation:**
   Network topology and device information should be documented and updated regularly.

6. **Properly configure and monitor the WAF:**
   The WAF (ModSecurity) should be kept enabled and properly tuned, since it already blocks basic attacks.

7. **Review DNS records regularly:**
   DNS records should be checked periodically to ensure that only required information and services are publicly exposed.

8. **Perform security testing with authorization:**
   Reconnaissance and scanning should only be performed against systems and networks where proper authorization has been provided.

9. **Review publicly exposed technology information:**
   Organizations should regularly review what information about their web technologies, CMS, and plugins is publicly visible.

---

## 7. Conclusion

During Week 2 of my Cybersecurity & Ethical Hacking internship, I worked on practical activities related to **footprinting, reconnaissance, and network scanning**.

For the footprinting activity, I used six different Kali Linux tools to gather information about the target domain. I used **WHOIS** to collect domain information, **WhatWeb** to identify web technologies, **Nslookup** to resolve domain names, **Curl** to inspect HTTP headers, **Wafw00f** to detect a Web Application Firewall (WAF), and **DNSRecon** to gather additional DNS information.

For the network scanning activity, I used **Zenmap** to analyze my local network and identify active hosts. I also collected information such as **IP addresses and MAC addresses**, and used the findings to create a basic network topology. These exercises helped me understand how important **information gathering and reconnaissance** are in cybersecurity.

Before attempting to exploit a system, a security professional can already learn a lot about a target environment by analyzing publicly available information and observing how systems respond to network requests. 

I also learned the importance of **proper documentation**. A cybersecurity report should clearly explain what was performed, what was discovered, what the findings mean, what risks they could create, and what steps can be taken to reduce those risks. 

Finally, I learned that reconnaissance and scanning should always be performed within an **authorized scope**. All of these activities were completed as part of the assigned educational cybersecurity lab.

---

## 8. Evidences Collected

All screenshots referenced in this report are included in this repository:

**Module 1: Footprinting**

![PM1_whois](/Module 1/0_whois.png)
![PM1_whatweb](/Module 1/1_whatweb.png)
![PM1_nslookup](/Module 1/2_nslookup.png)
![PM1_curl](/Module 1/3_curl_I.png)
![PM1_wafw00f](/Module 1/4_wafw00f.png)
![PM1_dns_recon](/Module 1/5_dns_recon.png)

**Module 5: Network Scanning**

![PM5_IPConfig](/Module 5/2_IPConfig.png)
![PM5_Running_ZenMap](/Module 5/3_Running_ZenMap_for_Subnet.png)
![PM5_Subnet_Topology](/Module 5/4_Subnet_Topology.png)
![PM5_Exporting_the_Topology](/Module 5/5_Exporting_the_Topology_as_pdf.png)


## Author

Sham Sunder\
Cybersecurity B083D
