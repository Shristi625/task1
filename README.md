Cyber Security Task 1 — Local Network Port Scan

Date of scan: 2025-10-14
Author: Shristi Gupta

Objective

Scan the local network to discover open ports, identify running services, and evaluate potential security risks (educational use only).

Target

Subnet scanned: 192.168.1.0/24

Observed hosts (examples from my scan):

192.168.1.1 — gateway / router

192.168.1.5 — web device

192.168.1.10 — server

192.168.1.67 — workstation (host of interest)

Tools

Nmap — network scanner.

xsltproc — (optional) converts Nmap XML → HTML for viewing in a browser.

Wireshark / tcpdump — optional packet capture and analysis.

Prerequisites

Administrative privileges (root / Administrator).

Permission to scan the network (do not scan networks without authorization).

Basic networking knowledge (IP addresses, ports, protocols).

Commands I used

Note: On Windows, run Command Prompt or PowerShell as Administrator (do not use sudo). On Linux/macOS, prefix with sudo if needed.

# 1) Top 100 common ports across the subnet (fast overview)
sudo nmap -sS --top-ports 100 192.168.1.0/24 -oN subnet_top.txt

# 2) Detailed scan for a host of interest (service/version detection + default safe scripts)
sudo nmap -sS -sV -sC 192.168.1.67 -oN 192.168.1.67_detailed.txt

# 3) XML output for conversion to HTML (optional)
sudo nmap -sS 192.168.1.67 -oX 192.168.1.67.xml

# 4) Convert Nmap XML to HTML (viewable in browser)
xsltproc 192.168.1.67.xml -o 192.168.1.67.html


Useful alternatives

# Show only hosts with open ports (cleaner output)
sudo nmap -sS --open 192.168.1.0/24 -oN subnet_open_only.txt

# UDP scan (slower and often requires root)
sudo nmap -sU 192.168.1.0/24 -oN udp_scan.txt

# Faster top-ports scan
sudo nmap --top-ports 100 192.168.1.0/24 -oN top100.txt

Workflow / Steps (brief)

Install Nmap — verify with:

nmap --version


Find local IP range

Windows: ipconfig

Linux/macOS: ip addr or ifconfig

Identify subnet (e.g., 192.168.1.0/24).

Perform TCP SYN scan

Linux/macOS:

sudo nmap -sS 192.168.1.0/24 -oN scan_results.txt


Windows (Admin prompt):

nmap -sS 192.168.1.0/24 -oN scan_results.txt


Note: -sS requires administrative access.

Record results — save raw output and summarize hosts/ports.

Optional — Analyze with Wireshark

Capture on the interface used for the scan.

Use display filters to focus on hosts/ports found by Nmap.

Place your Wireshark screenshot here in the README/report (see example caption below).

Research services — map ports to services (IANA, Nmap -sV).

Identify risks & remediate — e.g., close unnecessary ports, firewall rules, secure service configs.

Where to place your Wireshark screenshot

Put the screenshot directly under Step 5 in the report. Example markdown to embed the image and caption:

### 5. Optional — Analyze with Wireshark

Capture packets and filter for the host(s) you scanned.

**Wireshark Capture:**  
![Wireshark Capture](path/to/your_screenshot.png)

*Figure X.* Capture for host `192.168.1.67` (display filter: `ip.addr == 192.168.1.67 && udp`).  
Observation: heavy UDP traffic between `192.168.1.67` and `142.250.82.223` (likely RTP/DTLS), some RTCP feedback labeled as `[Malformed Packet]`, and a TCP ACK to `34.104.35.123`.

Short sample analysis (paste into your summary)

The scan identified multiple hosts in 192.168.1.0/24. Host 192.168.1.67 showed open services discovered during a detailed scan — frequent UDP traffic to 142.250.82.223 suggests media or encrypted media sessions (RTP/DTLS). Wireshark displayed several RTCP entries flagged as malformed; this may be caused by capture truncation, MTU issues, or protocol dissector limitations. The presence of unexpected open ports or public-facing services should be reviewed and, where unnecessary, closed or restricted with firewall rules.

Deliverables (what to submit)

Raw Nmap output (.txt or .xml) — e.g., subnet_top.txt, 192.168.1.67_detailed.txt.

Summary file (summary.md) — include: date/time, subnet scanned, table of hosts & open ports, brief risk assessment and remediation steps.

(Optional) Wireshark screenshot + short caption/analysis (placed under Step 5).

(Optional) HTML report converted from XML (192.168.1.67.html).

Notes & Best Practices

Legal / Ethical: Only scan networks you own or have explicit permission to scan.

Privileges: -sS requires elevated privileges. On Windows run the terminal as Administrator.

Performance: UDP scans are slower; use --top-ports for speed.

Interpretation: “Malformed” packet flags may be due to capture limits or dissector issues — inspect raw bytes if necessary.
