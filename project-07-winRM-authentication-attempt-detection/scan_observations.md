# Scan Observations – Project M7

## Baseline State
The Windows host was reachable over the VPN but presented a fully filtered port profile, indicating a hardened initial configuration.

---

## WinRM Exposure Analysis
Targeted scanning confirmed the presence of the WinRM service on TCP port 5985, with access restricted by the firewall.

---

## Authentication Behavior
Initial authentication attempts did not reach the authentication layer due to firewall restrictions. After allowing WinRM access from the VPN subnet, authentication attempts were processed and rejected.

---

## Event Log Findings
The primary security event generated during the authentication attempt was:

- Event ID 4672 – Special privileges assigned to new logon

This event indicates that Windows evaluated privileged access during a remote administrative authentication attempt.

No Event ID 4625 entries were observed during the process.

---

## Security Interpretation
The presence of Event ID 4672 demonstrates that WinRM authentication attempts can trigger privileged log evaluation even when access is denied. This behavior is significant for SOC detection and monitoring of lateral movement attempts.

---

## Analyst Takeaway
- WinRM activity is highly sensitive and security-relevant
- Privileged log events provide strong detection signals
- Firewall configuration directly affects authentication visibility
- Absence of traditional failed logon events does not imply lack of detection

These observations reflect real-world enterprise SOC monitoring scenarios.
