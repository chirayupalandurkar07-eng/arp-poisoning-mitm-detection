# ARP Poisoning MITM Attack & Detection

**Analyst:** Chirayu Mahendra Palandurkar  
**Date:** 18 June 2026  
**Report ID:** ADR-2026-0618-001  
**Tools:** Ettercap 0.8.4.1 · Wireshark 4.6.6 · arpwatch 2.1a15 · Suricata 8.0.5 · Python/Scapy 2.7.1  
**Lab:** VirtualBox — NAT Network (Kali + Debian)

---

## Overview

Full ARP poisoning man-in-the-middle attack executed in an 
isolated two-VM lab with DNS spoofing. Captured the full 
attack at packet level and proved detection using three 
independent methods.

---

## Lab Environment

| Host | IP | Role |
|------|----|------|
| Kali Linux | 10.0.2.17 | Attacker |
| Debian Linux | 10.0.2.15 | Victim |
| Gateway | 10.0.2.1 | NAT Gateway |

---

## Attack Summary

```bash
sudo ettercap -T -q -i eth0 -M arp:remote /10.0.2.15// -P dns_spoof
```

- ARP cache of victim poisoned — all traffic routed through attacker
- DHCP ACK packets intercepted — full MITM position confirmed
- DNS spoofing redirected victim to fake Student Portal

---

## Detection Results

| Method | Result | Detail |
|--------|--------|--------|
| Wireshark | ✅ Detected | arp.opcode==2 isolated 8 unsolicited ARP replies |
| arpwatch | ✅ Detected | 3 stations logged within 3 minutes |
| Suricata 8.0.5 | ⚠️ Partial | 270 ARP packets decoded — Layer 2 rule signatures unsupported |
| Python/Scapy | ✅ Detected | IP-MAC mapping anomalies confirmed from PCAP |

**Detection limitation:** Suricata 8 does not support native 
ARP rule signatures. Use arpwatch on a dedicated network 
sensor for reliable Layer 2 detection.

---

## MITRE ATT&CK Mapping

| Technique ID | Name |
|---|---|
| T1040 | Network Sniffing |
| T1557.002 | ARP Cache Poisoning |
| T1557.001 | DNS Spoofing |
| T1071.001 | Application Layer Protocol: HTTP |
| T1491.001 | Defacement |

---

## Key Detection Signature
