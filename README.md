# Cybersecurity Capstone — Minor & Major Projects

Rose M Prakash — BCA Cybersecurity

This repository contains my Minor and Major capstone projects: packet-level
threat detection using Wireshark, and a reconnaissance/VAPT assessment
using Nmap.

---

## Minor Project: Network Traffic Threat Detection using Wireshark

**Objective:** Analyze PCAP files and identify suspicious network activities.

**Tools:** Wireshark, sample PCAP files.

**Steps followed:**
1. Installed Wireshark and sourced sample/reference PCAP files.
2. Opened each PCAP and observed protocols, IP addresses, timestamps, packet counts.
3. Applied filters — `arp`, `tcp`, `tcp.flags.syn==1 and tcp.flags.ack==0`, `arp.duplicate-address-detected`, `ip.addr==<IP>`.
4. Identified reconnaissance activity, a port scan, and ARP spoofing (duplicate IP-to-MAC mapping).
5. Built an incident timeline with attacker IP, victim IP, and attack sequence for each finding.
6. Prepared a SOC-style incident report with evidence, IOC list, and mitigation recommendations.

**Deliverables:**
- [reports/01_port_scan.md](reports/01_port_scan.md) — TCP handshake/HTTP baseline + TCP SYN port scan (attacker: 10.100.25.14, target: 10.100.18.12), IOC list, mitigation
- [reports/02_arp_spoofing.md](reports/02_arp_spoofing.md) — ARP spoofing / duplicate-IP detection, IOC list, mitigation
- `pcaps/` — source capture files
- `screenshots/` — evidence for each finding

---

## Major Project: Reconnaissance and VAPT

**Objective:** Perform ethical reconnaissance and vulnerability assessment on an authorized test target.

**Tools:** Nmap, Nikto (attempted — see tooling note), authorized public test targets (scanme.nmap.org, php.testinvicti.com).

**Steps followed:**
1. Selected authorized, purpose-built test targets for legal scanning practice.
2. Performed active reconnaissance — service and version enumeration with Nmap.
3. Enumerated hosts, open/filtered ports, and running services.
4. Performed vulnerability scanning using Nmap's NSE vuln scripts (Nikto substituted after a tool-level compatibility bug — documented in the report).
5. Validated findings and classified risk per finding (CVSS-style: High/Medium/Low).
6. Recommended remediation for each vulnerability and prepared a professional VAPT report.

**Deliverables:**
- [reports/03_vapt_recon.md](reports/03_vapt_recon.md) — Recon report (Nmap service scan) + vulnerability scan report (Nmap NSE) + risk ratings + remediation

---

## Skills Demonstrated

Wireshark, TCP/IP, DNS/HTTP, ARP, Nmap, NSE scripting, packet analysis,
IOC identification, SOC-style incident reporting, CVSS-style risk rating,
mitigation recommendations, Git/GitHub version control.

## Author

Rose M Prakash — BCA Cybersecurity student | [LinkedIn](https://linkedin.com/in/rose-m-prakash-b00208315) | [GitHub](https://github.com/rose-m-prakash)