
# Wireshark Capture — Kali Linux (Task 5)

**Author:** Rohit Madhav Sabale  
**Date:** 27/10/2025

---

## What this is
A compact, practical guide + summary of the packet capture I performed on Kali Linux using **Wireshark**. It explains how the capture was done.

---

## Quick snapshot — environment & tools
- **Host OS:** Kali Linux (VM or Live)  
- **Capture tool:** Wireshark  
- **Interface used:**  `eth0` 
- **Traffic generators used:** web browser, `ping`, `curl`  
- **Files produced:** Wireshark_anaalysis.pdf

---

## Capture steps (short & repeatable)
1. Update packages and install tools:  
   ```bash
   sudo apt update
   sudo apt install wireshark tcpdump tshark -y
   ```
2. Allow non-root packet capture (optional but convenient):  
   ```bash
   sudo dpkg-reconfigure wireshark-common
   sudo usermod -aG wireshark $USER
   newgrp wireshark
   ```
3. Start GUI capture: `wireshark` → double-click the active interface (`eth0`).
4. Or capture from terminal (60 seconds example):  
   ```bash
   sudo tshark -i eth00 -w ~/task5_capture.pcap -a duration:60
   ```
5. Generate traffic while capturing: open websites (example.com), run `ping google.com`, or `curl http://example.com`.


---

## Useful display filters 
- `dns` — DNS queries/replies  
- `http` — HTTP traffic  
- `tls || ssl` — TLS/SSL encrypted sessions (HTTPS)  
- `icmp` — ping / echo packets  
- `ip.addr == <IP>` — packets to/from a specific IP  
- `tcp.port == 80 || tcp.port == 443` — typical web ports  
- `frame contains "google"` — search packet payloads for string

---

## Protocols observed 
| Protocol | Role in traffic |
|----------|-----------------|
| **DNS**  | Name resolution (UDP 53) — client → resolver queries |
| **HTTP** | Unencrypted web requests (TCP 80) |
| **HTTPS / TLS** | Encrypted web traffic (TCP 443) — handshakes visible |
| **ICMP** | Network diagnostics (ping) |
| **TCP**  | Reliable transport — SYN / SYN-ACK / ACK seen |
| **UDP**  | Connectionless transport (used by DNS, some streaming) |
| **QUIC** | UDP-based encrypted transport (observed as Protected Payload) |
| **ARP**  | Local link address resolution (IP → MAC) |

---

## Short findings & notes
- DNS requests were sent to external DNS servers (e.g., `8.8.8.8`) and returned A records.  
- Browsing triggered TLS handshakes (ClientHello / ServerHello) — application data was encrypted.  
- `ping` produced ICMP echo requests and replies, confirming basic connectivity.  
- Several TCP connection lifecycles were captured (SYN → SYN-ACK → ACK → FIN).  
- QUIC packets appeared (protected payloads) when modern browsers used HTTP/3 — these show as `QUIC` or `Protected Payload` in Wireshark.

---

## Files to include in the submission
- `Wireshark_analysis.pcap` — raw capture file (required)  
- `Wireshark_analysis.pdf` — detailed report with screenshots (I prepared this file)  
- `README.md` — this file (summary + instructions)  
- `screenshots/` 

---

## How to extract a filtered pcap
To save only displayed packets (e.g., DNS only):  
1. Apply `dns` filter in Wireshark.  
2. File → Export Specified Packets → Select **Displayed** → Save as `dns_only.pcap`.

Or using tshark to extract DNS from a capture:
```bash
tshark -r Wireshark_analysis.pcap -Y "dns" -w dns_only.pcap
```

---

## Short command cheatsheet
```bash
# list interfaces
ip a
sudo tshark -D

# start GUI (if allowed to run as user)
wireshark

# capture with tcpdump
sudo tcpdump -i wlan0 -w ~/Wireshark_analysis.pcap

# one-minute tshark capture
sudo tshark -i wlan0 -a duration:60 -w ~/Wireshark_analysis.pcap

# read pcap quickly (summary)
tshark -r Wireshark_analysis.pcap -q -z io,stat,0
```
---

**Prepared by:** Rohit Madhav Sabale  

