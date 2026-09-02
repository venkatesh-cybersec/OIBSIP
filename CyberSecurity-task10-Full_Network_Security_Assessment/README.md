# Network Security Assessment

## Oasis Infobyte — Security Analysis Internship

This project documents a full network security assessment performed in an authorized lab environment using Kali Linux and a Metasploitable target.

> **Disclaimer:** This assessment was performed only against a controlled lab system for educational and internship purposes.

---

## Objective

The objective of this assessment is to identify exposed services, analyze network traffic, scan web services for common vulnerabilities, document security findings, and provide a practical remediation roadmap.

---

## Assessment Scope

| Item | Details |
|---|---|
| Target | Metasploitable Lab VM |
| Target IP | `10.170.20.105` |
| Assessment Type | Network Security Assessment |
| Environment | VirtualBox / Kali Linux |
| Tools | Nmap, Wireshark, Nikto |
| Network | Authorized isolated lab environment |

### Scope Includes

- Host and service discovery
- Service/version identification
- Operating-system detection
- Network traffic analysis
- HTTP, DNS and ARP analysis
- Web vulnerability scanning
- Risk classification
- Remediation recommendations

---

## Methodology

The assessment was divided into three phases:

### Phase 1 — Reconnaissance

Nmap was used to identify:

- Live hosts
- Open ports
- Running services
- Service versions
- Operating-system information

Command used:

```bash
sudo nmap -sV -O 10.170.20.105
```

Results were saved using:

```bash
sudo nmap -sV -O 10.170.20.105 -oN nmap_results.txt
```

---

### Phase 2 — Traffic Analysis

Wireshark was used to capture network traffic for at least five minutes.

The following protocols were analyzed:

```text
HTTP
DNS
ARP
TCP
```

Wireshark display filters:

```text
http
dns
arp
tcp
```

The analysis focused on:

- TCP communication
- DNS requests and responses
- ARP requests and replies
- HTTP traffic
- Possible unencrypted information

The capture file is stored as:

```text
wireshark_capture.pcap
```

> Add only observations that are actually visible in the captured packets. Do not report credentials or sensitive information unless they were genuinely observed.

---

### Phase 3 — Web Vulnerability Scanning

Nikto was used to assess available HTTP web services.

Example commands:

```bash
nikto -h http://10.170.20.105
```

If the Tomcat service on port 8180 was assessed:

```bash
nikto -h http://10.170.20.105:8180
```

Record the actual Nikto findings and screenshots in the final report.

---

## Nmap Findings

The Nmap service/version scan identified multiple exposed services on the target.

| Port | Service | Version / Information |
|---:|---|---|
| 21 | FTP | vsftpd 2.3.4 |
| 22 | SSH | OpenSSH 4.7p1 |
| 23 | Telnet | Linux telnetd |
| 25 | SMTP | Postfix smtpd |
| 53 | DNS | ISC BIND 9.4.2 |
| 80 | HTTP | Apache 2.2.8 |
| 111 | RPC | rpcbind |
| 139 | NetBIOS | Samba |
| 445 | SMB | Samba |
| 512 | rexec | netkit-rsh rexecd |
| 513 | rlogin | rlogind |
| 514 | TCP | tcpwrapped |
| 1099 | Java RMI | GNU Classpath grmiregistry |
| 1524 | Bindshell | Metasploitable root shell |
| 2049 | NFS | NFS v2–4 |
| 2121 | FTP | ProFTPD 1.3.1 |
| 3306 | MySQL | MySQL 5.0.51a |
| 5432 | PostgreSQL | PostgreSQL 8.3.x |
| 5900 | VNC | Protocol 3.3 |
| 6000 | X11 | X11 |
| 6667 | IRC | UnrealIRCd |
| 8009 | AJP | Apache Jserv Protocol 1.3 |
| 8180 | HTTP | Apache Tomcat/Coyote |

---

## Findings Register

| ID | Finding | Severity | Affected Asset | Recommended Fix |
|---|---|---|---|---|
| N-01 | Excessive number of exposed services | High | Multiple TCP ports | Disable unnecessary services and restrict access using firewall rules |
| N-02 | Legacy/unencrypted remote-access protocols | High | Ports 23, 512, 513 | Replace Telnet/rlogin/rexec with secure alternatives such as SSH |
| N-03 | Legacy FTP services | High | Ports 21, 2121 | Disable FTP where possible or replace with SFTP/FTPS |
| N-04 | Outdated database services | High | Ports 3306, 5432 | Upgrade database software and restrict database access |
| N-05 | Legacy web server software | High | Port 80 | Upgrade the web server and remove unsupported components |
| N-06 | Metasploitable root shell service | Critical* | Port 1524 | Disable the service; this is expected in the intentionally vulnerable lab VM |

\* The port 1524 finding is intentionally present on Metasploitable for security-training purposes and should not be treated as evidence of a production compromise.

---

## Risk Prioritization

### Priority 1 — Critical

- Disable the intentionally exposed root shell service on port 1524 when the system is not being used for the lab.

### Priority 2 — High

- Remove unnecessary exposed services.
- Disable Telnet, rlogin and rexec.
- Replace legacy FTP services.
- Upgrade outdated database services.
- Upgrade legacy web-server components.

### Priority 3 — Medium/Low

- Review remaining services individually.
- Apply firewall restrictions.
- Monitor network traffic.
- Maintain regular vulnerability assessments and patch management.

---

## Remediation Roadmap

| Priority | Recommendation | Effort |
|---|---|---|
| 1 | Disable unnecessary services | Easy |
| 2 | Restrict exposed ports with firewall rules | Medium |
| 3 | Replace Telnet/rlogin/rexec with SSH | Medium |
| 4 | Replace insecure FTP with SFTP/FTPS | Medium |
| 5 | Upgrade database software | Hard |
| 6 | Upgrade web server and application components | Medium/Hard |
| 7 | Implement continuous patching and monitoring | Medium |

---

## Screenshots

Add the following screenshots to the `screenshots/` directory:

```text
screenshots/
├── 01_nmap_basic_scan.png
├── 02_nmap_service_os_scan.png
├── 03_nmap_results_file.png
├── 04_wireshark_capture.png
├── 05_http_filter.png
├── 06_dns_filter.png
├── 07_arp_filter.png
├── 08_tcp_analysis.png
├── 09_plaintext_analysis.png
├── 10_nikto_scan_port80.png
└── 11_nikto_scan_port8180.png
```

Use screenshots as evidence for the findings documented in the technical report.

---

## Project Structure

```text
network-security-assessment/
│
├── README.md
├── network_security_assessment.md
├── nmap_results.txt
├── wireshark_capture.pcap
│
└── screenshots/
    ├── 01_nmap_basic_scan.png
    ├── 02_nmap_service_os_scan.png
    ├── 03_nmap_results_file.png
    ├── 04_wireshark_capture.png
    ├── 05_http_filter.png
    ├── 06_dns_filter.png
    ├── 07_arp_filter.png
    ├── 08_tcp_analysis.png
    ├── 09_plaintext_analysis.png
    ├── 10_nikto_scan_port80.png
    └── 11_nikto_scan_port8180.png
```

---

## Executive Summary

A network security assessment was performed against the authorized Metasploitable laboratory system at `10.170.20.105`. Nmap identified a large number of exposed services, including legacy remote-access protocols, FTP, database services, web services, SMB/NFS, VNC and other network services.

The assessment indicates that the laboratory host has a broad attack surface and contains several intentionally outdated or insecure services. These conditions are expected for a Metasploitable training environment but would represent significant security concerns on a production system.

Wireshark traffic analysis and Nikto web-scanning results should be added to the technical report using the actual evidence collected during the assessment.

The main remediation strategy is to minimize the exposed attack surface, disable unnecessary services, replace insecure protocols, upgrade unsupported software, restrict network access, and maintain continuous patching and monitoring.

---

## Conclusion

The assessment successfully demonstrated the process of network reconnaissance, service enumeration, traffic analysis, web scanning, risk classification and remediation planning.

The Metasploitable environment provides multiple intentionally vulnerable services, making it suitable for cybersecurity training. The same exposure should not be accepted in a production environment.

---

## Tools Used

- **Kali Linux**
- **Nmap**
- **Wireshark**
- **Nikto**
- **VirtualBox**
- **Metasploitable**

---

## References

- OWASP Web Security Testing Guide
- Penetration Testing Execution Standard (PTES)
- Common Vulnerability Scoring System (CVSS)
- Nmap Documentation
- Wireshark Documentation
- Nikto Documentation

---

## Internship

**Organization:** Oasis Infobyte  
**Track:** Cyber Security  
**Internship:** Security Analysis Internship  
**Task:** Full Network Security Assessment Report

---

## Author

**Venkatesh P**  
BE Computer Science and Engineering
