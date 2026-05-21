# Socks Packet Replay

**Time-aligned PCAP replay for hunt mission analyst training.**

Built by Nico "Socks" Smith | sockiewoxie@gmail.com | v4.1

---

## Overview

Socks Packet Replay is a custom-built packet replay generator for Windows and Linux. It replays `.pcap` and `.pcapng` files to analyst workstations over TCP while fully preserving the original capture timestamps. Analysts see the correct date (2021, 2023, whenever the traffic was captured) in their tools, not the current date.

This matters because live wire replay cannot preserve timestamps. The receiving NIC's OS kernel always timestamps packets at the moment they arrive. Socks Packet Replay bypasses the wire entirely by streaming a structured PCAP byte stream over TCP. The analyst's tool reads `ts_sec` / `ts_usec` directly from the PCAP record headers, so the original dates stay intact.

I use this tool to validate sensors and have used it extensively to train customers and students in threat hunting. You are free to use it however you want. All I ask is that you provide attribution or a link back to this repository.

Supported tools include Wireshark, Zeek, Suricata, Security Onion, and Malcolm.

---

## Delivery Modes

| Mode | Timestamps on Sensor | Use When |
|------|----------------------|----------|
| REMOTE STREAM | Original PCAP date | Hunt missions, time-correlation exercises |
| LIVE WIRE | Today's date | Detection and triage drills where date is irrelevant |
| NAMED PIPE | Original PCAP date | Single machine, local Wireshark only |

---

## Setup

The lab environment requires two machines on the same network: an engineer machine (the replay source) and one or more analyst machines (the receivers).

### Engineer Machine (Replay Source)

Run `SocksPCAPReplay.exe` as Administrator.

1. Select **REMOTE STREAM** as the delivery mode.
2. Click **File** or **Folder** to browse for your PCAP files.
3. Press **Start Replay**.
4. Wait for analysts to connect. The TUI logs each connection as it comes in.

### Analyst Machine (Receiver)

Open a command prompt and run the receiver, pointing it at the engineer machine:
