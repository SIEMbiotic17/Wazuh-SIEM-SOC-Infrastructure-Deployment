# SOC Home Lab

## Table of Contents
1. [Project Overview](#1-project-overview)
2. [Lab Objectives](#2-lab-objectives)
3. [Lab Architecture](#3-lab-architecture)
4. [Network Configuration](#4-network-configuration)
5. [Lab Environment Configuration](#5-lab-environment-configuration)
6. [Server Hardening & Initial Setup](#6-server-hardening--initial-setup)
7. [Wazuh SIEM Installation & Configuration](#7-wazuh-siem-installation--configuration)
8. [Agent Deployment & Log Onboarding](#8-agent-deployment--log-onboarding)


---

## 1. Project Overview

This repository documents the design and implementation of a foundational Security Operations Center (SOC) home lab focused on infrastructure deployment and log pipeline validation.

The primary objective of this project is to build and configure a functioning SIEM environment using Wazuh within an isolated virtualized network. The lab simulates a basic SOC monitoring setup where endpoint logs are collected, processed, indexed, and visualized in a centralized dashboard.

The environment is intentionally designed to operate under limited hardware resources (8GB host RAM), requiring careful virtual machine sizing and service optimization to ensure stable performance.

Through this project, I demonstrate:

* Deployment of a centralized Ubuntu-based SOC server
* Installation and configuration of the Wazuh all-in-one SIEM stack
* Secure onboarding of a monitored endpoint
* Validation of log ingestion and real-time alert visibility
* Firewall configuration and connectivity troubleshooting

This project represents the infrastructure foundation of a larger SOC lab series and establishes a fully operational monitoring pipeline ready for future detection and investigation projects.

---

## 2. Lab Objectives

This project focuses on building the foundational infrastructure of a functional Security Operations Center (SOC) within a controlled virtual environment.

The primary objectives of this lab are:

* Deploy a centralized Ubuntu Server to function as the SOC node
* Install and configure a SIEM platform using Wazuh
* Configure secure remote access and basic server hardening
* Establish a controlled NAT-based virtual network for internal communication
* Onboard a monitored endpoint (Kali Linux) using the Wazuh agent
* Validate successful log ingestion from endpoint to SIEM
* Confirm alert generation and dashboard visibility
* Troubleshoot firewall and connectivity issues

The goal of this phase is to establish a stable monitoring foundation that accurately simulates log collection and centralized visibility within a SOC environment.

This project intentionally focuses on infrastructure deployment and log pipeline validation. Advanced detection engineering, attack simulation, and incident investigation will be addressed in subsequent projects within this SOC lab series.

---
## 3. Lab Architecture

The SOC lab is deployed within a virtualized environment to simulate a simplified internal enterprise network. The architecture separates the monitoring server and the endpoint to reflect a realistic centralized logging model.

The environment consists of the following components:

| Component                         | Role in the Lab                                                           |
| --------------------------------- | ------------------------------------------------------------------------- |
| Host Machine (VMware Workstation) | Runs and manages all virtual machines                                     |
| Ubuntu Server (SOC Node)          | Centralized SIEM server for log collection, processing, and visualization |
| Kali Linux (Monitored Endpoint)   | Generates system and security events forwarded to the SIEM                |
| NAT Virtual Network               | Provides isolated internal subnet with internet access                    |
| Wazuh (All-in-One Deployment)     | Log collection, indexing (OpenSearch), detection engine, and dashboard    |

---

### Architectural Flow

The Ubuntu Server acts as the centralized SOC node where the Wazuh Manager, Indexer, and Dashboard are deployed in all-in-one mode to conserve system resources.

The Kali Linux VM operates as a monitored endpoint. Logs generated on this system are collected by the Wazuh agent and securely forwarded to the manager.

```
Kali Linux (Endpoint)
        ↓
Wazuh Agent
        ↓
Wazuh Manager (Ubuntu SOC Node)
        ↓
Indexer (OpenSearch)
        ↓
Wazuh Dashboard
```

---

### Architectural Design Considerations

This architecture was intentionally designed to:

* Operate within limited hardware resources (4GB SOC VM RAM)
* Maintain isolation from the external network
* Simulate centralized log aggregation
* Allow realistic endpoint onboarding workflows
* Provide clear visibility into log ingestion and alert generation

The current design establishes the foundational monitoring layer of the SOC lab. Detection engineering, attack simulation, and multi-endpoint expansion will be implemented in future phases of the project series.

---

## 4. Network Configuration

All virtual machines in this SOC lab are configured using **NAT networking mode** within VMware Workstation. This ensures that each VM resides within the same internal virtual subnet while maintaining outbound internet access through the host machine.

The internal subnet for this lab is configured as:

* Network Range: `192.168.1.0/24`
* Subnet Mask: `255.255.255.0`
* Default Gateway: Provided by VMware NAT service
* Internet Access: Routed through host system

<img src="screenshots/ubuntu/nat_config.png" width="600">

---

### Why NAT Networking Was Chosen

NAT networking provides:

* Isolated internal communication between VMs
* Safe attack simulation within a controlled subnet
* Internet access for package installation and updates
* Separation from the physical LAN

This configuration ensures:

* The Kali endpoint can communicate with the Ubuntu SOC server
* The Wazuh agent can forward logs to the manager
* The dashboard remains accessible via the SOC server’s internal IP

---

### Internal Communication Requirements

For proper SOC operation, the following communication paths must function:

* Kali → Ubuntu SOC Server (Agent communication)
* Ubuntu Server → Internal indexing services
* Host Browser → Wazuh Dashboard (HTTPS 443)

Firewall rules were configured accordingly to allow:

* SSH access (port 22)
* Agent communication (1514, 1515)
* Dashboard access (443)

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

This allows safe experimentation while maintaining isolation from the physical host network.

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

The Ubuntu Server VM was configured to balance performance and hardware constraints.

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

This verification step ensures the server is fully operational before proceeding to hardening and SIEM installation.

---

## 6. Server Hardening & Initial Setup

Before deploying the SIEM components, the Ubuntu SOC server was updated, cleaned, and minimally hardened to ensure system stability and security readiness.

---

## 6.1 System Update and Package Upgrade

The package index was refreshed and all installed packages were upgraded to their latest stable versions.

```bash
sudo apt update
sudo apt upgrade -y
```

This step ensures:

* Latest security patches are applied
* Dependency consistency
* Stable baseline before Wazuh installation

Evidence of successful update and upgrade completion is shown below:

<img src="/screenshots/ubuntu/update_complete.png" width="800">

---

## 6.2 Removal of Unnecessary Snap Packages

To reduce system overhead and maintain a minimal attack surface, unnecessary snap packages were reviewed and removed where applicable.

This helps:

* Reduce background services
* Improve resource efficiency
* Maintain a lightweight server profile

<img src="/screenshots/ubuntu/usless_snaps.png" width="800">

---

## 6.3 Firewall Baseline Configuration (UFW)

As part of initial server hardening, Ubuntu’s Uncomplicated Firewall (UFW) was enabled to restrict inbound traffic by default.

Initially, only SSH access was allowed to ensure secure remote administration.

```bash
sudo ufw allow OpenSSH
sudo ufw enable
```

Firewall status verification confirmed that UFW was active with minimal exposed services.

<img src="/screenshots/ubuntu/ufw_status.png" width="800">

At this stage, only essential administrative access was permitted.
Additional ports required for Wazuh services were configured later during SIEM deployment (Section 8).


---


## 6.4 SSH Service Verification

Secure Shell (SSH) service was verified to ensure remote administrative access is functional.

```bash
sudo systemctl status ssh
```

Verification output confirming active SSH service:

<img src="/screenshots/ubuntu/ssh_status.png" width="800">

SSH access enables secure remote system management and operational flexibility.

---

## 6.5 Network Configuration Verification

The server’s IP configuration was validated to ensure proper NAT subnet assignment and active network interface.

```bash
ip a
```

Verification confirms:

* Active network interface
* Proper NAT IP assignment (192.168.x.x)
* Valid routing configuration

<img src="/screenshots/ubuntu/system_check.png" width="800">

---

## 6.6 Resource Validation

System memory and disk availability were verified before proceeding with SIEM deployment.

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

# Hardening Summary

The Ubuntu SOC server was prepared through:

* Full system update and upgrade
* Removal of unnecessary snap packages
* Firewall configuration and enforcement
* SSH verification
* Network validation
* Resource verification

These steps establish a secure and stable baseline prior to Wazuh installation.

---

## 7. Wazuh SIEM Installation & Configuration

This section documents the deployment of the Wazuh all-in-one SIEM stack on the Ubuntu Server SOC node.

The installation includes:

* Wazuh Manager
* Wazuh Indexer (OpenSearch)
* Filebeat
* Wazuh Dashboard

The **all-in-one deployment model** was selected to optimize performance within the lab’s 4GB RAM allocation while maintaining full SIEM functionality.

---

## 7.1 Installation Script Download

The official Wazuh installation script was downloaded from the Wazuh package repository.

```bash
curl -O https://packages.wazuh.com/4.14/wazuh-install.sh
```

This script automates installation and configuration of all required Wazuh components.

<img src="/screenshots/wazuh/wazuh_script.png" width="800">

---

## 7.2 Executing the All-in-One Installation

The script was made executable and executed in all-in-one mode:

```bash
chmod +x wazuh-install.sh
sudo ./wazuh-install.sh -a
```

The installation process performed the following actions:

* Installed system dependencies
* Deployed Wazuh Manager
* Installed and configured OpenSearch (Indexer)
* Installed Filebeat
* Configured and secured the Wazuh Dashboard

Installation output during deployment:

<img src="/screenshots/wazuh/wazuh_install_start.png" width="800">

---

## 7.3 Dashboard Credential Generation

Upon successful completion, the installation script generated default dashboard credentials.

These credentials are required to authenticate to the Wazuh Dashboard via HTTPS.

<img src="/screenshots/wazuh/wazuh_dashboard_credentials.png" width="800">

The generated credentials were securely stored for later use.

---

## 7.4 Service Verification

After installation, all Wazuh-related services were verified to ensure proper startup and operational status.

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-dashboard
sudo systemctl status wazuh-indexer
sudo systemctl status filebeat
```

All services returned:

```
active (running)
```

Service verification output:

<img src="/screenshots/wazuh/wazuh_services_running.png" width="800">

This confirms:

* Manager is processing events
* Indexer is operational
* Dashboard is accessible
* Log shipping via Filebeat is active

---

## 7.5 Firewall Rule Expansion for Wazuh Services

During initial hardening (Section 6), only SSH access was allowed.

To enable secure dashboard access, HTTPS (Port 443) was explicitly permitted.

```bash
sudo ufw allow 443/tcp
```

Updated firewall configuration:

<img src="/screenshots/wazuh/wazuh_port443_open.png" width="800">

This controlled expansion demonstrates a security-first deployment model — services are exposed only when required.

---

## 7.6 Dashboard Access & Verification

The Wazuh Dashboard was accessed from the host machine using the server’s NAT IP address:

```
https://192.168.1.136
```

Since the installation uses a self-signed SSL certificate, the browser displayed a security warning, which was bypassed for lab purposes.

---

### Dashboard Login Page

<img src="/screenshots/wazuh/wazuh_dashboard_login.png" width="800">

---

### Dashboard Home Interface

<img src="/screenshots/wazuh/wazuh_dashboard_home.png" width="800">

Successful login confirmed:

* Dashboard connectivity to Wazuh Manager
* OpenSearch indexing functionality
* Full SIEM operational status

---

## 7.7 Deployment Summary

At this stage, the Wazuh SIEM stack is fully deployed and operational within the SOC lab environment.

The Ubuntu Server now functions as:

* Centralized log collection server
* Detection and correlation engine
* Alerting platform
* Security visualization dashboard

The infrastructure is now prepared for:

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

# 🔚 Project Completion & Validation

## Environment Validation Summary

The Mini SOC Lab environment has been successfully deployed and validated. The following components were confirmed operational:

* Wazuh Manager installed and running
* Wazuh Dashboard accessible via browser
* Kali Linux endpoint successfully onboarded as an agent
* Agent status showing as **Active** in the manager
* Log ingestion verified from endpoint to SIEM
* Security events visible in the dashboard
* Authentication logs successfully collected
* Firewall configuration validated to allow required communication ports

---

## Log Flow Verification

The following validations were performed to confirm proper log ingestion:

* Endpoint successfully connected to the Wazuh Manager
* Agent registration completed without errors
* Real-time logs visible in the dashboard
* Security alerts generated and displayed correctly
* Filtering and search functionality tested

This confirms successful:

* Log collection
* Log forwarding
* Event processing
* Alert generation
* Dashboard visualization

---

## Architecture Overview

```
Kali Linux (Agent)
        ↓
Wazuh Agent
        ↓
Wazuh Manager (Ubuntu SOC Server)
        ↓
Indexer / Event Processing
        ↓
Wazuh Dashboard (Visualization & Alerts)
```

The lab successfully simulates a basic SOC monitoring pipeline.

---

## Final Outcome

This project demonstrates the successful deployment of a functional SIEM-based monitoring environment using Wazuh.

The environment is fully operational and ready for advanced detection testing and incident analysis in future projects.

---

#  What I Learned

Through this Mini SOC Lab project, I developed practical, hands-on experience in:

* Understanding SIEM architecture and log flow
* Deploying and configuring a Wazuh Manager
* Onboarding and managing endpoint agents
* Troubleshooting connectivity and firewall issues
* Verifying log ingestion and alert generation
* Navigating and validating events within the Wazuh Dashboard

I gained a deeper understanding of how logs move from endpoint to SIEM, how alerts are triggered, and how a SOC validates monitoring effectiveness.

This project strengthened my understanding of real-world SOC infrastructure deployment and monitoring fundamentals.

---

#  Future Enhancements

The current lab establishes a working monitoring foundation. Future improvements will focus on expanding detection and response capabilities:

* Simulating attack scenarios (brute force, port scanning, privilege escalation)
* Creating custom detection rules
* Reducing false positives through rule tuning
* Mapping alerts to MITRE ATT&CK techniques
* Performing structured alert triage and investigation
* Expanding monitoring to additional endpoints
* Integrating log sources such as web servers and system audit logs

These enhancements will transform the environment from a monitoring setup into a fully functional detection and investigation lab.

---







