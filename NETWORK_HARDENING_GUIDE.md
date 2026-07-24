# Network Hardening Guide

A secure corporate network is built on a combination of modern controls, proper configuration, and ongoing monitoring. This guide summarizes core hardening practices that protect internal systems from unauthorized access.

## 1. Remove Obsolete and Insecure Services

Start by identifying and disabling legacy services that transmit data in plaintext or are no longer necessary. Common unsafe services include:
- FTP
- Telnet
- rlogin
- unsupported VNC and database services

Replace them with secure alternatives such as SFTP, SSH, and fully encrypted administrative interfaces.

## 2. Enforce Encryption Everywhere

All administrative and application traffic must use TLS/HTTPS. Administrative web portals like phpMyAdmin should never accept passwords over unencrypted channels. Use valid certificates and configure strong cipher suites to prevent downgrade attacks.

## 3. Apply Strong Access Controls

Implement least privilege and segmented access.
- Limit network access with firewall rules and access control lists.
- Place servers, workstations, and management interfaces into separate network zones.
- Restrict service exposure to only the systems that require access.

## 4. Maintain Patch and Configuration Hygiene

A strong patch management program is essential. Regularly update operating systems, services, and applications, and remove unsupported software. Use configuration baselines to enforce secure settings and reduce unnecessary exposure.

## 5. Monitor, Detect, and Respond

Deploy logging and intrusion detection systems to track authentication attempts, service access, and abnormal network traffic. Centralize logs for analysis, and ensure alerts are tuned to detect suspicious behavior quickly.

## 6. Harden Authentication

Use multi-factor authentication wherever possible, especially for administrative access. Require strong passwords and rotate credentials regularly. Disable default accounts and ensure orphaned or inactive accounts are removed promptly.

## 7. Practice Incident Preparedness

Document an incident response plan and test it regularly with drills. Ensure your team can quickly isolate compromised systems and recover from unauthorized access.

## Summary

A hardened corporate network is not a one-time effort. It is a continuous process that combines service minimization, encryption, segmentation, patching, monitoring, and strong authentication. Applying these practices greatly reduces the likelihood of successful unauthorized access and improves the organization’s ability to detect and recover from attacks.
