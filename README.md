# Wazuh SIEM Home Lab: Threat Detection & Log Analysis

A hands-on Security Information and Event Management (SIEM) laboratory built on **Arch Linux** using **KVM/QEMU**. This project demonstrates the deployment of a centralized security manager (Ubuntu) and the integration of a Windows Server 2022 endpoint to monitor, detect, and visualize security threats.

## Architecture Overview

The lab environment was virtualized on an Arch Linux host using **Virt-Manager (libvirt)** to ensure high-performance hardware acceleration and isolated networking.

* **Host OS:** Arch Linux (Rolling Release)
* **SIEM Manager:** Ubuntu Server 22.04 LTS (Running Wazuh Manager)
* **Endpoint/Agent:** Windows Server 2022 (Standard Evaluation)
* **Hypervisor:** KVM/QEMU via Virtual Machine Manager
* **Network:** NAT-based Virtual Bridge (192.168.122.0/24)

---

## Implementation Steps

### 1. Environment Virtualization (Arch Host)
* **Provisioned Resources:** Allocated 4GB RAM/2 vCPUs for Ubuntu and 8GB RAM/4 vCPUs for Windows Server using `virt-manager`.
* **Networking:** Configured a virtual bridge to allow inter-VM communication while maintaining host-only isolation for security.
* **Performance Tuning:** Integrated VirtIO drivers and optimized disk I/O to ensure seamless data flow between the Manager and the Agent.

### 2. Wazuh Manager Deployment (Ubuntu)
* **Automated Install:** Executed the Wazuh central installation script to deploy the Indexer, Server, and Dashboard.
* **Service Hardening:** Configured `ufw` (Uncomplicated Firewall) to allow traffic only on specific ports required for agent communication:
    * **Port 1514 (TCP):** For log collection and agent-to-manager communication.
    * **Port 1515 (TCP):** For agent enrollment and authentication.
* **Verification:** Confirmed service health using `systemctl status wazuh-manager`.

### 3. Windows Endpoint Integration (Agent)
* **Silent Installation:** Deployed the Wazuh MSI agent using a headless PowerShell command to streamline enrollment.
* **Manager Linking:** Pointed the agent to the Ubuntu IP (`192.168.122.63`) and assigned the hostname `WINDOWS2022`.
* **Service Management:** Initialized the agent via `Start-Service Wazuh` to begin telemetry transmission.

### 4. Threat Detection & Security Auditing
* **Logon Failure Monitoring:** Enabled Windows Local Security Policies (`secpol.msc`) to audit "Account Logon" events.
* **Brute Force Simulation:** Triggered security alerts by simulating failed authentication attempts, resulting in real-time **Level 5/10** alerts on the Wazuh Dashboard.
* **Vulnerability Assessment:** Conducted a system-wide scan to identify CVEs and outdated software packages on the Windows Server.

---

## Key Results

* **System Integration:** Successfully connected a Windows Server endpoint to a Linux-based SIEM **as measured by** an "Active" status in the Wazuh Dashboard, **by doing** advanced network configuration and port forwarding in KVM.
* **Threat Visibility:** Improved security posture visibility **as measured by** the generation of real-time alerts (Level 5+), **by doing** manual auditing of the Windows Security Channel and Event Logs.
* **Issue Resolution:** Reduced system configuration downtime **as measured by** successful log ingestion, **by doing** troubleshooting of Linux file permissions (`sudo/sed`) and Windows firewall rules.

---

## Proof of Concept
*(Note: Replace these placeholders with your actual screenshots)*

1.  **Dashboard Overview:** [Screenshot of Agents tab showing Windows2022 Active]
2.  **Security Events:** [Screenshot of Logon Failure alerts]
3.  **Vulnerability Inventory:** [Screenshot of the CVE list for the Windows VM]

---

## How to Replicate
1.  Install `virt-manager` and `qemu` on Arch Linux.
2.  Spin up an Ubuntu VM and run the [Wazuh Installation Guide](https://documentation.wazuh.com).
3.  Deploy a Windows VM and install the Agent using PowerShell:
    ```powershell
    Invoke-WebRequest -Uri [https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.msi](https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.msi) -OutFile wazuh-agent.msi
    msiexec.exe /i wazuh-agent.msi /q WAZUH_MANAGER='YOUR_UBUNTU_IP' WAZUH_AGENT_NAME='WINDOWS2022'
    ```
4.  Restart the Wazuh service and monitor the dashboard at `https://YOUR_UBUNTU_IP`.
