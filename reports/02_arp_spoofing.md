## Finding: Normal ARP Resolution (Baseline)

**Packets 1–2** — arp.pcap
- Request: Who has 192.168.0.1? Tell 192.168.0.114
- Reply: 192.168.0.1 is at 00:13:46:0b:22:ba (single MAC)

**Conclusion:** This is legitimate ARP behavior — one IP resolves to one
consistent MAC address. In an ARP spoofing attack, the same IP would
resolve to multiple conflicting MAC addresses within a short time window,
or Wireshark would flag "Duplicate use of detected IP address."

## Finding: ARP Spoofing / Duplicate IP Address Detected

**Source file:** Duplicate_IP_Address.cap (public Wireshark reference capture)
**Filter used:** arp.duplicate-address-detected

**Evidence — Packets 212–223 (and beyond):**
Multiple ARP reply packets claim different IP addresses using rapidly
changing MAC addresses within a ~0.1 second window (timestamps
17.801105–17.801183). Example:

| Time | Source MAC | Claims IP |
|---|---|---|
| 17.801105 | 02:a0:98:03:a7:20 | 10.29.152.117 |
| 17.801108 | 02:a0:98:03:9e:77 | 10.29.152.101 |
| 17.801108 | 02:a0:98:29:04:80 | 10.29.152.108 |
| 17.801109 | 02:a0:98:10:8b:08 | 10.29.152.112 |

Wireshark's built-in expert analysis explicitly flags this activity:
"Duplicate IP address detected for 10.29.153.85 (00:18:fe:89:fc:6...)"

**Conclusion:** This traffic pattern — many ARP replies for different IPs
arriving within milliseconds of each other, each flagged by Wireshark as
a duplicate/conflicting IP-to-MAC mapping — is consistent with ARP cache
poisoning. In a real ARP spoofing attack, an attacker floods the network
with forged replies to redirect traffic through their own machine (MITM).

## IOC List
| Indicator | Type | Notes |
|---|---|---|
| 02:a0:98:03:a7:20 | MAC | Claimed conflicting IP (10.29.152.117) |
| 02:a0:98:03:9e:77 | MAC | Claimed conflicting IP (10.29.152.101) |
| 02:a0:98:29:04:80 | MAC | Claimed conflicting IP (10.29.152.108) |
| 10.29.152.101, .105, .108, .112, .117 (and more) | IP | Targets of conflicting ARP claims |

## Mitigation Recommendations
- Enable Dynamic ARP Inspection (DAI) on managed switches
- Use static ARP entries for critical hosts (gateway, servers)
- Deploy ARP spoofing detection tools (e.g., arpwatch, XArp)
- Segment network with VLANs to limit ARP broadcast domain
- Monitor for Wireshark/IDS alerts on "duplicate IP address" events
