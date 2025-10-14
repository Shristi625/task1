
# Cyber Security Task 1 — Local Network Port Scan

**Date of scan:** 2025-10-14  

## Objective
Scan the local network to discover open ports, identify running services, and evaluate potential security risks.

## Target
- **Subnet scanned:** `192.168.1.0/24`  
- **Observed hosts (examples / observed in my scan):**
  - `192.168.1.1` (gateway/router)
  - `192.168.1.5` (web device)
  - `192.168.1.10` (server)
  - `192.168.1.67` (workstation / host of interest)

## Tools
- Nmap
- xsltproc (optional, for XML → HTML conversion)
- (Optional) tcpdump / Wireshark for packet capture

## Commands Used
I ran the following Nmap commands to produce the outputs included in this repository:

```bash
# Top 100 common ports across the subnet (saved to subnet_top.txt)
sudo nmap -sS --top-ports 100 192.168.1.0/24 -oN task1.txt

# Detailed scan for a host of interest (version detection + default scripts)
sudo nmap -sS -sV -sC 192.168.1.67 -oN 192.168.1.67_detailed.txt

# XML output for conversion to HTML report (optional)
sudo nmap -sS 192.168.1.67 -oX 192.168.1.67.xml
xsltproc 192.168.1.67.xml -o 192.168.1.67.html
```

## Steps (concise)
1. Install Nmap and verify: `nmap --version`.  
2. Find local IP/subnet: Windows `ipconfig`, Linux/macOS `ip addr` or `ifconfig`.  
3. Run SYN scan (requires admin): `nmap -sS 192.168.1.0/24 -oN scan_results.txt`.  
4. Save outputs and summarize open ports.  
5. (Optional) Capture traffic with Wireshark/tcpdump and analyze Nmap-listed hosts/ports.  
6. Research service versions (`-sV`) and assess risk.  
7. Recommend and apply mitigations.

## Findings (fill with your real scan output)
Use this table format — replace values with your actual results.

| Host IP | Open Port(s) | Service (probable) | Notes / Risk |
|---:|:---:|:---|:---|
| `192.168.1.1` | 80, 443 | HTTP/HTTPS | Router UI — ensure firmware & strong password |
| `192.168.1.5` | 80 | HTTP | Web device — check defaults |
| `192.168.1.10` | 22, 3306 | SSH, MySQL | Server — restrict DB access; enforce SSH key auth |
| `192.168.1.67` | 5060 (UDP), 5004 (UDP) | SIP / RTP | VoIP/media traffic — verify access control |

## Wireshark — where to add the screenshot & caption
Place your Wireshark screenshot under Step 5. Example Markdown:

```markdown
### 5. Optional — Analyze with Wireshark

Capture packets and filter for the host(s) you scanned.

**Wireshark Capture:**  
![Wireshark Capture](figures/wireshark_capture.png)

*Figure X.* Capture for host `192.168.1.67` (display filter: `ip.addr == 192.168.1.67 && udp`).  
Observation: frequent UDP packets between `192.168.1.67` and `142.250.82.223` (likely RTP/DTLS); some RTCP entries flagged `[Malformed Packet]`.
```

## Remediation suggestions (commands you can use)
- **Ubuntu / Debian with UFW**
```bash
sudo ufw default deny incoming
sudo ufw allow from 192.168.1.0/24 to any port 22 proto tcp
sudo ufw allow 80,443/tcp
sudo ufw enable
```

- **iptables example**
```bash
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -j DROP
```

## Troubleshooting & tips
- If `nmap -sS` fails on Windows, ensure terminal is opened **as Administrator**.  
- If Wireshark shows `[Malformed Packet]`, try: increase capture length (Preferences → Capture → Default snaplen), capture on the correct interface, or examine raw bytes (right-click → "Follow" → "UDP stream" or view packet bytes).  
- For slow scans, use `--top-ports` or scan smaller ranges.  
- Use `-sV` to confirm service banners — helps reduce false positives.

## How to present results (short)
- Include: date/time, subnet scanned, commands used, raw outputs (attach `.txt`/.xml), a one-page summary (table of hosts & ports), screenshot(s) with captions, and clear remediation steps.  
- Label figures (Figure 1, Figure 2) and reference them in your summary.

## Deliverables (for submission)
- `subnet_top.txt`  
- `192.168.1.67_detailed.txt`  
- `192.168.1.67.xml` and `192.168.1.67.html` (optional)  
- `summary.md` or `summary.txt` (with table + remediation)  
- Wireshark capture screenshot(s) (optional)

## References
- Nmap documentation — https://nmap.org/book/man.html  
- Wireshark documentation — https://www.wireshark.org/docs/
