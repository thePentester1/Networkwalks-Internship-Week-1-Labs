# NetworkWalks Internship – Week 1 Labs

**Intern:** COLLINS AKALMBI ASHIME JR. 
  **Program:** NetworkWalks Cybersecurity Internship, Week 1
**Platform:** NetworkWalks Wireshark SOC Lab

## About
This repository documents my Week 1 hands-on labs from the NetworkWalks internship. Each lab places me in the role of a SOC analyst who captures network traffic and investigates it in Wireshark to find suspicious activity, attackers, and exposed data.

## Skills Practiced

- Setting up a virtual lab environment (Kali Linux and Windows VMs)
- Capturing and filtering live traffic with Wireshark display filters
- Spotting reconnaissance and brute-force behavior in packet captures
- Following TCP streams to reconstruct conversations and web sessions
- Recognizing the risks of unencrypted protocols (HTTP, FTP, plain-text chat, VoIP/RTP)
- Reconstructing VoIP calls with the Wireshark RTP Player
- Identifying attacker and victim hosts from traffic patterns

## Lab Summary

| Lab | Topic | Score |
|-----|-------|-------|
| 1 | Install a Kali Linux Virtual Machine | Completed |
| 2 | Install Windows in a Virtual Machine | Completed |
| 3 | Identify the Attacker's IP | 90% |
| 4 | Follow TCP Stream | 100% |
| 5 | Follow TCP Stream, Part 2 | 80% |
| 6 | HTTP Plain Text Credentials | 90% |
| 7 | Brute Force Catch | 100% |
| 8 | VoIP Call Playback (RTP) | 100% |

## Lab Details

### Lab 1 – Install a Kali Linux Virtual Machine
**Goal:** Build the attacker/analyst workstation used for the rest of the internship.
**Approach:** Installed Kali Linux inside a virtual machine and configured it so it boots and runs properly.
**Result:** Kali Linux VM up and running, ready for security tools such as Wireshark.

### Lab 2 – Install Windows in a Virtual Machine
**Goal:** Build a Windows machine to act as a target/victim system in later labs.
**Approach:** Installed Windows inside a second virtual machine and confirmed it starts and runs correctly alongside the Kali VM.
**Result:** Both the Kali and Windows virtual machines are installed and working, giving me a safe lab environment.

### Lab 3 – Identify the Attacker's IP
**Scenario:** A staff member reported unusual traffic to the company web server.
**Approach:** Filtered for TCP SYN packets (`tcp.flags.syn == 1 && tcp.flags.ack == 0`), then looked at the target's RST replies (`ip.src == 192.168.10.20 && tcp.flags.reset == 1`) to find closed ports.
**Finding:** A TCP SYN **port scan**. One host (192.168.10.66) probed many ports on a single target (192.168.10.20) from the same source port within milliseconds, without completing any connection.

### Lab 4 – Follow TCP Stream
**Scenario:** Staff were using an old office chat tool that sends messages in plain text.
**Approach:** Located the chat session and used **Follow TCP Stream** to read the whole conversation.
**Finding:** The chat was fully readable, and sensitive information (credentials) was shared in clear text.

### Lab 5 – Follow TCP Stream, Part 2
**Scenario:** An admin suspected a secret was left inside one of five intranet pages.
**Approach:** Followed the HTTP streams for each page and inspected the raw HTML responses.
**Finding:** `/staff.html` contained a hidden **HTML comment** with an internal note. Comments are invisible in the browser but fully visible in the raw traffic.

### Lab 6 – HTTP Plain Text Credentials
**Scenario:** An old staff portal still ran on plain HTTP.
**Approach:** Captured the sign-in traffic and inspected the HTTP request.
**Finding:** Login details were sent unencrypted and could be read directly from the capture, which shows why HTTPS is required for authentication.

### Lab 7 – Brute Force Catch
**Scenario:** An FTP server owner reported many failed sign-ins.
**Approach:** Analyzed the FTP traffic to find who was guessing passwords and whether any attempt succeeded.
**Finding:** Identified the source of the repeated failed logins and checked the server responses to confirm whether the attacker gained access.

### Lab 8 – VoIP Call Playback (RTP)
**Scenario:** A phone call was made through the company VoIP system.
**Approach:** Captured the traffic, located the call, and used Wireshark's **RTP Player** to play the audio back.
**Finding:** The call was carried as two RTP streams (one per direction) between 192.168.10.45 and 192.168.10.52, encoded with G.711 PCMU. Because the audio is unencrypted, anyone capturing the traffic can reconstruct and listen to the conversation.

## Key Takeaways

- Port scans show up as one source hitting many ports quickly; closed ports answer with RST, ACK.
- Plain-text protocols (HTTP, FTP, old chat tools) expose credentials and data to anyone capturing traffic.
- Follow TCP Stream is one of the fastest ways to turn raw packets into readable evidence.
- Repeated failed logins from one IP are the signature of a brute-force attack.
- Unencrypted VoIP (RTP) calls can be replayed from a capture, so voice traffic should be encrypted (SRTP).
