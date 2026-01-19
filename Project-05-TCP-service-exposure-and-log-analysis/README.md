# Project 05 – Pre-Authentication RDP Transport Blocking Analysis (VPN Environment)

## Project Type
Blue Team / SOC Foundations

## Difficulty Level
Intermediate – Junior SOC Tier 1

## Project Objective
The objective of this project is to analyze and document a real-world scenario where a Windows native TCP service (Remote Desktop Protocol) is exposed over a VPN network, but all connection attempts are blocked before reaching the authentication phase.

This project focuses on understanding transport-level failures, authentication visibility limitations, and realistic detection blind spots from a SOC analyst perspective.

---

## Lab Environment
- Attacker Machine: Kali Linux
- Target Machine: Windows 10 / Windows 11
- Network Connectivity: Encrypted VPN network
- Firewall: Windows Defender Firewall
- Analyzed Service: Remote Desktop Protocol (RDP – TCP 3389)

---

## Scope and Assumptions
- VPN connectivity between systems is assumed to be operational
- VPN deployment and tunnel validation are out of scope
- The goal is analysis, not successful access
- No exploitation is performed
- All actions are executed in a controlled lab environment

---

## Step 1 – Baseline Scan (Hardened State)
Before enabling any Windows services, a baseline scan is executed from the Kali Linux machine.

Command executed:  nmap <VPN_WINDOWS_IP>

Observed result:
- No open TCP ports
- No exposed services

This confirms that the Windows host starts in a hardened state.

---

## Step 2 – Service Selection
Remote Desktop Protocol (RDP) is selected for analysis because:
- It is a native Windows service
- It is a common real-world attack vector
- It usually generates authentication-related security logs
- It is highly relevant for SOC monitoring

---

## Step 3 – Enable RDP Service on Windows
On the Windows target system:
- Remote Desktop is enabled via system settings
- Network Level Authentication (NLA) is enabled by default
- NLA is later disabled for testing purposes


---

## Step 4 – Firewall Configuration (VPN-Scoped Exposure)
A custom inbound firewall rule is created to allow RDP access only from the VPN subnet.

PowerShell command executed as Administrator:
New-NetFirewallRule 
-DisplayName "Allow RDP from VPN"
-Direction Inbound 
-Protocol TCP
-LocalPort 3389 
-Action Allow
-RemoteAddress <VPN_SUBNET>


This configuration ensures controlled exposure and prevents public access.

---

## Step 5 – Post-Exposure Port Scan
After enabling the service and firewall rule, a targeted scan is performed from Kali Linux.

Command executed:  nmap <VPN_WINDOWS_IP>

Observed result:
- TCP port 3389 responds as open
- The service is reachable at the transport level

---

## Step 6 – RDP Connection Attempts
Multiple RDP connection attempts are executed from the Kali Linux system using different parameters.

Commands tested:
xfreerdp3 /v:<VPN_WINDOWS_IP>
xfreerdp3 /v:<VPN_WINDOWS_IP> /u:testuser /p:wrongpassword
xfreerdp3 /v:<VPN_WINDOWS_IP> /sec:rdp


Observed behavior:
- All connection attempts fail during transport or handshake negotiation
- Client-side errors include "handshake failed" and "transport failed"
- No connection reaches the authentication phase

---

## Step 7 – Windows Log Analysis
After each connection attempt, Windows Event Viewer is reviewed.

Logs inspected:
- Windows Logs → Security
- Windows Logs → System

Observed results:
- No Event ID 4624 (successful logon)
- No Event ID 4625 (failed logon)
- No RDP authentication-related events

---

## Step 8 – Analysis and Interpretation
The absence of authentication logs confirms that all RDP connection attempts were blocked before reaching the authentication layer.

This indicates:
- Transport-level or protocol negotiation blocking
- Credential validation never occurred
- Host-based authentication logging was not triggered

This behavior is consistent with pre-authentication security controls and VPN overlay constraints.

---

## Step 9 – Cleanup and Hardening
After completing the analysis:
- Remote Desktop is disabled
- The custom firewall rule is removed

PowerShell command executed:  Remove-NetFirewallRule -DisplayName "Allow RDP from VPN"


A final scan confirms that TCP port 3389 is no longer accessible.

---

## Lessons Learned
- Not all blocked access attempts generate authentication logs
- Pre-authentication controls can create visibility gaps
- VPN overlays can affect protocol negotiation
- Absence of logs can still indicate effective security enforcement
- SOC analysts must understand detection limitations

---

## Skills Demonstrated
- Windows service management
- Firewall rule scoping
- VPN-restricted service exposure
- Transport vs authentication-layer analysis
- Host-based log investigation
- SOC-level documentation

---

## Conclusion
This project demonstrates a realistic Blue Team scenario where access attempts are blocked before authentication, resulting in limited host-level logging. Understanding these behaviors is essential for accurate SOC analysis and effective incident triage.





