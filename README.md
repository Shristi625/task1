# Cyber Security Task 1 — Local Network Port Scan

**Date of scan:** 2025-10-14

## Objective
Scan the local network to discover open ports, identify running services, and evaluate potential security risks.

## Target
- Subnet scanned: `192.168.1.0/24`
- Scanned hosts (examples / observed in my scan):
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
sudo nmap -sS --top-ports 100 192.168.1.0/24 -oN subnet_top.txt

# Detailed scan for a host of interest (version detection + default scripts)
sudo nmap -sS -sV -sC 192.168.1.67 -oN 192.168.1.67_detailed.txt

# XML output for conversion to HTML report (optional)
sudo nmap -sS 192.168.1.67 -oX 192.168.1.67.xml
xsltproc 192.168.1.67.xml -o 192.168.1.67.html
