# Project 09 – Linux Workstation Hardening (Ubuntu Desktop)

## Overview
This project focuses on hardening an Ubuntu Desktop workstation by applying system, network, service, and user-level security controls.  
The objective is to reduce the attack surface, enforce secure defaults, and document a realistic Blue Team hardening workflow suitable for SOC and defensive roles.

All configurations were applied on a standard Ubuntu Desktop installation without converting it into a server environment.

---

## Environment
- Operating System: Ubuntu Desktop
- User Privileges: Local user with sudo access
- Scope: Single workstation (standalone system)

---

## Step 0 – Initial System Preparation
Objective: Ensure the system is fully updated before applying security controls.

## Actions

### bash

sudo apt update && sudo apt upgrade -y

### Outcome
System packages updated and baseline stabilized.

## Step 1 – System Information and Baseline Review
Objective: Understand the current system state before applying changes.

## Actions

uname -a
lsb_release -a
ip a
ss -tuln

### Outcome
Baseline system, network interfaces, and listening services documented.

## Step 2 – SSH Hardening
Objective: Secure remote access by enforcing strong authentication and reducing exposure.

## Actions

### 2.1 Install and verify SSH
sudo apt install openssh-server -y
sudo systemctl status ssh
### 2.2 Default configuration file backup
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
### 2.3 Generate SSH key pair (client side)
ssh-keygen -t ed25519
### 2.4 Deploy public key
ssh-copy-id username@localhost
### 2.5 Harden SSH configuration
sudo nano /etc/ssh/sshd_config

### Applied settings:
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
LoginGraceTime 30

### 2.6 Restart SSH service
sudo systemctl restart ssh

### Outcome
SSH access restricted to key-based authentication with hardened defaults.

## Step 3 – Firewall Hardening (UFW)
Objective: Control inbound and outbound traffic using a host-based firewall.

## Actions

sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose

### Outcome
Firewall enabled with restrictive inbound rules.

## Step 4 – Logging and Auditing Verification
Objective: Ensure logging services are active and available for incident analysis.

## Actions

systemctl status rsyslog
journalctl -p 3 -xb

### Outcome
System logging confirmed operational.

## Step 5 – Service Exposure Reduction (Avahi)
Objective: Reduce local network discovery exposure.

## Actions

systemctl status avahi-daemon
sudo systemctl stop avahi-daemon
sudo systemctl disable avahi-daemon

### Verification:
ss -ulpn | grep 5353

### Outcome
mDNS service disabled, reducing local network visibility.

## Step 6 – Privilege and Account Review
Objective: Review privilege boundaries and sudo access.

## Actions

cut -d: -f1 /etc/passwd | column
grep -E "bash|ssh" /etc/passwd
getent group sudo

### Optional sudo hardening:
sudo visudo

### Outcome
Privilege usage reviewed and documented.

## Step 7 – GNOME Desktop Security Hardening
Objective: Reduce user-level attack surface and local information exposure.

## Actions

### Automatic screen lock
gsettings set org.gnome.desktop.session idle-delay 300
### Require password on unlock
gsettings set org.gnome.desktop.screensaver lock-enabled true
gsettings set org.gnome.desktop.screensaver lock-delay 0
### Disable lock screen notifications
gsettings set org.gnome.desktop.notifications show-in-lock-screen false
### Disable recent files history
gsettings set org.gnome.desktop.privacy remember-recent-files false
### Disable usage statistics
gsettings set org.gnome.desktop.privacy send-software-usage-stats false
### Disable removable media auto-mount
gsettings set org.gnome.desktop.media-handling automount false
gsettings set org.gnome.desktop.media-handling automount-open false

### Outcome
Desktop session hardened against unattended access and data exposure.

## Final Status

- SSH hardened
- Firewall enforced
- Unnecessary services disabled
- Logging verified
- Desktop environment secured

This project demonstrates realistic Linux workstation hardening aligned with Blue Team and SOC defensive practices.
