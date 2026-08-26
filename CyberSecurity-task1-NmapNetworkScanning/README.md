# Basic Network Scanning with Nmap

## Objective

The objective of this project is to perform network scanning on a local test environment using Nmap, identify active hosts, discover open ports, determine running services and versions, and analyze the possible security risks associated with exposed services.

The scanning was performed in a controlled VirtualBox lab using Kali Linux and a Metasploitable test machine.

## What is Nmap?

Nmap (Network Mapper) is a network scanning and security auditing tool used to discover hosts, open ports, running services, service versions, and operating-system information.

It is widely used by cybersecurity professionals for network discovery, vulnerability assessment, and security auditing.

## Why Network Scanning Matters

Network scanning helps security professionals:

* Discover active devices on a network.
* Identify open and exposed ports.
* Determine which services are running.
* Identify outdated or potentially vulnerable software.
* Understand the attack surface of a system.
* Improve network security by reducing unnecessary services.

## Tools Used

* **Kali Linux** – Security testing operating system
* **Nmap 7.99** – Network scanning and service detection
* **arp-scan** – Local network host discovery
* **VirtualBox** – Virtualization platform
* **Metasploitable** – Intentionally vulnerable test machine

## Lab Network Information

| Device         | IP Address       | Purpose             |
| -------------- | ---------------- | ------------------- |
| Kali Linux     | `192.168.1.6`    | Scanning machine    |
| Metasploitable | `192.168.1.9`    | Target/test machine |
| Network        | `192.168.1.0/24` | Local lab network   |

The `ip addr` command showed that the Kali Linux `eth0` interface had the IP address `192.168.1.6/24`.

## Host Discovery

The following command was used to discover active hosts on the local network:

```bash
sudo arp-scan --interface=eth0 192.168.1.6/24
```

The scan identified three responding hosts:

```text
192.168.1.1
192.168.1.5
192.168.1.9
```

The target selected for further testing was:

```text
192.168.1.9
```

This address was identified as the Metasploitable test machine.

## Nmap Version Check

The installed Nmap version was verified using:

```bash
nmap --version
```

The screenshot shows:

```text
Nmap version 7.99
Platform: x86_64-pc-linux-gnu
```

This confirms that Nmap was correctly installed and available for scanning.

## Commands Used

### 1. Service Version Detection

```bash
nmap -sV 192.168.1.9
```

The `-sV` option attempts to identify the service and version running on each discovered open port.

### 2. Operating System Detection

```bash
sudo nmap -O 192.168.1.9
```

The `-O` option performs operating-system detection.

The scan identified the target as a Linux-based system and reported:

```text
Running: Linux 2.6.X
OS details: Linux 2.6.9 - 2.6.33
```

### 3. Network Discovery

```bash
sudo arp-scan --interface=eth0 192.168.1.6/24
```

This was used to identify active devices before selecting the Metasploitable machine for detailed Nmap scanning.

# Findings

The Nmap service-version scan identified **23 open TCP ports** on `192.168.1.9`.

| Port | Service    | Detected Version                | Security Observation                                              |
| ---: | ---------- | ------------------------------- | ----------------------------------------------------------------- |
|   21 | FTP        | vsftpd 2.3.4                    | FTP is unencrypted and the old version increases security risk    |
|   22 | SSH        | OpenSSH 4.7p1                   | Old SSH version; should be updated and securely configured        |
|   23 | Telnet     | Linux telnetd                   | Sends credentials/data without encryption                         |
|   25 | SMTP       | Postfix smtpd                   | Mail service should be restricted and securely configured         |
|   53 | DNS        | ISC BIND 9.4.2                  | Very old DNS software; unnecessary exposure increases risk        |
|   80 | HTTP       | Apache 2.2.8                    | Old web server version and HTTP traffic is unencrypted            |
|  111 | RPC        | rpcbind                         | Can expose RPC-related services if unnecessarily accessible       |
|  139 | NetBIOS    | Samba 3.x–4.x                   | Network file-sharing service can increase attack surface          |
|  445 | SMB        | Samba 3.x–4.x                   | SMB exposure should be restricted to trusted networks             |
|  512 | rexec      | netkit-rsh                      | Legacy remote execution service; highly insecure                  |
|  513 | rlogin     | OpenBSD/Solaris rlogind         | Legacy remote-login service; insecure                             |
|  514 | shell      | tcpwrapped                      | Legacy remote shell service; unnecessary exposure is risky        |
| 1099 | Java RMI   | GNU Classpath grmiregistry      | Remote Java service increases attack surface                      |
| 1524 | bindshell  | Metasploitable root shell       | Extremely dangerous service; intentionally vulnerable lab service |
| 2049 | NFS        | RPC #100003                     | NFS exposure may reveal shared resources                          |
| 2121 | FTP        | ProFTPD 1.3.1                   | Additional FTP service using a non-standard port                  |
| 3306 | MySQL      | MySQL 5.0.51a                   | Database exposed over the network                                 |
| 5432 | PostgreSQL | PostgreSQL 8.3.x                | Database service exposed and very old                             |
| 5900 | VNC        | VNC protocol 3.3                | Remote graphical access service; requires strong protection       |
| 6000 | X11        | Access denied                   | X11 service detected; network exposure should be restricted       |
| 6667 | IRC        | UnrealIRCd                      | IRC service increases unnecessary attack surface                  |
| 8009 | AJP13      | Apache Jserv Protocol 1.3       | Application-server connector should not be unnecessarily exposed  |
| 8180 | HTTP       | Apache Tomcat/Coyote JSP engine | Web application service exposed on a secondary port               |

## Filtered Findings

For the purpose of the basic Nmap scanning task, the most important information is:

```text
Target: 192.168.1.9
Host Status: Up
Open TCP Ports: 23
Operating System: Linux-based
Target Type: Metasploitable test machine
```

The most significant exposed services include:

```text
21/tcp    FTP
22/tcp    SSH
23/tcp    Telnet
25/tcp    SMTP
53/tcp    DNS
80/tcp    HTTP
139/tcp   SMB/NetBIOS
445/tcp   SMB
3306/tcp  MySQL
5432/tcp  PostgreSQL
5900/tcp  VNC
8009/tcp  AJP13
8180/tcp  Tomcat HTTP
```

The system has a large number of intentionally exposed services because Metasploitable is designed as a vulnerable cybersecurity training environment.

# Security Analysis

### 1. FTP – Port 21

FTP is an unencrypted file-transfer protocol. Usernames, passwords and transferred data may be exposed when FTP is used without additional protection.

**Risk:** Credential and data exposure.

**Recommendation:** Use SFTP or another encrypted file-transfer mechanism and disable FTP when it is not required.

### 2. SSH – Port 22

SSH provides encrypted remote access, but the detected OpenSSH version is very old.

**Risk:** Outdated software may contain known security weaknesses.

**Recommendation:** Upgrade SSH and use strong authentication and key-based login where appropriate.

### 3. Telnet – Port 23

Telnet does not provide encryption for authentication or communication.

**Risk:** Credentials can potentially be captured by an attacker with network visibility.

**Recommendation:** Disable Telnet and use SSH instead.

### 4. HTTP – Port 80

Apache HTTP Server 2.2.8 is an outdated web-server version, and normal HTTP communication is not encrypted.

**Risk:** Old software and unencrypted web traffic increase the attack surface.

**Recommendation:** Upgrade the web server and use HTTPS.

### 5. SMB – Ports 139 and 445

SMB/Samba services are commonly used for file and printer sharing.

**Risk:** Unnecessary SMB exposure can allow unauthorized access attempts and information disclosure.

**Recommendation:** Restrict SMB access to trusted hosts and keep Samba updated.

### 6. Legacy Remote Services – Ports 512, 513 and 514

The `rexec`, `rlogin`, and `shell` services are legacy remote-access mechanisms.

**Risk:** These services are insecure compared with modern encrypted remote-access protocols.

**Recommendation:** Disable these services and use SSH.

### 7. Metasploitable Bindshell – Port 1524

Port 1524 was identified as a `bindshell` and is associated with the intentionally vulnerable Metasploitable environment.

**Risk:** A root shell service represents extremely high risk if exposed on a real network.

**Recommendation:** Never expose such a service outside an isolated training environment.

### 8. NFS – Port 2049

NFS provides network file sharing.

**Risk:** Incorrectly configured NFS exports may expose sensitive files or directories.

**Recommendation:** Restrict NFS access and carefully configure exported directories and permissions.

### 9. MySQL – Port 3306

MySQL is directly accessible through the network.

**Risk:** Exposed database services can become targets for unauthorized authentication attempts and data access.

**Recommendation:** Restrict database access to trusted systems and use strong authentication.

### 10. PostgreSQL – Port 5432

PostgreSQL is also exposed and the detected version is very old.

**Risk:** Outdated database software may contain known vulnerabilities.

**Recommendation:** Upgrade PostgreSQL and restrict network access.

### 11. VNC – Port 5900

VNC provides remote graphical access.

**Risk:** Weak authentication or unrestricted access could allow unauthorized remote control.

**Recommendation:** Restrict VNC access and use secure tunneling or VPN protection.

### 12. Tomcat/AJP – Ports 8009 and 8180

Apache Tomcat-related services are exposed through ports 8009 and 8180.

**Risk:** Exposed application-server interfaces increase the attack surface, particularly when old software is used.

**Recommendation:** Update the application server and restrict AJP access to systems that actually require it.

# Overall Security Assessment

The scan shows that the Metasploitable machine has a **large attack surface**, with 23 open TCP ports and many outdated or legacy services.

The most concerning categories are:

1. **Unencrypted services** – FTP and Telnet.
2. **Legacy remote-access services** – rexec, rlogin and shell.
3. **Outdated software** – several old service versions were detected.
4. **Exposed databases** – MySQL and PostgreSQL.
5. **Remote administration services** – SSH and VNC.
6. **File-sharing services** – SMB and NFS.
7. **Intentionally vulnerable services** – particularly the bindshell on port 1524.
8. **Web/application services** – Apache and Tomcat.

Because the target is Metasploitable, these weaknesses are expected and are useful for cybersecurity training.

# Ethical Use

Nmap should only be used against systems that I own or have explicit permission to test.

In this project, the scanning was performed within a controlled VirtualBox cybersecurity lab using a deliberately vulnerable Metasploitable machine.

Unauthorized scanning of external, production, organizational, or third-party systems should not be performed.

# Conclusion

The Nmap network-scanning exercise successfully demonstrated how to discover active hosts, identify open ports, detect running services and determine service versions.

The local network discovery identified the Metasploitable machine at `192.168.1.9`. Nmap then identified **23 open TCP ports**, including FTP, SSH, Telnet, HTTP, SMB, MySQL, PostgreSQL, VNC, Tomcat and several legacy services.

The results demonstrate how excessive services, outdated software, unencrypted protocols and exposed databases can increase a system's attack surface. This practical exercise improved understanding of network reconnaissance and basic security assessment in a controlled and ethical cybersecurity laboratory.

## Evidence / Screenshots

The following screenshots can be included in the project report:

1. Nmap version verification.
2. Kali Linux `ip addr` output showing `eth0` and `192.168.1.6`.
3. ARP scan showing active hosts including `192.168.1.9`.
4. Nmap basic scan of `192.168.1.9`.
5. Nmap OS detection of the Metasploitable machine.
6. Nmap service-version scan showing the detected services and versions.

