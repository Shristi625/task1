Cyber Security Task 1 — Local Network Port Scan

Date of scan: 2025-10-14
Author: Shristi Gupta

Objective

Scan the local network to discover open ports, identify running services, and evaluate potential security risks.

Target

Subnet scanned: 192.168.1.0/24

Observed hosts (examples / observed in my scan):

192.168.1.1 (gateway/router)

192.168.1.5 (web device)

192.168.1.10 (server)

192.168.1.67 (workstation / host of interest)

Tools

Nmap

xsltproc (optional — converts Nmap XML → HTML)

tcpdump / Wireshark (optional — packet capture & analysis)

Commands Used

Notes:

On Linux/macOS, prepend sudo for raw-socket scans (e.g., -sS).

On Windows, open Command Prompt or PowerShell as Administrator (do not use sudo).

# 1) Top 100 common ports across the subnet (overview)
sudo nmap -sS --top-ports 100 192.168.1.0/24 -oN subnet_top.txt

# 2) Detailed scan for a host (service/version detection + default safe NSE scripts)
sudo nmap -sS -sV -sC 192.168.1.67 -oN 192.168.1.67_detailed.txt

# 3) Save XML for conversion to HTML
sudo nmap -sS 192.168.1.67 -oX 192.168.1.67.xml

# 4) Convert XML to HTML (correct xsltproc usage)
xsltproc 192.168.1.67.xml -o 192.168.1.67.html


Useful alternatives

# Show only hosts with open ports (cleaner output)
sudo nmap -sS --open 192.168.1.0/24 -oN subnet_open_only.txt

# UDP scan (slower / noisy)
sudo nmap -sU 192.168.1.0/24 -oN udp_scan.txt

# Quick scan of top ports (fast)
sudo nmap --top-ports 100 192.168.1.0/24 -oN top100.txt

# Version and OS detection (slower, more intrusive)
sudo nmap -sS -sV -O 192.168.1.67 -oN host_full_info.txt

Steps (concise)

Install Nmap and verify: nmap --version.

Find local IP/subnet: Windows ipconfig, Linux/macOS ip addr or ifconfig.

Run SYN scan (requires admin): nmap -sS 192.168.1.0/24 -oN scan_results.txt.

Save outputs and summarize open ports.

(Optional) Capture traffic with Wireshark/tcpdump and analyze Nmap-listed hosts/ports.

Research service versions (-sV) and assess risk.

Recommend and apply mitigations.

Where to add your Wireshark screenshot

Place the screenshot under Step 5. Example:

### 5. Optional — Analyze with Wireshark

**Wireshark Capture:**  
![Wireshark Capture](figures/wireshark_capture.png)

*Figure X.* Capture for host `192.168.1.67` (display filter: `ip.addr == 192.168.1.67 && udp`).  
Observation: frequent UDP packets to `142.250.82.223` (likely RTP/DTLS); some RTCP entries flagged `[Malformed Packet]`. See troubleshooting notes below.


Helpful filters

ip.addr == 192.168.1.67
ip.addr == 192.168.1.67 && udp
ip.addr == 192.168.1.67 && tcp
ip.addr == 192.168.1.67 && (ip.addr == 142.250.82.223 || ip.addr == 34.104.35.123)

Findings (fill with your real scan output)

Use this table format — replace values with your actual results.

Host IP	Open Port(s)	Service (probable)	Notes / Risk
192.168.1.1	80, 443	HTTP/HTTPS	Router UI — ensure firmware & strong password
192.168.1.5	80	HTTP	Web device — check defaults
192.168.1.10	22, 3306	SSH, MySQL	Server — restrict DB access; enforce SSH key auth
192.168.1.67	5060 (UDP), 5004 (UDP)	SIP / RTP	VoIP/media traffic — verify access control
