# Project 08 – Lateral Movement via Remote PowerShell (WinRM)

## Overview
This project simulates a real-world **post-compromise lateral movement** scenario inside a Windows environment.  
A previously compromised internal host is used to move laterally to another Windows system using **Remote PowerShell (WinRM)** and valid credentials.

The objective is to demonstrate:
- Successful lateral movement
- Proper detection using Windows logs
- Incident response and containment actions

This project focuses on **enterprise-style attacks** and **SOC / Blue Team detection workflows**, rather than perimeter-based intrusion attempts.

---

## Environment
- Two Windows machines on the same internal network / VPN
- **Windows A**: Compromised internal host (attacker-controlled)
- **Windows B**: Target host
- Windows Pro editions
- No services exposed to the Internet

---

## Attack Scenario
1. An attacker has already compromised **Windows A**
2. Valid credentials are obtained and reused internally
3. The attacker performs lateral movement using **WinRM / PowerShell Remoting**
4. Commands are executed remotely on **Windows B**
5. Security events are generated and detected
6. Incident response actions are applied

---

## Step-by-Step Execution


### Step 1 – WinRM Verification (Windows B)

Powershell:

Get-Service WinRM

### Step 2 – Lateral User Creation (Windows B)

net user Atacante P@ssw0rd123! /add

### Step 3 – Permission Assignment (Windows B)

The user is added to the Remote Management Users local group, which allows PowerShell remoting without full administrator privileges.

net localgroup "Usuarios de administración remota" Atacante /add

### Verification:

net localgroup "Usuarios de administración remota"

### Step 4 – WinRM Firewall Validation (Windows B)

Test-NetConnection localhost -Port 5985

### Step 5 – TrustedHosts Configuration (Windows A)

Because the connection is performed using an IP address, NTLM authentication is required.

Set-Item WSMan:\localhost\Client\TrustedHosts -Value "<IP_WINDOWS_B>" -Force

Restart-Service WinRM

### Step 6 – Lateral Movement Execution (Windows A)

Enter-PSSession -ComputerName <IP_WINDOWS_B> -Credential Atacante

Successful connection confirms lateral movement:

[IP_WINDOWS_B]: PS C:\Users\Atacante>

### Step 7 – Post-Compromise Activity (Windows B)

whoami

hostname

Get-Process | Select-Object -First 5

### Exit session:

Exit-PSSession


## Detection and Evidence

### Security Logs

4624 – Successful Logon

Logon Type: 3 (Network)

Account Name: Atacante

Source IP: Windows A

4672 – Special privileges assigned


### Incident Response Actions

Disable-LocalUser -Name Atacante

Stop-Service WinRM

### Optional hardening:

Set-Item WSMan:\localhost\Client\TrustedHosts -Value ""


## Outcome

Lateral movement successfully simulated

Clear detection through multiple log sources

Effective containment and erradication

Realistic enterprise attack scenario demonstrated


## Conclusion

This project demonstrates how attackers move inside the network after initial compromise, and how defenders can detect and respond to such activity using native Windows telemetry.

The focus on WinRM-based lateral movement reflects real-world attacker behavior and common enterprise misconfigurations.
