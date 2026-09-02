# Network Security Assessment Report

## 1. Assessment Overview

**Assessment Type:** Network Security Assessment  
**Target:** `10.170.20.105`  
**Target Environment:** Metasploitable lab machine  
**Assessment Purpose:** Identify exposed services, gather service/version information, analyse network traffic, and document security findings in an authorized test environment.

> **Authorization:** This assessment is intended for the user's own isolated lab environment (Metasploitable/VirtualBox). Do not use these techniques against systems without authorization.

---

## 2. Assessment Scope

### In Scope
- **Target IP:** `10.170.20.105`
- **Network:** Local/VirtualBox lab network
- **Services:** TCP services exposed by the target
- **Network Traffic:** HTTP, DNS, ARP and TCP traffic generated/observed during the lab capture
- **Web Application:** HTTP services running on the target, if available

### Time Window
- **Nmap Scan:** 02 September 2026, 05:16:55–05:17:09
- **Wireshark Capture:** 5+ minutes required by the task; enter the actual capture start/end time below.
- **Nikto Scan:** Enter the actual scan date/time below.

---

# 3. Executive Summary

A network security assessment was performed against the authorized Metasploitable lab host `10.170.20.105`.

The Nmap service/version and operating-system scan confirmed that the host is reachable and exposes a large number of TCP services. The scan identified services including FTP, SSH, Telnet, SMTP, DNS, HTTP, SMB, remote login services, databases, VNC, IRC, Apache AJP and Apache Tomcat.

Several services use old software versions or insecure/legacy protocols. In particular, Telnet, FTP, rsh/rlogin and other legacy services can expose authentication or communication data without modern encryption. The scan also identified a Metasploitable root shell service on TCP port 1524, which is intentionally vulnerable and should only exist in an isolated training environment.

The overall security posture of this lab host is **high risk by design**, which is expected for Metasploitable. In a production environment, unnecessary services should be disabled, legacy protocols should be removed or replaced, software should be patched, and access should be restricted using firewall/network segmentation controls.

> **Important:** Wireshark and Nikto findings in this report must be updated with the actual results from the user's capture and web vulnerability scan. No unverified findings are claimed here.

---

# 4. Methodology

The assessment followed these phases:

1. **Reconnaissance / Network Scanning**
   - Nmap host discovery
   - Service and version detection
   - Operating-system detection

2. **Traffic Analysis**
   - Wireshark packet capture
   - HTTP analysis
   - DNS analysis
   - ARP analysis
   - TCP analysis
   - Plaintext/unencrypted data review

3. **Web Vulnerability Assessment**
   - Nikto scan against discovered web services
   - Review of server configuration and known web-server issues

4. **Risk Assessment**
   - Findings classified as Critical, High, Medium, Low or Informational
   - Remediation recommendations provided

---

# 5. Phase 1 — Nmap Reconnaissance

## 5.1 Command Used

```bash
sudo nmap -sV -O 10.170.20.105
```

The results were saved using:

```bash
sudo nmap -sV -O 10.170.20.105 -oN nmap_result_task10.txt
```

## 5.2 Host Information

| Item | Result |
|---|---|
| Target IP | `10.170.20.105` |
| Host Status | Up |
| Network Distance | 1 hop |
| Device Type | General purpose |
| OS Family | Linux |
| Kernel Range | Linux 2.6.X |
| OS Details | Linux 2.6.9 – 2.6.33 |
| MAC Address | `08:00:27:23:28:EF` |
| Virtual NIC | Oracle VirtualBox |

## 5.3 Discovered Open Services

| Port | Service | Version / Details | Security Observation |
|---:|---|---|---|
| 21 | FTP | vsftpd 2.3.4 | Legacy FTP; credentials/data may be unencrypted |
| 22 | SSH | OpenSSH 4.7p1 | Very old SSH version |
| 23 | Telnet | Linux telnetd | Unencrypted remote administration |
| 25 | SMTP | Postfix smtpd | Mail service exposed |
| 53 | DNS | ISC BIND 9.4.2 | Very old DNS version |
| 80 | HTTP | Apache 2.2.8 (Ubuntu), DAV/2 | Legacy web server |
| 111 | rpcbind | RPC #100000 | RPC service exposed |
| 139 | NetBIOS | Samba 3.X–4.X | SMB/NetBIOS exposed |
| 445 | SMB | Samba 3.X–4.X | SMB service exposed |
| 512 | exec | netkit-rsh rexecd | Legacy remote execution service |
| 513 | login | rlogind | Legacy remote login service |
| 514 | tcpwrapped | TCP wrapped | Service requires further identification |
| 1099 | Java RMI | GNU Classpath grmiregistry | Java RMI exposed |
| 1524 | bindshell | Metasploitable root shell | Critical exposure in non-lab environments |
| 2049 | NFS | NFS v2–4 | Network file service exposed |
| 2121 | FTP | ProFTPD 1.3.1 | Legacy FTP service |
| 3306 | MySQL | MySQL 5.0.51a-3ubuntu5 | Very old database version |
| 5432 | PostgreSQL | PostgreSQL 8.3.0–8.3.7 | Very old database version |
| 5900 | VNC | Protocol 3.3 | Remote graphical access exposed |
| 6000 | X11 | Access denied | X11 service exposed |
| 6667 | IRC | UnrealIRCd | IRC service exposed |
| 8009 | AJP | Apache Jserv Protocol v1.3 | AJP service exposed |
| 8180 | HTTP | Apache Tomcat/Coyote JSP engine 1.1 | Web application service exposed |

---

# 6. Nmap Findings Analysis

### Finding N-01 — Excessive Exposed Services

**Severity:** High  
**Affected Asset:** `10.170.20.105`

The host exposes numerous network services, including remote administration, file sharing, databases, web services and legacy protocols.

**Risk:** A large attack surface increases the number of services that could be misconfigured, vulnerable or abused.

**Recommendation:**
- Disable unnecessary services.
- Restrict administrative services to trusted hosts.
- Use host-based and network firewalls.
- Segment critical services from untrusted networks.

---

### Finding N-02 — Legacy / Unencrypted Remote Access Protocols

**Severity:** High  
**Affected Ports:** `23`, `512`, `513`

Telnet, rsh and rlogin are legacy protocols that do not provide modern secure communication.

**Risk:** Authentication information and session data may be exposed to network interception.

**Recommendation:**
- Disable Telnet, rsh and rlogin.
- Use SSH for secure remote administration.
- Restrict SSH access with firewall rules and strong authentication.

---

### Finding N-03 — Legacy FTP Services

**Severity:** High  
**Affected Ports:** `21`, `2121`

FTP services were identified using vsftpd 2.3.4 and ProFTPD 1.3.1.

**Risk:** Standard FTP does not encrypt credentials or transferred data. Old FTP software may also contain known security weaknesses.

**Recommendation:**
- Disable FTP where it is not required.
- Prefer SFTP over SSH or another securely configured file-transfer mechanism.
- Upgrade supported software to maintained versions.

---

### Finding N-04 — Outdated Database Services

**Severity:** High  
**Affected Ports:** `3306`, `5432`

The scan identified MySQL 5.0.51a and PostgreSQL 8.3.x.

**Risk:** These versions are obsolete and may contain known vulnerabilities. Exposing database ports directly also increases attack surface.

**Recommendation:**
- Upgrade to currently supported database versions.
- Restrict database access to application servers or trusted administration hosts.
- Require strong authentication.
- Do not expose database ports to untrusted networks.

---

### Finding N-05 — Legacy Web Server

**Severity:** High  
**Affected Port:** `80`

Apache HTTP Server 2.2.8 was identified.

**Risk:** The version is obsolete and may contain known vulnerabilities.

**Recommendation:**
- Upgrade to a currently supported Apache release.
- Remove unnecessary modules.
- Enforce secure server configuration.
- Use HTTPS for sensitive communication.

---

### Finding N-06 — Metasploitable Root Shell

**Severity:** Critical  
**Affected Port:** `1524`

Nmap identified:

`1524/tcp open bindshell Metasploitable root shell`

**Risk:** A remotely accessible root shell would provide complete system compromise if exposed on a real system.

**Recommendation:**
- Remove/disable the service immediately on production systems.
- Restrict access using firewall rules.
- Never expose such intentionally vulnerable lab services to the public Internet.

**Lab Note:** This finding is expected on Metasploitable because it is intentionally designed as a vulnerable training platform.

---

# 7. Phase 2 — Wireshark Traffic Analysis

## 7.1 Capture Details

**Capture File:** `wireshark_capture.pcap`

**Required Capture Duration:** At least 5 minutes

**Actual Capture Duration:** `ENTER ACTUAL DURATION`

**Interface Used:** `ENTER INTERFACE`

## 7.2 HTTP Analysis

Wireshark display filter:

```text
http
```

### Observations
- HTTP traffic was reviewed for requests and responses.
- Source and destination IP addresses were checked.
- HTTP methods and requested resources were examined.
- The traffic was reviewed for readable plaintext or sensitive information.

**Actual Finding:** `ENTER YOUR ACTUAL HTTP OBSERVATION`

## 7.3 DNS Analysis

Wireshark display filter:

```text
dns
```

### Observations
- DNS queries and responses were inspected.
- Requested domain names were identified.
- Source and destination addresses were reviewed.

**Actual Finding:** `ENTER YOUR ACTUAL DNS OBSERVATION`

## 7.4 ARP Analysis

Wireshark display filter:

```text
arp
```

### Observations
ARP request/reply traffic was examined to understand local IP-to-MAC address resolution.

**Actual Finding:** `ENTER YOUR ACTUAL ARP OBSERVATION`

## 7.5 TCP Analysis

Wireshark display filter:

```text
tcp
```

The TCP three-way handshake was reviewed:

```text
SYN
   ↓
SYN + ACK
   ↓
ACK
```

**Actual Finding:** `ENTER YOUR ACTUAL TCP OBSERVATION`

## 7.6 Plaintext / Unencrypted Data

HTTP and other non-encrypted traffic were reviewed for readable sensitive information.

To inspect a TCP conversation:

**Wireshark → Right-click packet → Follow → TCP Stream**

**Actual Observation:**

`ENTER ONE OF THE FOLLOWING BASED ON YOUR CAPTURE:`

- `Sensitive information was observed in plaintext HTTP traffic.`
- `No sensitive information was observed in plaintext during the captured traffic.`

> Do not claim that usernames, passwords or other sensitive information were exposed unless they are actually visible in your packet capture.

---

# 8. Phase 3 — Nikto Web Vulnerability Scan

## 8.1 Web Services Identified

Nmap identified HTTP services on:

- `80/tcp`
- `8180/tcp`

## 8.2 Commands

For port 80:

```bash
nikto -h http://10.170.20.105
```

For port 8180:

```bash
nikto -h http://10.170.20.105:8180
```

## 8.3 Nikto Results

**Port 80 Findings:**  
`PASTE ACTUAL NIKTO RESULTS HERE`

**Port 8180 Findings:**  
`PASTE ACTUAL NIKTO RESULTS HERE`

**Screenshots:**  
Add screenshots of the actual Nikto output to the GitHub repository.

---

# 9. Findings Register

| ID | Finding | Severity | Affected Asset | Recommended Fix |
|---|---|---|---|---|
| N-01 | Excessive exposed services | High | 10.170.20.105 | Disable unnecessary services and restrict access |
| N-02 | Telnet/rsh/rlogin legacy protocols | High | Ports 23, 512, 513 | Disable and use SSH |
| N-03 | Legacy FTP services | High | Ports 21, 2121 | Disable FTP or migrate to secure transfer |
| N-04 | Outdated MySQL/PostgreSQL | High | Ports 3306, 5432 | Upgrade and restrict database access |
| N-05 | Outdated Apache HTTP server | High | Port 80 | Upgrade and harden web server |
| N-06 | Metasploitable root shell | Critical | Port 1524 | Disable/remove; isolate lab system |
| W-01 | Wireshark plaintext observation | `ENTER` | Network traffic | Use HTTPS/SSH/TLS and avoid plaintext protocols |
| W-02 | Nikto web finding | `ENTER` | Web server | Apply the remediation reported by Nikto |

---

# 10. Risk Prioritization

## Critical
- Metasploitable root shell on TCP 1524.

## High
- Excessive exposed services.
- Telnet/rsh/rlogin.
- Legacy FTP.
- Outdated database services.
- Outdated Apache HTTP server.

## Medium / Low / Informational
- Add verified Wireshark and Nikto findings here after reviewing the actual evidence.

---

# 11. Remediation Roadmap

| Priority | Remediation | Effort |
|---|---|---|
| 1 | Remove/disable unintended root shell and isolate vulnerable lab services | Easy |
| 2 | Disable Telnet, rsh and rlogin | Easy |
| 3 | Restrict database and administrative ports with firewall rules | Medium |
| 4 | Replace FTP with SFTP/secure transfer | Medium |
| 5 | Upgrade MySQL, PostgreSQL and Apache to supported versions | Medium |
| 6 | Review and harden SMB, NFS, VNC and other exposed services | Medium |
| 7 | Remediate verified Nikto findings | Medium/Hard depending on finding |
| 8 | Enforce encrypted protocols such as HTTPS and SSH | Medium |

---

# 12. Evidence / Screenshots

Add the following screenshots to the GitHub repository:

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

---

# 13. Conclusion

The assessment of the authorized Metasploitable host `10.170.20.105` identified a broad attack surface with numerous exposed services, including several obsolete or insecure protocols and software versions.

The most serious Nmap finding is the exposed Metasploitable root shell on TCP port 1524. Legacy remote-access services such as Telnet, rsh and rlogin, along with outdated FTP, database and web services, also increase the risk.

Because Metasploitable is intentionally vulnerable, these findings are expected in the training environment. In a production environment, the recommended approach would be to minimize exposed services, remove legacy protocols, patch software, restrict network access, and enforce encryption.

The Wireshark and Nikto sections should be finalized using the actual capture and scan evidence before submission.

---

# 14. Tools Used

- Nmap
- Wireshark
- Nikto
- Kali Linux
- Metasploitable
- VirtualBox

---

# 15. References

- OWASP Web Security Testing Guide
- Penetration Testing Execution Standard (PTES)
- Common Vulnerability Scoring System (CVSS)
- Nmap documentation
- Wireshark documentation
- Nikto documentation

---

## Submission Files

```text
network-security-assessment/
├── README.md
├── network_security_assessment.md
├── nmap_results.txt
├── wireshark_capture.pcap
└── screenshots/
```
