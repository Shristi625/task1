Cyber Security Task 1 — Local Network Port Scan

Date of scan: 2025-10-14
Author: Shristi Gupta

Objective

Scan the local network to discover open ports, identify running services, and evaluate potential security risks. This lab is for educational purposes only—always obtain authorization before scanning networks you do not own.

Target

Subnet scanned: 192.168.1.0/24

Observed / example hosts from my scan:

192.168.1.1 — gateway / router

192.168.1.5 — web device

192.168.1.10 — server

192.168.1.67 — workstation (host of interest)

Tools

Nmap — network scanner (https://nmap.org
)

xsltproc — convert Nmap XML → HTML (optional)

Wireshark / tcpdump — optional packet capture & inspection

Prerequisites

Administrative privileges (root on Linux/macOS, Run as Administrator on Windows).

Permission to scan the network.

Basic knowledge of IP addresses, ports, and common services.

Commands I used

Note for Windows users: open Command Prompt or PowerShell as Administrator and do not use sudo.
Note for Linux/macOS: prefix commands with sudo when required.

# 1) Top 100 common ports across the subnet (overview)
sudo nmap -sS --top-ports 100 192.168.1.0/24 -oN subnet_top.txt

# 2) Detailed scan of a single host (service/version detection + safe default NSE scripts)
sudo nmap -sS -sV -sC 192.168.1.67 -oN 192.168.1.67_detailed.txt

# 3) Save XML (for converting to HTML)
sudo nmap -sS 192.168.1.67 -oX 192.168.1.67.xml

# 4) Convert XML to HTML for easy viewing
xsltproc 192.168.1.67.xml -o 192.168.1.67.html


Useful alternatives

# Only list hosts with open ports
sudo nmap -sS --open 192.168.1.0/24 -oN subnet_open_only.txt

# UDP scan (slower)
sudo nmap -sU 192.168.1.0/24 -oN udp_scan.txt

# Fast top ports scan (no sudo required for non-privileged mode, but -sS needs privileges)
sudo nmap --top-ports 100 192.168.1.0/24 -oN top100.txt

Steps (short)

Install Nmap and verify: nmap --version

Find local IP: Windows ipconfig, Linux/macOS ip addr or ifconfig

Run SYN scan (requires admin): nmap -sS 192.168.1.0/24

Record and save raw outputs.

(Optional) Capture traffic with Wireshark and analyze hosts/ports from Nmap.

Research services for each open port and evaluate risk.

Recommend mitigations (firewall, close service, patch software).

Findings (example table — fill with your actual results)
Host IP	Open Port(s)	Service (probable)	Notes / Risk
192.168.1.1	80, 443	HTTP, HTTPS	Router web UI — ensure strong password & update firmware
192.168.1.5	80	HTTP	Web device; check for default creds
192.168.1.10	22, 3306	SSH, MySQL	Server — verify SSH keys, restrict DB access
192.168.1.67	5060 (UDP), 5004 (UDP)	SIP / RTP (media)	Media/VoIP traffic visible; check for exposed services

Replace the table above with the exact ports and services you observed (from 192.168.1.67_detailed.txt and subnet_top.txt).

Wireshark — where to add the screenshot & caption

Place your Wireshark screenshot under Step 5 in the README (or in a figures/ folder). Example Markdown:

### 5. Optional — Analyze with Wireshark

Capture packets and filter for the host(s) you scanned.

**Wireshark Capture:**  
![Wireshark Capture](path/to/your_screenshot.png)

*Figure X.* Capture for host `192.168.1.67` (display filter: `ip.addr == 192.168.1.67 && udp`).  
Observation: frequent UDP packets between `192.168.1.67` and `142.250.82.223` (likely RTP/DTLS), some RTCP entries flagged as `[Malformed Packet]`, and a TCP ACK to `34.104.35.123`.


Helpful Wireshark display filters

All traffic for a host:

ip.addr == 192.168.1.67


Only UDP traffic:

ip.addr == 192.168.1.67 && udp


Only TCP:

ip.addr == 192.168.1.67 && tcp


Only two IPs:

ip.addr == 192.168.1.67 && (ip.addr == 142.250.82.223 || ip.addr == 34.104.35.123)

Quick sample analysis (paste into your summary)

The detailed scan of 192.168.1.67 revealed media-related UDP flows to 142.250.82.223 (likely RTP/DTLS). Wireshark showed a number of RTCP packets labeled [Malformed Packet], which may be caused by capture truncation or dissector issues. Any unexpected open ports should be reviewed and either closed or restricted via firewall rules; ensure services exposed to the LAN or WAN are updated and configured securely.

Remediation recommendations

Close unnecessary ports and disable unused services.

Restrict remote management (SSH/RDP) to authorized IPs or use VPN.

Apply software/firmware updates to devices and servers.

Use strong credentials and consider key-based SSH authentication.

Monitor logs and schedule regular scans.

Deliverables (what to submit)

subnet_top.txt (top-ports scan)

192.168.1.67_detailed.txt (detailed scan)

192.168.1.67.xml and 192.168.1.67.html (optional HTML report)

summary.md or summary.txt — contains the findings table, risk assessment, and remediation steps

(Optional) Wireshark screenshot + short caption

Submission checklist

 Raw Nmap outputs included

 Summary with table of hosts & ports

 Wireshark screenshot (if captured)

 Remediation suggestions included

Notes & best practices

Ethical/legal: only scan networks you own or have permission to scan.

Use -sV for service/version detection when you need more detail.

UDP scans are slower and less reliable—use carefully.

“Malformed” flags in Wireshark often mean packet truncation, capture issues, or an unsupported protocol dissector—inspect raw bytes if needed.

References

Nmap documentation — https://nmap.org/book/man.html

IANA Port Assignments — https://www.iana.org/assignments/port-numbers/port-numbers.xhtml

Wireshark documentation — https://www.wireshark.org/docs/
