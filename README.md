# Socks_Packet_Replay
This is a Packet Replay Generator. Custom homebrewed for Windows and or Linux. This is something that i use to check sensors and have in the past used to train customers and students in threat hunting. Use it however you want just provide attribution or a link back to me. 

# Time-aligned PCAP replay for hunt mission analyst training.
# Built by Nico "Socks" Smith · sockiewoxie@gmail.com · v4.1

# What It Does:
Replays .pcap and .pcapng files to analyst workstations over TCP with the original capture timestamps fully preserved. Analysts see the correct date (2021, 2023, whenever the traffic was captured) in Wireshark, Zeek, Suricata, Security Onion, and Malcolm , not today's date.
Live wire replay cannot achieve this (the receiving NIC's OS kernel always timestamps packets at arrival). This tool bypasses the wire entirely by streaming a structured PCAP byte stream over TCP. The analyst's tool reads ts_sec/ts_usec directly from the PCAP record headers.

# SETUP
In order to ensure the traffic displaying the traffic at time of capture vs. time of replay, the lab environment will have the engineer machine( the .pcap replay machine ) on this machine you will need the executable:
SocksPCAPReplay.exe ( # Run as Administrator )
  * Select REMOTE STREAM as delivery mode
  * Click File or Folder to browse for your PCAP(s)
  * Press Start Replay
  * Wait for analysts to connect — the TUI logs each connection

On a analyst machine that is on the same network monitoring using wireshark you will need the reciever executable : 
Open a command prompt : SocksPCAPReceiver.exe --server <engineer machine IP>
Open a second command prompt : wireshark -k -i \\.\pipe\SocksPCAPReplay  or # this has been the best method :
  * open wireshark and add a pipe in the gui:  wireshark -k -i \\.\pipe\SocksPCAPReplay

# NAS Replay Pipeline
Scan NAS, sort by actual capture date, preview manifest
SocksPCAPPipeline.exe --nas \\NAS\share\pcaps

# Scan and stream immediately
SocksPCAPPipeline.exe --nas \\NAS\share\pcaps --stream

# Filter by date range and keyword
SocksPCAPPipeline.exe --nas Z:\pcaps --stream --from 2023-01-01 --to 2023-12-31 --filter ransomware

# Save a playlist for a scheduled exercise
SocksPCAPPipeline.exe --nas \\NAS\pcaps --export mission1.txt
SocksPCAPPipeline.exe --playlist mission1.txt --stream

Delivery Modes
|Mode|Timestamps on Sensor|Use When|
| ----- | ------------ | ---------- |
| REMOTE STREAM | Original PCAP date | Hunt missions, time-correlation exercises | 
| LIVE WIRE | Today's date | Detection/triage drills where date is irrelevant |
| NAMED PIPE | Original PCAP date | Single machine, local Wireshark only |

# Sensor Integration
# Wireshark
 * powershellwireshark -k -i \\.\pipe\SocksPCAPReplay

# tcpdump / tshark
  * tcpdump -r /tmp/socks_pcap_pipe
  * tshark  -i /tmp/socks_pcap_pipe

# Security Onion
  * python3 socks_pcap_receiver.py --server <engineer IP> --output /tmp/rx.pcap
  * sudo so-import-pcap /tmp/rx.pcap

# Malcolm
  * python3 socks_pcap_receiver.py --server <engineer IP> --output /tmp/rx.pcap
  * Upload /tmp/rx.pcap via the Malcolm PCAP ingest UI

# Zeek / Suricata
  * python3 socks_pcap_receiver.py --server <engineer IP>
  * zeek     -r /tmp/socks_pcap_pipe
  * suricata -r /tmp/socks_pcap_pipe -l /tmp/suricata-logs/
