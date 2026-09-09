# Network Traffic Threat Detection

A Wireshark-based PCAP analysis project identifying reconnaissance,
ARP spoofing, and TCP port scanning — built as hands-on practice for
SOC analyst / blue team work.

## Overview

This project analyzes packet captures to detect and document three
categories of network threats:

1. **Baseline traffic analysis** — TCP 3-way handshake and HTTP request identification
2. **ARP spoofing detection** — identifying duplicate IP-to-MAC mappings indicative of ARP cache poisoning
3. **Port scan detection** — identifying reconnaissance via rapid sequential SYN packets across multiple ports

## Structure