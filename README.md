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

## 3. Lab Architecture

The SOC lab is deployed using a virtualized environment to simulate a controlled internal network. The architecture separates monitoring, analysis, and attack simulation components to reflect a simplified real-world SOC structure.

The environment consists of:

* **Host Machine** – Primary system running VMware
* **Ubuntu Server (SOC Node)** – Centralized log collection and analysis system
* **Kali Linux (Attacker Node)** – Used to simulate offensive activity
* **Host-Only Virtual Network** – Isolated network segment for secure lab communication

The Ubuntu Server acts as the core SOC system where SIEM and IDS components will be installed. All simulated attack traffic generated from the attacker machine will traverse the isolated virtual network and be monitored by the SOC node.

![Architecture](screenshots/ubuntu/Architecture_Flow.png)

This architecture ensures:

* Safe experimentation without affecting the external network
* Controlled attack simulation
* Clear separation of roles (attacker vs defender)
* Realistic detection workflow


