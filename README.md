# Home Lab — Suricata IDS on Kali Linux

## Overview
This lab demonstrates setting up and configuring Suricata, an open-source 
network intrusion detection system, on a Kali Linux virtual machine running 
on Proxmox. The goal was to understand how IDS works by capturing live 
network traffic and generating real-world alerts.

## Lab Environment
- **OS:** Kali Linux (Virtual Machine)
- **Hypervisor:** Proxmox (installed on spare desktop)
- **Network Interface:** eth0
- **IP Address:** 192.168.1.235
- **Suricata Version:** 8.0.4

## Steps Completed

###Installation
- Installed Suricata using apt package manager
- Verified installation with version check

###Configuration
- Edited suricata.yaml in VS Code
- Verified HOME_NET covered my subnet
- Confirmed eve-log JSON output was enabled

###Rule Updates
- Downloaded Emerging Threats ruleset using suricata-update
- Loaded 65,000+ detection rules

###Running Suricata
- Launched Suricata on eth0 interface
- Confirmed engine started successfully

###Alert Monitoring
- Streamed eve.json log in real time
- Used jq to filter and display clean alert output

###Traffic Generation
- Pinged 8.8.8.8 to generate ICMP traffic
- Used curl to trigger test IDS rule
- Observed real alerts firing with full details

## What I Learned
- How to configure an IDS on Linux from the command line
- How to read and interpret IDS alert logs in JSON format
- The difference between IDS (detection) and IPS (prevention) mode

## Tools Used
- Suricata 8.0.4
- Kali Linux
- Proxmox
- VS Code
- jq

## Next Steps
- Write custom Suricata rules
- Set up IPS mode to actively block threats
- Integrate alerts into a SIEM dashboard
