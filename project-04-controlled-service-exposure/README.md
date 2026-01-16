# Project 04 – Controlled Service Exposure over Encrypted VPN Network

## Overview
This project demonstrates controlled service exposure and firewall rule management over an encrypted VPN network. The objective is to analyze how a Windows host behaves before and after a scoped firewall change, while maintaining a strong defensive security posture.

The project focuses on blue team principles such as change control, minimal exposure, and attack surface management in a remote collaboration scenario.

---

## Lab Environment
- Attacker Machine: Kali Linux
- Target Machine: Windows (Windows Defender Firewall enabled)
- Connectivity: Encrypted VPN network
- Security Control: Host-based firewall (Windows Defender Firewall)

Secure VPN connectivity is assumed to be established prior to the project and is not explicitly validated as part of the documented steps.

---

## Project Objectives
- Perform baseline reconnaissance against a remote Windows host
- Identify the impact of firewall restrictions over an encrypted VPN
- Expose a service in a controlled and scoped manner
- Compare pre-change and post-change behavior
- Revert changes and restore a hardened configuration
- Document findings using a defensive security mindset

---

## Methodology

### Baseline Scan
An initial scan was performed against the Windows host before any firewall changes were applied.

Command used:
nmap <VPN_WINDOWS_IP>

This scan established the baseline state of the system, which showed no exposed TCP services and filtered or no-response results.

---

### Controlled Exposure
A minimal exposure was selected for this project: ICMP Echo Request (ping). This choice allows for clear visibility changes without exposing TCP services or increasing the attack surface unnecessarily.

A firewall rule was created on the Windows system to allow ICMP traffic only from the VPN network.

Command used (PowerShell as Administrator):
New-NetFirewallRule -DisplayName "Allow ICMP from VPN Network" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow -RemoteAddress <VPN_SUBNET>

---

### Post-Change Validation
After applying the firewall rule, connectivity was tested from the remote Kali system.

Command used:
ping <VPN_WINDOWS_IP>

The target system responded successfully, confirming that ICMP traffic was permitted through the encrypted VPN.

---

### Post-Change Scan
A new scan was performed to observe changes in host discovery behavior after the firewall modification.

Command used:
nmap -sn <VPN_WINDOWS_IP>

The scan confirmed improved host visibility while maintaining firewall protection for TCP services.

---

### Rollback and Hardening
After analysis, the firewall rule was removed to restore the original hardened state.

Command used:
Remove-NetFirewallRule -DisplayName "Allow ICMP from VPN Network"

A final ping test confirmed that ICMP traffic was once again blocked.

---

## Tools Used
- Nmap
- Windows Defender Firewall
- Kali Linux
- VPN-based remote connectivity

---

## Conclusion
This project demonstrates how controlled firewall changes impact network visibility over an encrypted VPN connection. By limiting exposure to a single protocol and reverting the change after validation, the system maintains a strong defensive posture while enabling necessary functionality.

The lab highlights real-world blue team practices such as scoped access, change management, and rollback procedures.

