# Scan Observations – Project 08

## Summary
During this project, a lateral movement attempt using **Remote PowerShell (WinRM)** was successfully executed and detected. The activity was fully observable using native Windows logging mechanisms.

---

## Observed Behavior

### Authentication Events
- **Event ID 4624**
  - Logon Type: 3 (Network)
  - Account: Atacante
  - Source: Internal host (Windows A)

This confirms credential reuse and successful authentication from another internal system.

---

### Privilege Assignment
- **Event ID 4672**
  - Indicates assignment of special privileges during logon
  - Common during remote management sessions

---

These processes are strongly associated with PowerShell remoting activity.

---

## Security Impact
- Internal credential reuse
- Unauthorized lateral access
- Potential for further compromise if not contained

---

## Defensive Gaps Identified
- WinRM enabled internally without strict access control
- Credential reuse across hosts
- Lack of network-level restrictions for management protocols

---

## Recommendations
- Limit WinRM access via firewall rules
- Monitor Event IDs 4624 and 4672
- Enable PowerShell logging organization-wide
- Enforce credential hygiene and least privilege
