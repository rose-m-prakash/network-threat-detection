## Finding: TCP 3-Way Handshake

**Filter used:** tcp.port == 80 (or click packet 1 directly)

**Packet 1** — SYN
- Src: 145.254.160.237:3372 → Dst: 65.208.228.223:80
- Flags: SYN
- Seq=0, Win=8760

**Packet 2** — SYN, ACK
- Src: 65.208.228.223:80 → Dst: 145.254.160.237:3372
- Flags: SYN, ACK
- Seq=0, Ack=1, Win=5840

**Packet 3** — ACK
- Src: 145.254.160.237:3372 → Dst: 65.208.228.223:80
- Flags: ACK
- Seq=1, Ack=1, Win=9660

**Conclusion:** Standard TCP handshake completes successfully before HTTP
communication begins in packet 4.

## Finding: HTTP Request Identified

**Packet 4**
- GET /download.html HTTP/1.1
- Host: www.ethereal.com
- User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.6) Gecko/20040113

**Conclusion:** Client requests a file over HTTP following successful TCP
handshake (packets 1-3). Legacy User-Agent string suggests an older client
(Windows NT 5.1 = Windows XP era) — worth noting in a real investigation as
a potential outdated/unpatched system indicator.

## Finding: TCP SYN Port Scan Detected

**Source:** 10.100.25.14 (scanner)
**Target:** 10.100.18.12
**Pattern:** Sequential SYN packets to many different destination ports
(139, 135, 445, 80, 22, 515, 23, 21, 6000, 1025, 25, 111, ...) within
~1 second, each with Seq=0 and no completed handshake.

**Filter used:** ip.src==10.100.25.14 and tcp.flags.syn==1 and tcp.flags.ack==0

**Conclusion:** This is a TCP SYN scan (half-open scan), consistent with
reconnaissance tooling such as Nmap. The rapid, sequential probing of
multiple common service ports (SMB/139&445, RPC/135, HTTP/80, SSH/22,
Telnet/23, FTP/21) indicates the source is enumerating open services on
the target — a classic pre-attack reconnaissance technique.

## IOC List (so far)
| Indicator | Type | Notes |
|---|---|---|
| 10.100.25.14 | IP | Source of SYN port scan |
| 10.100.18.12 | IP | Scan target |

## Mitigation Recommendations
- Rate-limit or alert on excessive SYN packets from a single source in a short window
- Deploy an IDS/IPS signature for SYN scan detection (e.g. Snort/Suricata "many ports, one source, short time")
- Restrict exposed services via firewall rules (least-privilege port exposure)
- Enable port scan detection/logging on perimeter firewall