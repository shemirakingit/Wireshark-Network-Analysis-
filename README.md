# Wireshark-Network-Analysis-
Wireshark hands-on lab covering the core skills of packet analysis — live traffic capture, TCP handshakes, DNS tracing, cleartext credential exposure in HTTP, and TCP stream reconstruction. No simulations. Real captures on live interfaces.

📹 Check out this walkthrough guide and share your feedback here: 
https://www.loom.com/share/cf85962432ab404fb95ab0bf1edd7824

Overview
Wireshark (free & open source)
Local machine or Azure VM
Time to Complete
2–4 hours
Estimated Cost
$0
Certification Alignment
CompTIA Network+ · Security+ · CySA+

Career Relevance
Network Engineer · SOC Analyst · Cloud Security Engineer · Incident Responder



What This Lab Covers :

Skill

Real-World Application

Capture live network traffic

Foundational skill — you can't analyze what you can't see

Apply display filters

Filter millions of packets to find the 10 that matter

Read TCP handshakes

Distinguish successful connections from failures instantly

Identify DNS queries and responses

DNS is involved in virtually every network action

Spot cleartext credentials in HTTP

Hands-on demonstration of why HTTPS matters

Follow TCP streams

Reconstruct full conversations between hosts — essential for incident investigation



## Prerequisites
Wireshark installed (see setup below)
Basic familiarity with the command line
A network interface (Wi-Fi or Ethernet)


Setup
Install Wireshark
Download from wireshark.org/download.html — no account required.

OS
Instructions
Windows
Download the x64 .exe installer. Accept all defaults. Install Npcap when prompted — required for packet capture.
macOS
Download the .dmg for your chip (Arm or Intel). Allow ChmodBPF if prompted — this grants interface access.
Linux
sudo apt install wireshark (Ubuntu/Debian). Then add yourself to the wireshark group and log out/in.


# Linux only — add yourself to the wireshark group

sudo usermod -aG wireshark $USER

# Verify installation

wireshark --version


## Exercises

**Exercise A** — Capture a DNS Lookup
Trigger a DNS query manually using nslookup, then inspect the query and response packets in Wireshark.

nslookup google.com

What to look for: In the DNS response packet, expand Domain Name System (response) → Answers to see the A record and returned IP address.

Key concept: DNS queries happen before every website visit, API call, and email. Unexpected DNS queries to unusual domains are often the first sign of malware phoning home.


**Exercise B** — Watch the TCP Three-Way Handshake
Navigate to http://example.com and capture the three packets that establish the connection.

Packet
Flags
Meaning
1st
SYN
Client: I want to connect
2nd
SYN, ACK
Server: Request received, connection accepted
3rd
ACK
Client: Confirmed, ready to send data


Filter to use: tcp and ip.addr == <example.com IP>

Key concept: A SYN with no SYN-ACK means the server is unreachable or refused the connection. A RST packet means the connection was forcibly closed.


**Exercise C** — Spot Cleartext Credentials (HTTP)
Submit a test login form over HTTP (not HTTPS) and capture the POST request.

Filter to use: http.request.method == POST

Look for the HTML Form URL Encoded layer in the packet detail pane — the username and password will be visible in plaintext.

Note: This exercise is for educational purposes only. Only capture traffic on networks and systems you own or have explicit permission to test.

Key concept: Without TLS, anyone on the network path can read credentials exactly as typed. This is the practical reason HTTPS became the standard.


**Exercise D** — Follow a Full TCP Stream
Capture HTTP traffic, right-click any HTTP packet, and select Follow → TCP Stream.

Red text = your browser's request
Blue text = the server's response

Key concept: Individual packets are fragments. Stream view reconstructs the full conversation — essential for understanding what data was actually transferred during an incident.


Essential Display Filters
Filter
What It Shows
dns
All DNS queries and responses
http
Unencrypted HTTP traffic
tcp
All TCP traffic
tcp.flags.syn == 1
Connection attempts (SYN packets only)
tcp.flags.reset == 1
Refused or forcibly closed connections
icmp
Ping and network diagnostics
ip.addr == 192.168.1.1
All traffic to/from a specific IP
http.request
HTTP GET and POST requests only
tcp.port == 443
HTTPS traffic by port


Display filters vs capture filters: Display filters show/hide packets after capture without discarding anything. Always use display filters in this lab — you can re-examine the same capture through different lenses without re-capturing.


Saving Captures
# Save full capture

File → Save As → .pcapng format

# Export only filtered packets

# (apply display filter first)

File → Export Specified Packets → Displayed

# Command-line capture with tshark

tshark -i eth0 -w capture.pcapng -c 1000

# -i: interface   -w: output file   -c: packet count limit


Verification Checklist
Apply dns filter and identify a query/response pair by matching transaction IDs
Find SYN → SYN-ACK → ACK packets and explain each without notes
Filter by IP address, port, and protocol from memory
Follow a TCP stream and read the full HTTP request/response
Save a capture, reopen Wireshark, load the file, and confirm all packets are present


Portfolio
This repo includes three saved captures demonstrating:


TCP handshake — three-way handshake with example.com
<img width="2240" height="1260" alt="Screenshot 2026-05-24 at 7 33 18 PM" src="https://github.com/user-attachments/assets/03fe3aa7-f4d8-4e98-afb3-ec105609354c" />

TCP stream follow — full reconstructed HTTP conversation
<img width="2240" height="1260" alt="Screenshot 2026-05-24 at 7 34 12 PM" src="https://github.com/user-attachments/assets/30eeeeca-3f80-49fe-9316-1abc20e50f3b" />

Each .pcapng file can be opened in Wireshark to reproduce the analysis.
DNS lookup — query and response for google.com
<img width="2240" height="1260" alt="Screenshot 2026-05-24 at 7 35 56 PM" src="https://github.com/user-attachments/assets/15d7fe57-1d81-40a7-870a-d8a736ce741d" />


