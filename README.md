# SOC Home Lab
*Infrastructure Deployment | Log Pipeline Validation | Wazuh SIEM*

[![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04-orange?style=for-the-badge&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Kali Linux](https://img.shields.io/badge/Kali_Linux-2023.3-blue?style=for-the-badge&logo=kali-linux&logoColor=white)](https://www.kali.org/)
[![Wazuh](https://img.shields.io/badge/Wazuh-SIEM-0056D2?style=for-the-badge&logo=wazuh&logoColor=white)](https://wazuh.com/)
[![VMware](https://img.shields.io/badge/VMware-Workstation-607078?style=for-the-badge&logo=vmware&logoColor=white)](https://www.vmware.com/)
[![Security](https://img.shields.io/badge/SOC-Lab-F535AA?style=for-the-badge&logo=security&logoColor=white)](https://www.cisecurity.org/)
[![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-FF6600?style=for-the-badge&logo=mitre&logoColor=white)](https://attack.mitre.org/)

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Lab Objectives](#2-lab-objectives)
3. [Lab Architecture](#3-lab-architecture)
4. [Network Configuration](#4-network-configuration)
5. [Lab Environment Configuration](#5-lab-environment-configuration)
6. [Server Hardening & Initial Setup](#6-server-hardening--initial-setup)
7. [Wazuh SIEM Installation & Configuration](#7-wazuh-siem-installation--configuration)
8. [Agent Deployment & Log Onboarding](#8-agent-deployment--log-onboarding)
9. [Attack Simulation & Detection Validation](#9-attack-simulation--detection-validation)
10. [Lessons Learned, Limitations & Future Improvements](#10-lessons-learned-limitations--future-improvements)
11. [Final Project Conclusion](#final-project-conclusion)

---

## 1. Project Overview

This repository documents the design and implementation of a foundational **Security Operations Center (SOC) home lab**, focused on infrastructure deployment and log pipeline validation.

The primary objective is to build and configure a **fully functional SIEM environment** using **Wazuh** within an isolated virtualized network. The lab simulates a basic SOC monitoring setup where endpoint logs are:

* Collected
* Processed
* Indexed
* Visualized in a centralized dashboard

The environment is intentionally designed to operate under limited hardware resources (8 GB host RAM), requiring careful VM sizing and service optimization for stable performance.

Through this project, the following capabilities are demonstrated:

* Deployment of a centralized Ubuntu-based SOC server
* Installation and configuration of the Wazuh all-in-one SIEM stack
* Secure onboarding of a monitored endpoint
* Validation of log ingestion and real-time alert visibility
* Firewall configuration and connectivity troubleshooting

This project forms the **infrastructure foundation** of a larger SOC lab series and establishes a fully operational monitoring pipeline for future detection and investigation exercises.

---

## 2. Lab Objectives

This project focuses on building the **foundational infrastructure** of a functional **Security Operations Center (SOC)** within a controlled virtual environment.

The primary objectives of this lab are to:

* Deploy a centralized **Ubuntu Server** to act as the SOC node
* Install and configure a **SIEM platform using Wazuh**
* Configure secure remote access and implement basic server hardening
* Establish a controlled **NAT-based virtual network** for internal communication
* Onboard a monitored endpoint (**Kali Linux**) using the Wazuh agent
* Validate successful log ingestion from endpoint to SIEM
* Confirm alert generation and dashboard visibility
* Troubleshoot firewall and connectivity issues

The goal of this phase is to establish a **stable monitoring foundation** that accurately simulates log collection and centralized visibility within a SOC environment.

> ⚠️ **Note:** This project intentionally focuses on **infrastructure deployment and log pipeline validation**. Advanced detection engineering, attack simulation, and incident investigation will be addressed in subsequent SOC lab projects.
---
## 3. Lab Architecture

The SOC lab is deployed within a **virtualized environment** to simulate a simplified internal enterprise network. The architecture separates the **monitoring server** and the **endpoint**, reflecting a realistic centralized logging model.

### Lab Components

| Component                         | Role in the Lab                                                           |
| --------------------------------- | ------------------------------------------------------------------------- |
| Host Machine (VMware Workstation) | Runs and manages all virtual machines                                     |
| Ubuntu Server (SOC Node)          | Centralized SIEM server for log collection, processing, and visualization |
| Kali Linux (Monitored Endpoint)   | Generates system and security events forwarded to the SIEM                |
| NAT Virtual Network               | Provides isolated internal subnet with internet access                    |
| Wazuh (All-in-One Deployment)     | Log collection, indexing (OpenSearch), detection engine, and dashboard    |

---

### Architectural Flow

The **Ubuntu Server** acts as the centralized SOC node where the **Wazuh Manager**, **Indexer**, and **Dashboard** are deployed in **all-in-one mode** to conserve system resources.

The **Kali Linux VM** functions as a monitored endpoint. Logs generated on this system are collected by the **Wazuh agent** and securely forwarded to the manager.

<img src="screenshots/ubuntu/Architecture_Flow.png" width="800">

---

### Architectural Design Considerations

This architecture was intentionally designed to:

* Operate within limited hardware resources (4GB SOC VM RAM)
* Maintain isolation from the external network
* Simulate centralized log aggregation
* Support realistic endpoint onboarding workflows
* Provide clear visibility into log ingestion and alert generation

This design establishes the **foundational monitoring layer** of the SOC lab. Detection engineering, attack simulation, and multi-endpoint expansion will be implemented in future phases of the project series.

---

#### 4. Network Configuration

All virtual machines in this SOC lab are configured using **NAT networking mode** within VMware Workstation. This ensures that each VM resides within the same internal virtual subnet while maintaining outbound internet access through the host machine.

### Subnet Configuration

The internal subnet for this lab is:

* **Network Range:** `192.168.1.0/24`
* **Subnet Mask:** `255.255.255.0`
* **Default Gateway:** Provided by VMware NAT service
* **Internet Access:** Routed through host system

<img src="screenshots/ubuntu/nat_config.png" width="600">

---

### Why NAT Networking Was Chosen

NAT networking provides:

* Isolated internal communication between VMs
* Safe attack simulation within a controlled subnet
* Internet access for package installation and updates
* Separation from the physical LAN

This ensures that:

* The Kali endpoint can communicate with the Ubuntu SOC server
* The Wazuh agent can forward logs to the manager
* The dashboard remains accessible via the SOC server’s internal IP

---

### Internal Communication Requirements

For proper SOC operation, the following communication paths must function:

* **Kali → Ubuntu SOC Server:** Agent communication
* **Ubuntu Server → Internal indexing services**
* **Host Browser → Wazuh Dashboard:** HTTPS (443)

Firewall rules were configured accordingly to allow:

* **SSH access:** Port 22
* **Agent communication:** Ports 1514, 1515
* **Dashboard access:** Port 443

---

## 5. Lab Environment Configuration

This section outlines the virtualization platform, operating system selection, and virtual machine resource allocation used to build the SOC infrastructure.

---

### 5.1 Host Virtualization Platform

The SOC lab is deployed using **VMware Workstation** as the hypervisor.

VMware was selected because it provides:

* Stable NAT networking controls
* Flexible resource allocation
* Reliable VM performance under limited hardware
* Snapshot capability for safe testing and rollback

This setup allows safe experimentation while maintaining isolation from the physical host network.

---

### 5.2 Operating System Selection

The primary SOC server runs:

**Ubuntu Server 22.04 LTS**

Ubuntu Server was chosen due to:

* Long-Term Support (LTS) stability
* Lightweight resource footprint
* Strong compatibility with security tooling
* Wide adoption in enterprise and cloud environments

Using an LTS release ensures long-term package stability and security updates.

---

### 5.3 Ubuntu SOC Server Specifications

The Ubuntu Server VM was configured to balance performance and hardware constraints:

| Resource | Allocation   |
| -------- | ------------ |
| RAM      | 4 GB         |
| CPU      | 2 Cores      |
| Disk     | 40 GB        |
| Network  | VMnet8 (NAT) |

<img src="screenshots/ubuntu/vm_settings.png" width="700">

This allocation was intentionally chosen to:

* Support the Wazuh all-in-one deployment
* Maintain responsiveness within an 8GB host limit
* Allow indexing and dashboard services to operate reliably

---

### 5.4 Disk Allocation During Installation

Disk configuration was validated during Ubuntu installation to ensure the full 40GB allocation was recognized.

<img src="screenshots/ubuntu/storage_config.png" width="700">

Proper disk allocation is critical for SIEM deployments, as indexing and log storage can consume significant space over time.

---

### 5.5 SSH Configuration Verification

OpenSSH Server was enabled during installation to allow secure remote access to the SOC node.

<img src="screenshots/ubuntu/ssh_config.png" width="700">

SSH access enables:

* Remote administration
* Secure command execution
* Easier management from the host system

---

### 5.6 System Verification After Installation

Post-installation checks confirmed:

* Correct Ubuntu version
* Proper hostname configuration
* Valid IP assignment within the NAT subnet

<img src="screenshots/ubuntu/system_check.png" width="800">

This verification ensures the server is fully operational before proceeding to hardening and SIEM installation.

---

## 6. Server Hardening & Initial Setup

Before deploying the SIEM components, the Ubuntu SOC server was updated, cleaned, and minimally hardened to ensure system stability and security readiness.

---

### 6.1 System Update and Package Upgrade

The package index was refreshed and all installed packages were upgraded to their latest stable versions:

```bash
sudo apt update
sudo apt upgrade -y
````

This ensures:

* Latest security patches are applied
* Dependency consistency
* A stable baseline before Wazuh installation

**Evidence of successful update and upgrade:**

<img src="/screenshots/ubuntu/update_complete.png" width="800">

---

### 6.2 Removal of Unnecessary Snap Packages

To reduce system overhead and minimize the attack surface, unnecessary snap packages were reviewed and removed where applicable.

Benefits:

* Reduced background services
* Improved resource efficiency
* Maintained a lightweight server profile

<img src="/screenshots/ubuntu/usless_snaps.png" width="800">

---

### 6.3 Firewall Baseline Configuration (UFW)

Ubuntu’s Uncomplicated Firewall (UFW) was enabled to restrict inbound traffic by default. Initially, only SSH access was allowed for secure remote administration:

```bash
sudo ufw allow OpenSSH
sudo ufw enable
```

Verification confirms UFW is active with minimal exposed services:

<img src="/screenshots/ubuntu/ufw_status.png" width="800">

> At this stage, only essential administrative access is permitted. Additional ports required for Wazuh services will be configured later during SIEM deployment (Section 7/8).

---

### 6.4 SSH Service Verification

The SSH service was verified to ensure remote administrative access is functional:

```bash
sudo systemctl status ssh
```

Verification output confirming active service:

<img src="/screenshots/ubuntu/ssh_status.png" width="800">

SSH access provides secure remote management and operational flexibility.

---

### 6.5 Network Configuration Verification

The server’s IP configuration was validated to confirm proper NAT subnet assignment and active network interface:

```bash
ip a
```

This confirms:

* Active network interface
* Proper NAT IP assignment (192.168.x.x)
* Valid routing configuration

<img src="/screenshots/ubuntu/system_check.png" width="800">

---

### 6.6 Resource Validation

System memory and disk availability were verified before proceeding with SIEM deployment:

```bash
free -h
df -h
```

This confirms:

* Allocated RAM availability
* Disk allocation integrity
* Readiness for indexing and log storage

<img src="/screenshots/ubuntu/resource_check.png" width="800">

---

### Hardening Summary

The Ubuntu SOC server was prepared through:

* Full system update and upgrade
* Removal of unnecessary snap packages
* Firewall configuration and enforcement
* SSH service verification
* Network validation
* Resource verification

These steps establish a secure and stable baseline prior to Wazuh installation.

---

## 7. Wazuh SIEM Installation & Configuration

This section documents the deployment of the **Wazuh all-in-one SIEM stack** on the Ubuntu SOC server.

The installation includes:

* Wazuh Manager
* Wazuh Indexer (OpenSearch)
* Filebeat
* Wazuh Dashboard

The **all-in-one deployment model** was chosen to optimize performance within the lab’s 4GB RAM allocation while maintaining full SIEM functionality.

---

### 7.1 Installation Script Download

The official Wazuh installation script was downloaded from the Wazuh repository:

```bash
curl -O https://packages.wazuh.com/4.14/wazuh-install.sh
````

This script automates the installation and configuration of all required Wazuh components.

<img src="/screenshots/wazuh/wazuh_script.png" width="800">

---

### 7.2 Executing the All-in-One Installation

The script was made executable and run in all-in-one mode:

```bash
chmod +x wazuh-install.sh
sudo ./wazuh-install.sh -a
```

Installation performed:

* System dependency installation
* Wazuh Manager deployment
* OpenSearch (Indexer) installation and configuration
* Filebeat installation
* Wazuh Dashboard configuration and SSL setup

**Installation progress output:**

<img src="/screenshots/wazuh/wazuh_install_start.png" width="800">

---

### 7.3 Dashboard Credential Generation

Upon completion, default credentials for the Wazuh Dashboard were generated. These are required for HTTPS login:

<img src="/screenshots/wazuh/wazuh_dashboard_credentials.png" width="800">

> Credentials were securely stored for later use.

---

### 7.4 Service Verification

All Wazuh services were verified to ensure proper startup:

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-dashboard
sudo systemctl status wazuh-indexer
sudo systemctl status filebeat
```

Expected output:

```
active (running)
```

**Service verification screenshot:**

<img src="/screenshots/wazuh/wazuh_services_running.png" width="800">

This confirms:

* Wazuh Manager is processing events
* Indexer (OpenSearch) is operational
* Dashboard is accessible
* Filebeat is actively forwarding logs

---

### 7.5 Firewall Rule Expansion for Wazuh Services

Initially, only SSH was allowed. HTTPS (Port 443) was explicitly permitted to allow dashboard access:

```bash
sudo ufw allow 443/tcp
```

**Updated firewall status:**

<img src="/screenshots/wazuh/wazuh_port443_open.png" width="800">

> This approach follows a **security-first deployment model**, exposing services only when required.

---

### 7.6 Dashboard Access & Verification

The Wazuh Dashboard was accessed from the host machine using the server’s NAT IP:

```
https://192.168.1.136
```

Since a self-signed SSL certificate was used, a browser warning appeared and was bypassed for lab purposes.

**Login page screenshot:**

<img src="/screenshots/wazuh/wazuh_dashboard_login.png" width="800">

**Dashboard home interface screenshot:**

<img src="/screenshots/wazuh/wazuh_dashboard_home.png" width="800">

Successful login confirms:

* Dashboard connectivity to Wazuh Manager
* OpenSearch indexing functionality
* Full SIEM operational status

---

### 7.7 Deployment Summary

The Wazuh SIEM stack is now fully deployed and operational. The Ubuntu SOC server functions as:

* Centralized log collection server
* Detection and correlation engine
* Alerting platform
* Security visualization dashboard

The environment is now ready for:

* Endpoint agent deployment
* Log ingestion testing
* Attack simulation and detection validation

---


## 8. Agent Deployment & Log Onboarding

With the Wazuh SIEM stack operational on the Ubuntu SOC node, the next phase involves onboarding a monitored endpoint.

A Kali Linux virtual machine was configured as a client endpoint and enrolled into the Wazuh monitoring infrastructure. This mirrors real-world SOC workflows where endpoints must be securely registered before telemetry ingestion begins.

---

## 8.1 Kali Linux VM Configuration

The Kali Linux VM was provisioned within VMware using NAT networking to reside within the same internal subnet as the SOC server.

| Resource | Allocation   |
| -------- | ------------ |
| RAM      | 2 GB         |
| CPU      | 2 Cores      |
| Disk     | 30 GB        |
| Network  | VMnet8 (NAT) |

<img src="/screenshots/agent/kali_vm_hardware_config.png" width="800">

This configuration balances endpoint realism with host resource constraints (8GB total system RAM).

---

## 8.2 Network Connectivity Verification

Before agent installation, internal connectivity was validated.

### Verify Kali IP Address

```bash
ip a
```

<img src="/screenshots/agent/kali_ip_address.png" width="800">

### Test Connectivity to Wazuh Manager

```bash
ping 192.168.1.136
```

<img src="/screenshots/agent/kali_ping_manager_success.png" width="800">

Successful ICMP responses confirm:

* Both systems reside in the same NAT subnet
* Internal communication is functional
* No firewall blocking at the network layer

---

## 8.3 Installing the Wazuh Agent

### Add Wazuh GPG Key

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg
```

<img src="/screenshots/agent/wazuh_gpg_key_added.png" width="800">

---

### Add Wazuh Repository

```bash
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
```

<img src="/screenshots/agent/wazuh_repo_added.png" width="800">

---

### Update and Install Agent

```bash
sudo apt update
sudo WAZUH_MANAGER='192.168.1.136' apt install wazuh-agent -y
```

<img src="/screenshots/agent/apt_update_wazuh_repo.png" width="800">
<img src="/screenshots/agent/wazuh_agent_installation.png" width="800">

---

### Enable and Start Agent Service

```bash
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
sudo systemctl status wazuh-agent
```

<img src="/screenshots/agent/wazuh_agent_service_running.png" width="800">

Service status confirmed the agent was active and attempting to communicate with the manager.

---

## 8.4 Secure Agent Registration (Key-Based Authentication)

Wazuh uses key-based authentication between agents and the manager.

### Add Agent on Manager

On the Ubuntu SOC server:

```bash
sudo /var/ossec/bin/manage_agents
```

<img src="/screenshots/agent/manage_agents_add_kali.png" width="800">

An agent entry was created for `kali-linux`.

---

### Extract and Import Agent Key

The generated key was extracted:

<img src="/screenshots/agent/kali_import_agent_key.png" width="800">

The key was then imported on the Kali machine using the same `manage_agents` utility.

After importing the key, the agent service was restarted.

---

### Verify Agent Registration on Manager

```bash
sudo /var/ossec/bin/agent_control -l
```

<img src="/screenshots/agent/agent_control_list_output.png" width="800">

The output confirms:

* Agent ID assigned
* Status: Active
* Secure communication established

---

## 8.5 Firewall Validation for Agent Communication

If agent connectivity issues occur, ensure required ports are allowed on the manager:

```bash
sudo ufw allow 1514
sudo ufw allow 1515
```

<img src="/screenshots/agent/if_server_firewall_blocks_port_1514.png" width="800">

Ports 1514 and 1515 are required for:

* Agent data transmission
* Secure registration

This aligns with the firewall configuration strategy defined in earlier sections.

---

## 8.6 Dashboard Agent Verification

The Wazuh Dashboard was accessed via:

```
https://192.168.1.136
```

Under **Wazuh → Agents**, the endpoint `kali-linux` appeared as:

* Status: Active
* Operating System: Detected correctly
* Keepalive signals: Received

<img src="/screenshots/agent/wazuh_dashboard_agent_active.png" width="800">

This confirms successful onboarding.

---

## 8.7 Log Generation & Event Validation

To validate real-time log ingestion, controlled events were generated on the Kali endpoint.

---

### Authentication Attempts

```bash
sudo su -
sudo su invaliduser
```

<img src="/screenshots/agent/kali_auth_log_generation.png" width="800">

These actions generate authentication-related logs.

---

### Package Installation Activity

```bash
sudo apt install sl -y
```

<img src="/screenshots/agent/kali_package_install_log.png" width="800">

This generates system and package management logs.

---

### Event Visibility in Dashboard

Events were filtered in the Wazuh Dashboard under Security Events for `kali-linux`.

<img src="/screenshots/agent/wazuh_event_validation_kali1.png" width="800">
<img src="/screenshots/agent/wazuh_event_validation_kali2.png" width="800">

The dashboard confirmed:

* Log ingestion
* Rule matching
* Alert generation
* Indexed event storage

---

## 8.8 Deployment Summary

The Kali Linux endpoint is now:

* Installed with Wazuh Agent
* Securely registered with the SOC Manager
* Actively sending logs
* Generating detectable events

The SOC lab environment is now fully operational and ready for:

* Attack simulation
* Detection engineering
* Alert tuning
* Incident response exercises

---

## 9. Attack Simulation & Detection Validation

To validate the effectiveness of the deployed SIEM infrastructure, controlled attack simulations were conducted from the Kali Linux endpoint.

The objective of this phase was to:

* Confirm log ingestion from the agent
* Validate rule-based detection
* Verify alert severity classification
* Demonstrate end-to-end SOC visibility

Each attack simulation includes:

1. Attack execution on endpoint
2. Log generation
3. Detection by Wazuh
4. Alert visibility in dashboard

---

## 9. Attack Simulation & Detection Validation

This section demonstrates the SOC lab's detection capabilities through controlled attack simulations on the Kali endpoint. Each scenario validates Wazuh’s monitoring, alerting, and telemetry pipeline.

---

### 9.1 Authentication Abuse Simulation

**Objective:** Simulate repeated failed authentication attempts to validate detection of brute-force behavior.

#### Attack Execution (Kali Endpoint)

```bash
sudo su invaliduser
sudo su invaliduser
sudo su invaliduser
````

📸 Evidence — Attack Execution

<img src="/screenshots/attacks/kali/auth_failed_generation.png" width="800">

#### Detection in Wazuh Dashboard

Alerts for authentication failures were generated for the `kali-linux` agent.

📸 Evidence — Detection Alert

<img src="/screenshots/attacks/dashboard/auth_failed_alert.png" width="800">

**Validation:**

* Correct agent identification
* Rule ID triggered
* Severity level assigned
* Accurate timestamp correlation

This confirms functional authentication monitoring.

---

### 9.2 Privilege Escalation Activity

**Objective:** Validate detection of privileged command execution.

#### Attack Execution (Kali Endpoint)

```bash
sudo -l
sudo su -
exit
```

📸 Evidence — Privilege Escalation Execution

<img src="/screenshots/attacks/kali/privilege_escalation_generation.png" width="800">

#### Detection in Dashboard

Alerts were triggered for privileged command usage and root session activity.

📸 Evidence — Privilege Escalation Alert

<img src="/screenshots/attacks/dashboard/privilege_escalation_alert.png" width="800">

**Validation:**

* Root access activity logged
* Event severity assigned
* MITRE ATT&CK technique mapping applied

This demonstrates monitoring of privilege escalation attempts.

---

### 9.3 Suspicious Tool Installation

**Objective:** Simulate adversarial reconnaissance preparation by installing scanning tools.

#### Attack Execution (Kali Endpoint)

```bash
sudo apt install nmap -y
```

📸 Evidence — Tool Installation

<img src="/screenshots/attacks/kali/nmap_install_generation.png" width="800">

#### Detection in Dashboard

Package installation activity was successfully logged and indexed by Wazuh.

📸 Evidence — Installation Alert

<img src="/screenshots/attacks/dashboard/tool_installation_alert.png" width="800">

**Validation:**

* System activity logging is functional
* Package management events monitored
* Endpoint telemetry properly forwarded

---

### 9.4 File Integrity Monitoring (FIM) Validation

**Objective:** Verify real-time detection of critical file modifications.

#### Attack Execution (Kali Endpoint)

The `/etc/hosts` file was modified to simulate unauthorized changes.

📸 Evidence — File Modification

<img src="/screenshots/attacks/kali/file_modification_generation.png" width="800">

#### Detection in Dashboard

Wazuh FIM detected the modification event.

📸 Evidence — FIM Alert

<img src="/screenshots/attacks/dashboard/file_integrity_alert.png" width="800">

**Validation:**

* Exact file path monitored
* Change detection triggered
* Timestamp correlation
* Agent attribution

Demonstrates host-based intrusion detection capabilities.

---

### 9.5 MITRE ATT&CK Mapping Validation

Wazuh rule metadata includes MITRE ATT&CK technique mapping for enhanced threat classification.

📸 Evidence — MITRE Mapping

<img src="/screenshots/attacks/dashboard/mitre_mapping_example.png" width="800">

**Validation:**

* Detection aligned with standardized threat frameworks
* Enhanced analytical context for SOC investigations

---

### 9.6 Detection Validation Summary

The attack simulations confirm:

* Endpoint log forwarding is functional
* Rule-based detection is active
* Alert severity classification works correctly
* File integrity monitoring is operational
* Privilege escalation monitoring is effective
* Authentication abuse is detectable
* MITRE framework mapping is integrated

The SOC lab is now fully validated for simulated attack detection and monitoring readiness.

---

## 10. Lessons Learned, Limitations & Future Improvements

This section reflects on key takeaways, limitations of the current lab setup, and potential future enhancements to improve SOC capabilities.

---

### 10.1 Importance of Controlled Network Architecture

The use of NAT networking provided a safe internal environment for attack simulation while maintaining internet access for updates and package installation.

**Key takeaways:**

* Isolated testing environments improve security and reliability  
* Controlled service exposure reduces unnecessary risk  
* Incremental firewall rule expansion ensures safety and monitoring integrity  

Proper network segmentation is foundational to effective SOC design.

---

### 10.2 Resource Constraints in SIEM Deployments

Deploying the Wazuh all-in-one stack within a 4GB RAM VM highlighted:

* Indexing services are resource-intensive  
* Memory planning is critical for stable operations  
* Log volume directly impacts performance  

**Lessons learned:**

* Capacity planning is essential  
* Log retention strategy affects storage and indexing  
* Performance monitoring is required for long-term stability  

Enterprise SIEM deployments typically use distributed architectures to handle scale and high log volumes.

---

### 10.3 Detection Validation Is Critical

Installing a SIEM does not automatically guarantee detection capability.

**Observations from attack simulations:**

* Rules must be validated for correctness  
* Alert severities need review and tuning  
* Log visibility must be confirmed  

This exercise emphasizes the difference between **infrastructure deployment** and **operational detection capability**.

---

### 10.4 Understanding MITRE ATT&CK Mapping

Reviewing alert mappings to MITRE ATT&CK techniques improves:

* Threat categorization  
* Understanding of tactics and techniques  
* Context for standardized threat intelligence  

This enhances investigation clarity and reporting quality.

---

## 10.5 Limitations of the Lab Environment

Despite successful deployment, several limitations were noted:

* **Single Endpoint:** Only one Linux endpoint (Kali) was integrated. Real SOCs typically monitor Windows systems, Linux servers, network devices, and cloud workloads.  
* **No Log Volume Stress Testing:** The lab did not simulate high log ingestion rates, distributed agent scaling, or long-term retention impact.  
* **No EDR-Level Telemetry:** Kernel-level telemetry, advanced behavioral analytics, and memory-based threat detection were not included.  
* **Self-Signed Certificate Usage:** The dashboard used a self-signed SSL certificate, which is not production-ready.

---

## 10.6 Future Improvements

Enhancements to expand SOC capability include:

* **Add Windows Endpoint with Sysmon:** Integrate a Windows 10/11 VM with Wazuh agent and Sysmon for process creation, registry changes, and persistence detection.  
* **Integrate Intrusion Detection System (IDS):** Deploy a network IDS like Suricata to monitor network traffic, perform signature-based detection, and inspect layer 7 traffic.  
* **Centralized Log Enrichment:** Incorporate threat intelligence feeds for IP reputation checks, domain blacklist correlation, and GeoIP enrichment.  
* **Implement Alert Tuning:** Reduce false positives, adjust severity thresholds, and create custom detection rules.  
* **Distributed Wazuh Architecture:** Separate the all-in-one deployment into Manager, Indexer, and Dashboard nodes for better performance, fault tolerance, and scalability.

---

## 10.7 Final Project Conclusion

This project successfully demonstrated the design, deployment, and operational validation of a functional SOC lab using Wazuh SIEM.  

**Key capabilities now validated:**

* Endpoint onboarding and secure registration  
* Log ingestion and indexing  
* Real-time alerting and monitoring  
* File integrity monitoring  
* Privilege monitoring  
* Authentication abuse detection  
* MITRE ATT&CK-aligned alerts  

Controlled attack simulations confirmed detection mechanisms function as intended.  

The environment provides a solid foundation for advanced detection engineering, blue-team exercises, and future SOC expansion.

---
