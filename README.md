# OSI Model Assignment

**Topic:** The 7 Layers of the OSI Model, their functions, common vulnerabilities/attacks, and defensive measures.

---

## 1. Introduction

The **Open Systems Interconnection (OSI) model** is a conceptual framework created by the International Organization for Standardization (ISO) that describes how data moves between two devices on a network. It splits communication into **seven layers**, each with a specific job. Each layer serves the layer above it and is served by the layer below it.

Understanding the layers helps security professionals pinpoint *where* an attack happens and *which* control is appropriate to stop it.

| # | Layer | Data Unit | Example Protocols / Devices |
|---|-------|-----------|-----------------------------|
| 7 | Application | Data | HTTP, HTTPS, FTP, SMTP, DNS |
| 6 | Presentation | Data | TLS/SSL, JPEG, ASCII, MPEG |
| 5 | Session | Data | NetBIOS, RPC, PPTP, SIP |
| 4 | Transport | Segment / Datagram | TCP, UDP |
| 3 | Network | Packet | IP, ICMP, OSPF, BGP, Routers |
| 2 | Data Link | Frame | Ethernet, ARP, PPP, Switches |
| 1 | Physical | Bits | Cables, Hubs, NICs, Wi-Fi radio |

> Mnemonic (top to bottom): **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing.

---

## 2. Layer 7 – Application Layer

**Purpose / Function:**
The layer closest to the end user. It provides network services directly to applications such as web browsers, email clients, and file-transfer tools. It defines how applications request and receive network resources (e.g., HTTP for web pages, SMTP for email, DNS for name resolution).

| # | Vulnerability / Attack | Description | Defensive Measure(s) |
|---|------------------------|-------------|----------------------|
| 1 | **SQL Injection (SQLi)** | Attacker inserts malicious SQL into input fields to read, modify, or delete database data. | Parameterized queries / prepared statements, input validation, least-privilege DB accounts, Web Application Firewall (WAF). |
| 2 | **Cross-Site Scripting (XSS)** | Malicious scripts are injected into web pages and run in victims' browsers (session theft, defacement). | Output encoding/escaping, Content Security Policy (CSP), input sanitization, `HttpOnly` cookies. |
| 3 | **Cross-Site Request Forgery (CSRF)** | Tricks an authenticated user's browser into sending unwanted requests to a trusted site. | Anti-CSRF tokens, `SameSite` cookie attribute, re-authentication for sensitive actions. |
| 4 | **DNS Spoofing / Cache Poisoning** | Forged DNS responses redirect users to malicious sites. | DNSSEC, randomized source ports and query IDs, trusted/validated resolvers, DNS over HTTPS/TLS. |
| 5 | **HTTP Flood / Application-Layer DDoS** | Large volumes of legitimate-looking requests exhaust server resources. | Rate limiting, CDN and WAF, CAPTCHA/bot detection, autoscaling. |
| 6 | **Credential Stuffing / Brute Force** | Automated login attempts using leaked or guessed credentials. | Multi-factor authentication (MFA), account lockout/throttling, breached-password checks, strong password policy. |

---

## 3. Layer 6 – Presentation Layer

**Purpose / Function:**
Translates data between the application and the network. It handles **data formatting, character encoding, compression, and encryption/decryption** so that data sent by one system can be understood by another (e.g., ASCII/Unicode conversion, JPEG/MPEG encoding, TLS encryption).

| # | Vulnerability / Attack | Description | Defensive Measure(s) |
|---|------------------------|-------------|----------------------|
| 1 | **SSL Stripping** | Attacker downgrades an HTTPS connection to HTTP to read traffic in plaintext. | HSTS (HTTP Strict Transport Security) with preload, enforce HTTPS redirects, secure-by-default browsers. |
| 2 | **Protocol Downgrade / Weak Cipher Attacks (POODLE, BEAST)** | Forces use of outdated SSL/TLS versions or weak ciphers that can be broken. | Disable SSL 2.0/3.0 and TLS 1.0/1.1, use TLS 1.2+/1.3, restrict to strong cipher suites. |
| 3 | **Heartbleed-style Implementation Flaws** | Bug in a TLS library leaks memory contents (private keys, passwords). | Patch and update crypto libraries promptly, rotate keys/certificates after exposure, vulnerability scanning. |
| 4 | **Insecure Deserialization** | Untrusted serialized data is processed, leading to remote code execution or tampering. | Avoid deserializing untrusted data, use integrity checks (signatures), prefer safe formats (e.g., JSON with schema validation). |
| 5 | **Malicious/Malformed File Format Exploits** | Specially crafted files (images, documents, media) exploit parser bugs such as buffer overflows. | Strict input validation, sandboxed parsing, keep codecs/parsers patched, fuzz testing, antivirus/content scanning. |
| 6 | **Rogue / Forged Certificates (Man-in-the-Middle)** | Fraudulent or stolen certificates let attackers impersonate trusted sites. | Certificate pinning, Certificate Transparency monitoring, proper chain validation, short-lived certificates, OCSP stapling. |
| 7 | **Compression Side-Channel Attacks (CRIME/BREACH)** | Attackers infer secrets by observing the size of compressed encrypted data. | Disable TLS-level compression, mask/randomize secrets (e.g., CSRF tokens) per response, rate limiting. |

---

## 4. Layer 5 – Session Layer

**Purpose / Function:**
Establishes, manages, and terminates **sessions (dialogues)** between applications. It handles session setup, synchronization/checkpointing, authentication, and graceful or abrupt session closing (e.g., NetBIOS, RPC, SIP sessions, web login sessions).

| # | Vulnerability / Attack | Description | Defensive Measure(s) |
|---|------------------------|-------------|----------------------|
| 1 | **Session Hijacking** | Attacker steals a valid session token and takes over a user's session. | Encrypt traffic with TLS, `Secure` and `HttpOnly` cookie flags, bind sessions to additional context, short session lifetimes. |
| 2 | **Session Fixation** | Attacker forces a victim to use a known session ID, then reuses it after login. | Regenerate the session ID after login/privilege change, reject externally supplied IDs. |
| 3 | **Predictable Session IDs / Session Prediction** | Weakly generated IDs can be guessed or brute-forced. | Generate IDs with a cryptographically secure random generator and sufficient length (128+ bits). |
| 4 | **Replay Attack** | Captured valid session/authentication messages are re-sent to gain access. | Nonces and timestamps, one-time tokens, sequence numbers, use of Kerberos-style ticket validation, TLS. |
| 5 | **Session Sniffing (Sidejacking)** | Session cookies are captured on unencrypted or open Wi-Fi networks. | Enforce HTTPS everywhere, HSTS, VPN on untrusted networks. |
| 6 | **NetBIOS / RPC Null Session Abuse** | Anonymous sessions enumerate users, shares, and system data. | Disable null sessions, block SMB/NetBIOS at the perimeter, restrict anonymous access via policy, patch systems. |
| 7 | **Missing Session Timeout / Improper Logout** | Idle or non-invalidated sessions can be reused by others. | Idle and absolute timeouts, server-side session invalidation on logout, re-authentication for sensitive actions. |

---

## 5. Layer 4 – Transport Layer

**Purpose / Function:**
Provides **end-to-end communication** between hosts. It handles segmentation and reassembly, **port addressing**, flow control, error detection/recovery, and reliability (TCP) or low-latency delivery (UDP).

| # | Vulnerability / Attack | Description | Defensive Measure(s) |
|---|------------------------|-------------|----------------------|
| 1 | **SYN Flood** | Flood of TCP SYN packets leaves half-open connections, exhausting server resources. | SYN cookies, connection rate limiting, shorter SYN timeouts, firewall/IPS and DDoS mitigation services. |
| 2 | **UDP Flood** | Large volume of UDP packets overwhelms a host or network. | Rate limiting, filtering unused UDP ports, DDoS scrubbing, firewall rules. |
| 3 | **TCP Session Hijacking / Sequence Prediction** | Attacker predicts sequence numbers to inject packets into a TCP session. | Randomized initial sequence numbers, encryption (TLS/IPsec), ingress filtering. |
| 4 | **TCP Reset (RST) Injection** | Forged RST packets terminate legitimate connections. | Validate RST sequence numbers (RFC 5961), use encrypted/authenticated transport (TLS, TCP-AO), IPS monitoring. |
| 5 | **Port Scanning / Reconnaissance** | Attacker maps open ports and services (e.g., with Nmap) to find targets. | Firewalls with default-deny, close unused ports, IDS/IPS scan detection, port-knocking where appropriate. |
| 6 | **UDP Amplification / Reflection (DNS, NTP, memcached)** | Spoofed requests cause servers to send large responses to a victim. | Disable open resolvers and unneeded services, response rate limiting, BCP 38 anti-spoofing filters, patch/limit NTP & memcached. |
| 7 | **Fragmentation / Evasion at Transport Level** | Crafted segments bypass firewalls/IDS inspection. | Stateful inspection with proper reassembly, normalize traffic, keep IDS/IPS signatures updated. |

---

## 6. Layer 3 – Network Layer

**Purpose / Function:**
Handles **logical addressing (IP addresses)** and **routing** of packets across different networks. It determines the best path from source to destination and manages packet forwarding and fragmentation. Routers operate at this layer.

| # | Vulnerability / Attack | Description | Defensive Measure(s) |
|---|------------------------|-------------|----------------------|
| 1 | **IP Spoofing** | Attacker forges the source IP address to hide identity or impersonate a host. | Ingress/egress filtering (BCP 38), Unicast Reverse Path Forwarding (uRPF), IPsec, authentication beyond IP address. |
| 2 | **ICMP Flood / Ping of Death / Smurf Attack** | Abuse of ICMP to overwhelm hosts or crash systems with oversized/amplified packets. | Rate limit/filter ICMP, disable directed broadcasts, patch OS, firewall rules. |
| 3 | **BGP Hijacking / Route Leaks** | False route announcements divert internet traffic through an attacker's network. | RPKI route origin validation, prefix filtering and max-prefix limits, BGP session authentication (MD5/TCP-AO), monitoring. |
| 4 | **Routing Table Poisoning (RIP/OSPF Manipulation)** | Fake routing updates corrupt routing tables, causing blackholes or interception. | Routing protocol authentication, passive interfaces, route filtering, secure router configuration. |
| 5 | **IP Fragmentation Attacks (Teardrop, Overlapping Fragments)** | Malformed fragments crash systems or evade security devices. | Patch systems, drop malformed/overlapping fragments, enforce reassembly limits, IDS/IPS normalization. |
| 6 | **ICMP Redirect / Router Advertisement Spoofing** | Fake ICMP redirects or RAs reroute traffic via the attacker (MITM). | Disable ICMP redirects, RA Guard (IPv6), static routes where feasible, firewall filtering. |
| 7 | **Man-in-the-Middle via Rogue Routing / Packet Sniffing** | Traffic crossing untrusted networks is intercepted. | VPN/IPsec tunnels, encryption end-to-end, network segmentation. |

---

## 7. Layer 2 – Data Link Layer

**Purpose / Function:**
Provides **node-to-node data transfer** within the same local network. It handles **MAC (physical) addressing**, framing, error detection (CRC), and access control to the medium. It has two sublayers: **LLC** (Logical Link Control) and **MAC** (Media Access Control). Switches and NICs operate here.

| # | Vulnerability / Attack | Description | Defensive Measure(s) |
|---|------------------------|-------------|----------------------|
| 1 | **ARP Spoofing / Poisoning** | False ARP replies map the attacker's MAC to a legitimate IP, enabling MITM. | Dynamic ARP Inspection (DAI), static ARP entries for critical hosts, DHCP snooping, ARP monitoring tools. |
| 2 | **MAC Flooding (CAM Table Overflow)** | Switch is flooded with fake MACs and begins broadcasting traffic, allowing sniffing. | Port security (limit MACs per port), sticky MAC, 802.1X authentication. |
| 3 | **MAC Spoofing** | Attacker clones an authorized MAC address to bypass filtering. | 802.1X/NAC, port security, monitoring for duplicate MACs. |
| 4 | **VLAN Hopping (Switch Spoofing / Double Tagging)** | Attacker gains access to other VLANs by abusing trunking or tagging. | Disable DTP, set ports to static access mode, change native VLAN to unused ID, prune unused VLANs. |
| 5 | **Spanning Tree Protocol (STP) Manipulation** | Rogue switch becomes root bridge to intercept or disrupt traffic. | BPDU Guard, Root Guard, disable unused ports, STP authentication where supported. |
| 6 | **DHCP Starvation / Rogue DHCP Server** | Exhausts address pool or hands out malicious gateway/DNS settings. | DHCP snooping with trusted ports, port security, rate limiting DHCP requests. |
| 7 | **Evil Twin / Rogue Access Point (Wi-Fi)** | Fake AP imitates a legitimate SSID to capture frames and credentials. | WPA3/WPA2-Enterprise with certificate validation, wireless IPS, rogue AP detection, user awareness, VPN. |

---

## 8. Layer 1 – Physical Layer

**Purpose / Function:**
Responsible for the **physical transmission of raw bits** over a medium. It defines cabling, connectors, voltages, signal timing, radio frequencies, and data rates. Devices include cables (copper, fiber), hubs, repeaters, network interface hardware, and wireless radios.

| # | Vulnerability / Attack | Description | Defensive Measure(s) |
|---|------------------------|-------------|----------------------|
| 1 | **Wiretapping / Cable Tapping** | Physical taps on cables capture transmitted signals. | Fiber optics, locked conduits/cabinets, tamper-evident seals, encryption of data in transit, regular cable inspection. |
| 2 | **Electromagnetic Eavesdropping (TEMPEST)** | Emissions from cables or devices are captured and decoded remotely. | Shielded cabling (STP), Faraday cages, TEMPEST-certified equipment, physical distance controls. |
| 3 | **Unauthorized Physical Access / Device Theft** | Intruders access servers, switches, or ports directly, or steal hardware. | Access control (badges, biometrics), CCTV, locked server rooms/racks, visitor logs, full-disk encryption. |
| 4 | **Rogue Device / Hardware Implants** | Malicious devices (keyloggers, implants, rogue switches) are plugged into the network. | Disable/lock unused ports, 802.1X/NAC, port security, regular physical audits, asset inventory. |
| 5 | **Signal Jamming (Wireless DoS)** | Radio interference disrupts wireless communication. | Spread-spectrum/frequency hopping, wired fallback links, jammer detection, site surveys. |
| 6 | **Cable Cutting / Physical Sabotage** | Deliberate or accidental destruction of cables and equipment causes outages. | Redundant paths and links, protected/underground cabling, monitoring and alerting, disaster recovery plans. |
| 7 | **Environmental & Power Threats** | Fire, flooding, overheating, or power loss damage equipment. | UPS and generators, HVAC and environmental sensors, fire suppression, off-site backups. |

---

## 9. Key Takeaways

- Security must be applied in **layers** (defense in depth) because an attack at one layer can bypass controls placed at another.
- Lower layers (1–3) are often protected with **physical controls, segmentation, and network device hardening**; upper layers (4–7) rely more on **encryption, authentication, secure coding, and application firewalls**.
- Patch management, least privilege, monitoring/logging, and user awareness help at **every** layer.

---

## 10. References

- ISO/IEC 7498-1: Information technology – Open Systems Interconnection – Basic Reference Model
- OWASP Top 10 – https://owasp.org/www-project-top-ten/
- NIST SP 800-52 (TLS Guidelines) and NIST SP 800-41 (Firewall Guidelines)
- Cloudflare Learning Center – What is the OSI Model? – https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/
- MITRE ATT&CK – https://attack.mitre.org/

---

*Prepared as part of the OSI Model Assignment.*
