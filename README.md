# Home Lab — Suricata IDS on Kali Linux

## Overview
For this lab, I repurposed a spare computer and turned it into a virtualization server using Proxmox. Inside Proxmox, I created a Kali Linux virtual machine to run this home lab.
I then installed Suricata, an open-source network intrusion detection system, to monitor live network traffic and generate real world alerts.
The goal was to understand how an IDS works in practice, from installation and configuration to detecting actual network activity in real time.

## Lab Environment
- **OS:** Kali Linux (Virtual Machine)
- **Hypervisor:** Proxmox (installed on spare desktop)
- **Network Interface:** eth0
- **IP Address:** 192.168.1.235
- **Suricata Version:** 8.0.4

## Steps Completed

### Installation

- Updated package list and installed Suricata using the following commands:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install suricata -y
```

Verified successful installation:

```bash
suricata --version
```

Output confirmed Suricata 8.0.4 was installed and running.

### Configuration

- Opened the Suricata configuration file in VS Code using:

```bash
sudo code /etc/suricata/suricata.yaml --no-sandbox --user-data-dir=/root/.vscode-root
```

Made the following changes inside the file:

- Verified **HOME_NET** was already set to cover my subnet `192.168.1.0/24`
- Confirmed **default-log-dir** was set to `/var/log/suricata/`
- Verified **eve-log** was enabled with JSON output and filename `eve.json`

### Rule Updates

Downloaded and installed the latest Emerging Threats ruleset using:

```bash
sudo suricata-update
```

This automatically downloaded and loaded over 65,000 detection rules into Suricata.

### Running Suricata

First identified my active network interface and IP address:

```bash
ip a
```

This showed my interface was **eth0** with IP address **192.168.1.235**

Then launched Suricata in system mode listening on eth0:

```bash
sudo suricata -c /etc/suricata/suricata.yaml -i eth0
```

Confirmed engine started successfully with the following output:
- Suricata 8.0.4 running in SYSTEM mode
- Threads created: W2 FM1 FR1
- Engine started

### Alert Monitoring

- Opened a second terminal and streamed the Suricata log file in real time:

```bash
sudo tail -f /var/log/suricata/eve.json | grep "alert"
```

Then installed jq for cleaner, formatted output:

```bash
sudo apt install jq -y
```

Ran the improved alert filter:

```bash
sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```

This displayed each alert in clean JSON format showing:
- **timestamp** — when the alert fired
- **in_iface** — which interface detected it
- **event_type** — confirmed as alert
- **signature** — the rule that triggered
- **category** — type of threat detected
- **severity** — how serious the alert was on a scale of 1 to 4

### Traffic Generation

- Opened a third terminal and generated ICMP traffic by pinging Google's DNS server:

```bash
ping -c 4 8.8.8.8
```

Result:
- 4 packets transmitted
- 4 packets received
- 0% packet loss
- Alerts fired in real time on the monitoring terminal

Then triggered a test IDS rule using a URL specifically designed to test IDS detection:

```bash
curl http://testmynids.org/uid/index.html
```

Result:
- Server responded with: uid=0(root) gid=0(root) groups=0(root)
- Multiple alerts immediately fired in the monitoring terminal
- Alerts showed full details including signature, category and severity

Both tests confirmed Suricata was successfully detecting and logging 
real network traffic in real time.

## What I Learned

- How to install and configure Suricata on a Linux system using the command line
- How to edit system configuration files using VS Code on Kali Linux
- How to identify my network interface and IP address using the `ip a` command
- How to update and load over 65,000 detection rules using `suricata-update`
- How to read and interpret IDS alerts in JSON format using `jq`
- The difference between IDS (detection only) and IPS (active blocking) mode
- How to generate test traffic to verify an IDS is working correctly
- How to build and document a home lab using Proxmox as a virtualization platform

## Tools Used

- **Suricata 8.0.4** — Network IDS/IPS engine
- **Kali Linux** — Security-focused Linux distribution
- **Proxmox** — Virtualization platform (hypervisor)
- **VS Code** — Configuration file editor
- **jq** — JSON parser for alert filtering
- **curl** — Used to trigger test IDS rules
- **ping/ICMP** — Used to generate test network traffic
- **Emerging Threats ruleset** — Open source IDS rule database

## Next Steps
- **Expand the home lab** — Add a Windows Server VM to practice 
  Active Directory and endpoint monitoring
- **Set up Suricata in IPS mode** — Move from detection only to 
  actively blocking threats
- **Write custom Suricata rules** — Create rules to detect specific 
  traffic patterns on my network
- **Integrate with a SIEM** — Forward Suricata alerts into a tool 
  like Splunk or Security Onion for centralized log analysis
- **Practice with more Kali Linux tools** — Use tools like Nmap to 
  generate more complex traffic and see how Suricata responds
- **Continue WGU Cybersecurity degree** — Apply these hands-on skills 
  to coursework in network security and incident response
