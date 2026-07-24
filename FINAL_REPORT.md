# Network Security and Penetration Testing Final Report

## Executive Summary

This engagement simulates a corporate office network that has experienced unauthorized access attempts. The objective was to perform a structured penetration test to identify entry points, assess exploitable vulnerabilities, and provide actionable hardening recommendations. The test workflow covered network security fundamentals, reconnaissance, vulnerability scanning, exploitation, and network hardening.

Key findings include:
- Nmap reconnaissance identified multiple exposed services on the internal host, including FTP, Samba, HTTP, and MySQL.
- Wireshark analysis captured unencrypted credential transmission for phpMyAdmin and FTP, demonstrating network exposure risks.
- A Nessus vulnerability scan reported 73 issues across critical, high, medium, low, and informational severities.
- Metasploit exploitation confirmed unauthenticated root compromise through the vsftpd 2.3.4 backdoor and a Samba username map script command injection vulnerability.

This report is intended for network security stakeholders and decision makers who must prioritize remediation, improve monitoring, and harden the corporate internal network against unauthorized access.

## 1. Introduction to Network Security

Network security is the discipline of protecting data, systems, and resources as information travels across computing environments. It encompasses policies, technologies, and controls designed to prevent unauthorized access, misuse, modification, or denial of service. A strong network security posture must consider the confidentiality, integrity, and availability of systems across physical, virtual, and cloud-based networks.

Principles of network security include:
- **Defense in depth:** Implementing multiple layers of security controls so that if one control fails, others still protect the network.
- **Least privilege:** Granting systems and users only the access needed to perform their role, reducing the attack surface.
- **Segmentation:** Dividing systems into separate zones or VLANs so attackers cannot move laterally easily after gaining access.
- **Monitoring and logging:** Continuously collecting and analyzing logs and traffic to detect anomalies and respond quickly to incidents.
- **Patch management:** Keeping operating systems and applications updated to remove known vulnerabilities before attackers exploit them.

Common network security threats include:
- **Unauthorized access:** Attackers gain entry to internal resources through weak authentication, exposed services, or credential theft.
- **Man-in-the-middle attacks:** Unencrypted traffic allows adversaries on the same network segment to intercept and tamper with communications.
- **Service exploitation:** Vulnerable host services like FTP, SMB, or web applications can be exploited to execute code, elevate privileges, or exfiltrate data.
- **Denial of Service (DoS):** Attackers overload network resources or exploit protocol weaknesses to disrupt availability.
- **Reconnaissance and information leakage:** Attackers use scanning, fingerprinting, and banner grabbing to map the network and choose high-value targets.

Ethical hacking methodologies provide a structured way to test security controls without harming production systems. A typical ethical hacking engagement follows these stages:
1. **Planning and scoping:** Define the rules of engagement, authorized targets, and permitted tools.
2. **Reconnaissance:** Collect information about network topology, hosts, services, and applications.
3. **Vulnerability analysis:** Scan systems and services to identify weaknesses.
4. **Exploitation:** Attempt to use identified vulnerabilities to gain access or compromise systems.
5. **Post-exploitation:** Assess the impact of access, gather evidence, and identify persistence mechanisms.
6. **Reporting:** Document findings, evidence, risk rating, and remediation recommendations.

Ethical hackers must operate under explicit authorization. This safeguards both the network owner and the tester and ensures findings can be addressed responsibly. In this scenario, the simulated corporate network represents an environment where internal unauthorized access attempts must be assessed and mitigated.

## 2. Network Reconnaissance and Information Gathering

Reconnaissance is the first practical step in a penetration test. The goal is to understand the target network and identify exposed services that could become attack vectors.

### 2.1 Nmap Reconnaissance

Nmap was used to perform host discovery, port scanning, service version detection, and operating system fingerprinting against the internal target host at `192.168.70.136`.

Findings from the Nmap scans included:
- The target host was live and reachable on the internal TCP network.
- Multiple ports were open, including common service ports such as:
  - `21/TCP` (FTP)
  - `22/TCP` (SSH)
  - `23/TCP` (Telnet)
  - `80/TCP` (HTTP)
  - `139/TCP` and `445/TCP` (Samba/SMB)
  - `3306/TCP` (MySQL)
  - `5900/TCP` (VNC)
  - `6667/TCP` (IRC)
  - `8009/TCP` (Tomcat AJP)
- Service version detection and OS fingerprinting suggested the target was a vulnerable Metasploitable-style Linux host with outdated services and weak default configurations.

Security concerns from the Nmap findings:
- Exposed FTP and Telnet services transmit credentials in plaintext and are inappropriate for corporate networks.
- Legacy services like VNC, IRC, and older Samba versions are high-risk because they are commonly targeted by automated scanners.
- Open administrative and remote access ports increase the attack surface for lateral movement and privilege escalation.

Screenshots documenting Nmap reconnaissance are available in the repository under `Screenshots/nmap/`, including:
- `Host Discovery(live host).png`
- `Nmap Full TCP Port and Service Version Scan of the Metasploitable 2 Target (part1).png`
- `Nmap Full TCP Port and Service Version Scan of the Metasploitable 2 Target(part2).png`
- `Nmap Full TCP Port and Service Version Scan of the Metasploitable 2 Target (part3).png`
- `Operating system Detection.png`

### 2.2 Wireshark Traffic Analysis

Wireshark captured network traffic between the attacking host `192.168.70.135` and the target host `192.168.70.136`. The analysis focused on identifying unencrypted authentication traffic and protocol-level weaknesses.

Key observations:
- A phpMyAdmin login attempt was captured over HTTP, showing the submitted username and password in the request body. The form submission was visible in plaintext because HTTPS was not enforced.
- An FTP session was captured that included the FTP username and password in cleartext. This confirmed that unencrypted network protocols exposed credentials to any observer on the same subnet.

Security concerns from Wireshark findings:
- Administrative web interfaces such as phpMyAdmin must never be accessible over HTTP without TLS.
- Cleartext authentication protocols like FTP allow credential interception and active attacks such as replay or session hijacking.
- Even failed login attempts leak sensitive information when transmitted without encryption.

The repository includes Wireshark evidence in `Screenshots/wireshark/` and the summary text in `Screenshots/report finding of wireshark.txt`.

## 3. Vulnerability Scanning

Vulnerability scanning is essential to identify known weaknesses in network services and host configurations. Nessus Essentials was used with a basic network scanning policy to assess the target host.

### 3.1 Scan Results

The Nessus scan took approximately 14 minutes and identified 73 vulnerabilities across all severity levels. The distribution of findings included:
- **Critical:** 6+ items
- **High:** 4+ items
- **Medium:** 4+ items
- **Low:** 3 items
- **Informational:** ~50 items

Notable vulnerabilities included:
- `UnrealIRCd Backdoor` — a widely exploited backdoor in an outdated IRC daemon.
- `VNC weak password` — remote desktop access protected by a weak or default password.
- `SSLv2/SSLv3` support — obsolete cryptographic protocols that expose the host to protocol downgrade and MITM attacks.
- `Ghostcat` — Apache Tomcat AJP connector vulnerability that can allow remote file inclusion.
- `Bind Shell Backdoor` — a backdoor service that grants remote shell access.
- `Ubuntu 8.04 End-of-Life` — an outdated operating system with no security updates.
- `NFS world-readable shares` — improperly configured network file system shares exposing sensitive files.
- `Samba Badlock` — a known vulnerability in Samba negative channel bindings.
- `rlogin/rsh services` — insecure remote shells transmitting credentials in plaintext.
- `Telnet cleartext credentials` and `anonymous SSL ciphers`.

### 3.2 Risk Assessment

The scan results confirm that the target host is deeply insecure and would be a high-value target for attackers. The combination of unauthenticated backdoors, deprecated protocols, and exposed administrative services creates a dangerous environment.

Risks include:
- Rapid remote compromise through unauthenticated service exploits.
- Credential interception and replay on unencrypted protocols.
- Lateral movement enabled by weak or world-readable file shares.
- Increased persistence likelihood from old OS and unpatched application components.

Screenshots from the Nessus scan are available under `Screenshots/nessus for vulnerability scanning/`, including `Result of scanning part1.png` through `Result of scanning part4.png`.

## 4. Penetration Testing and Exploitation

A practical exploitation exercise was conducted using Kali Linux as the attacker system and Metasploitable as the vulnerable target environment. The goal was to confirm whether identified vulnerabilities could be exploited to achieve unauthorized system access.

### 4.1 Test Environment

The attack lab consisted of:
- **Attacker host:** Kali Linux
- **Target host:** Metasploitable-style vulnerable server, IP `192.168.70.136`
- **Attacker host IP:** `192.168.70.135`

Screenshots documenting the environment include:
- `kali linux(Attacker machine) ip address.png`
- `metasploitable2(Victim server) ip address.png`
- `ping(verify connection) to victim server.png`

### 4.2 Exploitation Pathways

Two independent, unauthenticated vulnerabilities were exploited successfully, demonstrating the target host’s extreme exposure.

#### vsftpd 2.3.4 Backdoor (CVE-2011-2523)

The vsftpd 2.3.4 FTP server on port 21 contained a hardcoded backdoor. The exploit was performed using the Metasploit module:
- `exploit/unix/ftp/vsftpd_234_backdoor`

Successful exploitation yielded a Meterpreter session with root privileges, which was confirmed by reading `/etc/passwd` and enumerating local user accounts.

#### Samba username map script command injection (CVE-2007-2447)

A second exploitation path was the Samba usermap script vulnerability, which allows command execution within the Samba service. The Metasploit module used was:
- `exploit/multi/samba/usermap_script`

This exploit also produced a root-level reverse shell, confirming that the target host was vulnerable through multiple service vectors.

### 4.3 Impact

The successful exploit chain demonstrates that the target environment could be fully compromised with minimal attacker effort. The two independent vulnerabilities show that attackers do not need a single point of failure; multiple exposed services can each provide a complete intrusion path.

The real-world implications for a corporate network include:
- Unauthorized access to proprietary data and intellectual property.
- Complete takeover of critical internal servers.
- Ability to install persistent backdoors or pivot to other segments.
- Loss of trust, regulatory non-compliance, and potential financial damage.

This exploitation phase validates the earlier reconnaissance and vulnerability scanning findings and highlights the most urgent remediation actions.

## 5. Network Hardening and Security Recommendations

Effective corporate network hardening requires both technical controls and operational improvements. The following recommendations are based on the observed findings and represent priorities for reducing risk:

### 5.1 Immediate Remediation

- **Disable or remove insecure services:** Remove FTP, Telnet, rlogin, and other obsolete protocols from the network.
- **Patch vulnerable systems:** Update or replace outdated software such as vsftpd 2.3.4, Samba versions with Badlock, Apache Tomcat, and outdated Linux distributions.
- **Enforce TLS/HTTPS:** Ensure all administrative web interfaces, especially phpMyAdmin, use TLS and redirect HTTP to HTTPS.
- **Harden Samba and NFS:** Restrict share access to authorized hosts and eliminate world-readable shares.
- **Secure remote access:** Replace weak VNC and SSH configurations with encrypted access, strong authentication, and multi-factor authentication where possible.

### 5.2 Architectural Improvements

- **Network segmentation:** Separate user, server, and administrative zones to reduce lateral movement and isolate critical assets.
- **Firewall and access control:** Use firewall rules to permit only required traffic and deny all other inbound and outbound connections by default.
- **Intrusion detection:** Deploy IDS/IPS monitoring for suspicious service activity, brute-force attempts, and protocol anomalies.
- **Asset inventory and vulnerability management:** Maintain an accurate inventory of systems and schedule regular vulnerability scans and patch windows.

### 5.3 Operational Controls

- **Strong credential policies:** Require complex passwords, rotate credentials regularly, and disable default accounts.
- **Logging and auditing:** Capture authentication, service, and configuration events centrally for real-time analysis and forensic investigation.
- **Incident response readiness:** Maintain a documented response plan and test it with tabletop exercises and simulated attacks.
- **User education:** Train staff on phishing, social engineering, and secure remote access best practices.

## 6. Hardening Guide (300 Words)

A secure corporate network must combine modern controls, strict configuration, and continuous monitoring. Begin by removing obsolete services and enforcing encrypted communication across the network: disable FTP, Telnet, and legacy remote shells, then require TLS for all administrative and application traffic. Use a zero-trust mindset where access is granted only on a need-to-know basis and every connection is verified before it is allowed.

Network segmentation is essential. Divide the environment into separate zones for user workstations, servers, and management. Restrict traffic between zones with firewalls and access control lists so attackers cannot move laterally from a compromised host to a critical backend system. Place administrative tools behind dedicated management networks or VPNs with strong authentication.

Patch management and configuration hygiene are also critical. Keep operating systems and application services up to date, and retire unsupported platforms. Use configuration baselines to ensure services are not exposed unnecessarily and that shared resources are accessible only to authorized systems.

Monitoring and detection should be active. Implement network and endpoint logging, and use intrusion detection systems to flag suspicious traffic patterns. Centralized log analysis helps identify credential theft, unauthorized access attempts, and abnormal communication at an early stage.

Finally, build security awareness into daily operations. Establish clear policies for user credentials, privilege escalation, and remote access. Regularly test the environment with authorized penetration tests so that vulnerabilities are discovered before attackers exploit them.

These steps provide a strong foundation for protecting corporate networks against unauthorized access and reducing the impact of compromise.

## 7. Conclusion

The simulated corporate network showed a range of serious security weaknesses from reconnaissance through exploitation. Open services, unencrypted traffic, outdated software, and unauthenticated backdoors allowed non-destructive penetration testing to achieve root compromise. The evidence collected in this repository supports a prioritized remediation effort and a hardened internal network architecture.

The professional submission package includes this report, the repository README, and supporting screenshots to demonstrate the full testing workflow. Implementing the recommendations in this report will significantly reduce the risk of unauthorized access and improve overall corporate network resilience.
