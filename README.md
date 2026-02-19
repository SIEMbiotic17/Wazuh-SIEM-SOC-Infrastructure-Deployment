# SOC Home Lab

## 1. Project Overview

This repository documents the design and implementation of a personal Security Operations Center (SOC) home lab built to simulate real-world blue team monitoring and detection workflows.

The primary goal of this project is to gain hands-on experience with log analysis, threat detection, and incident investigation by deploying a SIEM platform and a network intrusion detection system inside an isolated virtualized environment.

The lab is intentionally designed to run on limited hardware (8GB RAM, dual-core CPU), requiring careful resource allocation and architectural decisions to maintain performance while preserving realism.

Through this project, I aim to:

* Build practical experience with SIEM and IDS deployment
* Generate and analyze security events in a controlled lab
* Simulate attacker techniques and observe detection behavior
* Understand how alerts map to real-world attack patterns

This project is structured and documented incrementally to reflect a real SOC deployment lifecycle — from infrastructure setup to detection engineering and incident response analysis.

---

## 2. Lab Objectives

The lab is designed to replicate core functions of a basic Security Operations Center within a controlled virtual environment.

The key objectives of this setup are:

* Deploy a centralized Ubuntu Server to function as the SOC node
* Install and configure a SIEM platform for log aggregation and analysis
* Integrate a network-based intrusion detection system (IDS)
* Simulate attacker activity from a separate machine within an isolated network
* Generate, capture, and investigate security alerts
* Analyze detection logic and correlate events
* Document findings in a structured and professional format

The long-term goal is to build practical blue team experience by moving beyond theoretical concepts into hands-on monitoring and incident analysis.

---

## 3. Lab Architecture

The SOC lab is deployed using a virtualized environment to simulate a controlled internal network. The architecture separates monitoring, analysis, and attack simulation components to reflect a simplified real-world SOC structure.

The environment consists of:

* **Host Machine** – Primary system running VMware
* **Ubuntu Server (SOC Node)** – Centralized log collection and analysis system
* **Kali Linux (Attacker Node)** – Used to simulate offensive activity
* **Host-Only Virtual Network** – Isolated network segment for secure lab communication

The Ubuntu Server acts as the core SOC system where SIEM and IDS components will be installed. All simulated attack traffic generated from the attacker machine will traverse the isolated virtual network and be monitored by the SOC node.

<img src="screenshots/ubuntu/Architecture_Flow.png" width="650">

This architecture ensures:

* Safe experimentation without affecting the external network
* Controlled attack simulation
* Clear separation of roles (attacker vs defender)
* Realistic detection workflow

## Network Configuration

All virtual machines in this SOC Home Lab are configured to use **NAT networking mode** within the hypervisor. This ensures that each VM resides within the same internal virtual subnet while still having internet access through the host machine.

For this lab, the internal subnet is configured as:

<img src="screenshots/ubuntu/nat_config.png" width="500">

This means:
* All VMs receive IP addresses within the range 192.168.1.1 – 192.168.1.254
* The subnet mask is 255.255.255.0
* The VMs can communicate directly with each other
* External internet access is routed through the host system

Using a shared subnet is critical for a SOC lab environment because:

* The attacker machine (Kali Linux) must be able to reach the target systems
* The target machines must forward logs to the SIEM server
* The SIEM components (Wazuh + Elastic Stack) must communicate internally without network segmentation issues

This configuration simulates a small enterprise internal LAN environment, making it ideal for attack simulation, log collection, and detection testing.

---

## Lab Environment Configuration

### Host Virtualization Platform

The SOC Home Lab is deployed using **VMware Workstation** as the hypervisor. VMware provides stable networking controls and resource allocation, making it suitable for simulating a small enterprise lab environment.

### Operating System

The primary server for this lab runs:

**Ubuntu 22.04.5 LTS Server**

Ubuntu Server was selected due to:

* Long-Term Support (LTS) stability
* Wide compatibility with security tools
* Lightweight footprint suitable for SIEM deployments
* Strong community and enterprise adoption

---

### Virtual Machine Specifications

The Ubuntu Server VM is configured with the following resources:

* Memory: 4 GB RAM
* CPU: 2 cores
* Storage: 40 GB disk
* Network Adapter: VMnet8 (NAT mode)

<img src="screenshots/ubuntu/vm_settings.png" width="600">

### Disk Allocation (While Server Installation)

Confirmation of the allocated disk space during Ubuntu Server installation.

<img src="screenshots/ubuntu/storage_config.png" width="700">

### SSH Installation Verification (While Server Installation)

OpenSSH Server was enabled during installation to allow secure remote access to the Ubuntu Server VM.

<img src="screenshots/ubuntu/ssh_config.png" width="700">

### System Verification (After Proper Installation)

This screenshot verifies the Ubuntu version, hostname, and IP configuration, confirming the server is correctly installed and connected to the NAT subnet.

<img src="screenshots/ubuntu/system_check.png" width="800">

---





