# Network Security and Penetration Testing Project

## Overview
This repository contains the final deliverables for the corporate network penetration testing scenario: identifying unauthorized access points, documenting reconnaissance, vulnerability scanning, exploitation, and hardening recommendations.

## Project Scope
- A 500-word introduction to network security principles, threat categories, and ethical hacking methodologies.
- A documented reconnaissance phase using Nmap and Wireshark.
- A vulnerability scanning assessment using Nessus/OpenVAS.
- A penetration testing section showing exploitation of Metasploitable services using Kali Linux and Metasploit.
- A 300-word corporate network hardening guide.
- A full 1500+ word report summarizing the entire engagement.
- Supporting screenshots of findings and exploit steps.

## Repository Contents
- `README.md` — This project summary and navigation.
- `FINAL_REPORT.md` — Complete written report covering all tasks and recommendations.

## Supporting Evidence
Screenshots and captured findings are included in the existing `Screenshots` folder:
- `Screenshots/nmap/` — Nmap host discovery, port scan, service discovery, and OS detection.
- `Screenshots/nessus for vulnerability scanning/` — Nessus scan results and vulnerability detail screens.
- `Screenshots/Pentestinfg and exploitation throuh metasploit/` — Kali attacker configuration, target configuration, exploit steps, and proof of compromise.
- `Screenshots/report finding of wireshark.txt` — Text summary of Wireshark findings demonstrating cleartext credential exposure.

## Key Findings
- Nmap reconnaissance revealed multiple open services including FTP, SSH, Telnet, HTTP, Samba, and MySQL on the target network.
- Wireshark analysis confirmed sensitive credentials and administrative traffic were exposed in cleartext (HTTP phpMyAdmin login, FTP session data).
- Nessus scan identified 73 vulnerabilities across all severity levels, including multiple critical and high-risk items.
- Metasploit exploitation successfully leveraged the vsftpd 2.3.4 backdoor and Samba username map script vulnerability to achieve root-level access.

## How to Use This Repository
1. Open `FINAL_REPORT.md` to review the full 1500+ word executive report.
2. Review the screenshot folders to validate the attack workflow.
3. Use the `README.md` as a navigation guide for the submission package.

## Notes
- This repository is structured for professional review and grading.
- The report is written to be self-contained and accessible to network security stakeholders.
- The supplied screenshot folders provide visual evidence of reconnaissance, scanning, and exploitation.
