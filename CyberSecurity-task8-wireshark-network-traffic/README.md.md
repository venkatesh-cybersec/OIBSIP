# TASK 8 — Capture Network Traffic with Wireshark

## Objective

Capture live network traffic from a controlled Metasploitable lab environment using Wireshark, apply protocol filters, analyse TCP/DNS/HTTP packets, identify unencrypted HTTP data, and document the security implications.

> **Ethics / Safety:** This capture was performed only in my own isolated VirtualBox lab using Kali Linux and Metasploitable 2. Do not capture traffic from public Wi-Fi, university networks, or any network without explicit authorization.

## Lab Environment

| Component | Details |
|---|---|
| Analysis VM | Kali Linux |
| Vulnerable Test VM | Metasploitable 2 |
| Virtualization | Oracle VirtualBox |
| Network | Isolated Host-Only lab network |
| Metasploitable IP | `10.170.20.105` |
| Kali IP | `10.170.20.251` |
| Wireshark | 4.6.6 |

The Metasploitable web application was accessed from Kali Linux using `http://10.170.20.105`.

## 1. Wireshark Installation and Permissions

Wireshark was installed on Kali Linux and opened for packet capture. Packet capture requires appropriate permission to access the selected network interface. The capture was performed on the active interfaces shown by Wireshark (`eth0` and `eth1`).

## 2. Live Traffic Capture

Wireshark was started and live traffic was captured while generating traffic between Kali Linux and the Metasploitable VM.

### Screenshot — Live Capture

![Live Capture](screenshots/01_live_capture.png)

**Observation:** Wireshark successfully captured live packets. The packet list contains different protocols generated during network activity.

## 3. HTTP Traffic Analysis

Display filter used:

```text
http
```

### Screenshot — HTTP Filter

![HTTP Filter](screenshots/02_http_filter.png)

The filtered packets show HTTP communication between Kali Linux (`10.170.20.251`) and Metasploitable (`10.170.20.105`). The screenshot shows an HTTP `GET` request and an `HTTP/1.1 200 OK` response.

**Security observation:** HTTP is unencrypted, so request and response data can potentially be read by someone who can observe the traffic.

## 4. DNS Traffic Analysis

Display filter used:

```text
dns
```

### Screenshot — DNS Filter

![DNS Filter](screenshots/03_dns_filter.png)

The capture shows DNS queries and responses generated while browsing, including domain-name lookups.

**Security observation:** Traditional DNS is generally unencrypted. A network observer may be able to see which domain names are being queried, even when the web session itself uses HTTPS.

## 5. TCP Traffic and Three-Way Handshake

Display filter used:

```text
tcp
```

### Screenshot — TCP Traffic

![TCP Traffic](screenshots/04_tcp_handshake.png)

TCP connection establishment uses a three-way handshake:

1. **SYN** — client requests a TCP connection.
2. **SYN-ACK** — server acknowledges the request and sends its SYN.
3. **ACK** — client acknowledges the server response.

In Wireshark, the handshake can be identified from the **Info** column as `[SYN]`, `[SYN, ACK]`, and `[ACK]` packets with matching source/destination and sequence/acknowledgment values.

> **Note:** The provided TCP screenshot contains TCP connection traffic including FIN/RST packets. For the final report, the exact SYN → SYN-ACK → ACK rows should be selected in the capture and annotated if the screenshot is replaced.

## 6. Unencrypted HTTP Data

A packet containing an HTTP request was inspected in the packet details/bytes view.

### Screenshot — Unencrypted HTTP Packet

![Unencrypted HTTP](screenshots/05_unencrypted-http.png)

The packet visibly contains:

```text
GET /dvwa/login.php
```

The HTTP form data is readable in the packet payload, including:

```text
username=admin&password=password&Login=Login
```

A PHP session cookie is also visible in the HTTP headers.

### Why This Is Dangerous

Because HTTP sends data without encryption, a network observer with access to the traffic may be able to read:

- Requested URLs
- HTTP headers
- Form parameters
- Usernames and passwords
- Session cookies
- Other application data

In a real environment, this could lead to credential theft or session hijacking. The credentials shown here were used only in the intentionally vulnerable Metasploitable/DVWA training lab.

## 7. HTTP vs HTTPS

### HTTP

HTTP sends application data in plaintext over the network. If the traffic can be captured, sensitive information may be readable.

### HTTPS

HTTPS uses TLS encryption to protect HTTP traffic between the client and server. It provides confidentiality, integrity, and server authentication through TLS certificates.

Therefore, HTTPS significantly reduces the risk of network eavesdropping compared with unencrypted HTTP.

## 8. Security Findings

| Finding | Risk | Observation |
|---|---|---|
| Unencrypted HTTP | High | HTTP request data was readable in Wireshark |
| Credentials in HTTP request | High | Username/password parameters were visible |
| Session cookie in HTTP | High | PHP session information was visible |
| Plain DNS queries | Medium | Queried domain names can be observable |
| TCP traffic visible | Informational | IPs, ports and connection metadata are visible |

### Recommendations

1. Use HTTPS/TLS instead of HTTP.
2. Never transmit passwords over plaintext HTTP.
3. Protect session cookies with appropriate `Secure` and `HttpOnly` attributes.
4. Use secure DNS options where privacy is required.
5. Keep vulnerable training VMs isolated from untrusted networks.
6. Disable unnecessary services and keep systems patched in real environments.

## 9. Capture File

Export the capture from Wireshark as:

```text
wireshark_capture.pcap
```

Recommended repository structure:

```text
wireshark-network-traffic/
├── README.md
├── wireshark_capture.pcap
└── screenshots/
    ├── 01_live_capture.png
    ├── 02_http_filter.png
    ├── 03_dns_filter.png
    ├── 04_tcp_handshake.png
    └── 05_unencrypted-http.png
```

In Wireshark: **File → Save As → `wireshark_capture.pcap`** and then add it to the GitHub repository.

## 10. Glossary

### Packet
A small unit of data transmitted across a network. It contains control information such as addressing details and may contain application data.

### Protocol
A defined set of rules that devices use to communicate with each other. Examples include TCP, DNS and HTTP.

### Port
A numbered endpoint used to identify a network service or application. HTTP commonly uses TCP port `80`.

### Payload
The useful data carried inside a packet, excluding the protocol headers.

### Handshake
A sequence of messages used by communicating systems to establish or agree on a connection. TCP uses SYN, SYN-ACK and ACK to establish a connection.

## Conclusion

Wireshark successfully captured and analysed live traffic from the isolated Kali Linux and Metasploitable lab environment. The HTTP filter demonstrated readable HTTP traffic, while the DNS filter showed DNS queries and responses. TCP traffic was analysed to understand connection establishment and TCP control packets.

The main security finding was the visibility of unencrypted HTTP request data. The captured DVWA login request demonstrated why sensitive information should never be transmitted using plaintext HTTP. HTTPS/TLS should be used to protect application data from network eavesdropping.
