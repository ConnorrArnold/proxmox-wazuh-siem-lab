# 🛡️ Proxmox Wazuh SIEM Lab

A personal on-premises SIEM homelab built on Proxmox VE, deploying Wazuh for centralized log collection, threat detection, and security monitoring. This repo documents the end-to-end process of provisioning virtual machines, deploying the Wazuh stack, recovering services, onboarding agents, and hardening systems to CIS benchmarks.

---

## 🗺️ Project Overview

| Module | Description | Status |
|---|---|---|
| Proxmox VE Setup | Install and configure Proxmox hypervisor on bare metal | ✅ Complete |
| Wazuh Server Deployment | Deploy Wazuh manager, indexer, and dashboard via OVA | ✅ Complete |
| Dashboard Recovery | Recover inaccessible Wazuh dashboard after deployment | ✅ Complete |
| Agent Deployment | Install and enroll Wazuh agents on Debian endpoints | ✅ Complete |
| Log Collection & Alerting | Configure log sources and validate real-time alerts | ✅ Complete |
| CIS Benchmark Hardening | Apply CIS Debian 13 hardening and track score improvement | ✅ Complete |

---

## ⚙️ Module Details

### 1. Proxmox VE Setup
- Downloaded Proxmox VE ISO and flashed to bootable USB
- Installed Proxmox on bare-metal hardware and completed initial configuration
- Accessed the Proxmox web UI and configured networking for VM connectivity
- Set up local storage for VM disk images and ISO uploads

### 2. Wazuh Server Deployment
- Downloaded the official Wazuh OVA appliance
- Imported the OVA into Proxmox as a virtual machine
- Configured VM networking to ensure connectivity within the lab environment
- Completed initial Wazuh setup and verified all services were running

```bash
# Verify Wazuh services are active
systemctl status wazuh-manager
systemctl status wazuh-indexer
systemctl status wazuh-dashboard
```

### 3. Dashboard Recovery
- Identified that the Wazuh dashboard was unreachable after initial OVA deployment
- Diagnosed misconfigured dashboard binding address and certificate issues
- Edited `/etc/wazuh-dashboard/opensearch_dashboards.yml` to correct server host settings
- Restarted the dashboard service and confirmed access via browser

```bash
# Edit dashboard configuration
nano /etc/wazuh-dashboard/opensearch_dashboards.yml

# Restart dashboard service
systemctl restart wazuh-dashboard
```

### 4. Agent Deployment
- Provisioned a Debian 13 VM in Proxmox as the monitored endpoint
- Installed the Wazuh agent package on the Debian VM
- Configured the agent to point to the Wazuh manager IP address
- Enrolled the agent and verified it appeared as active in the Wazuh dashboard

```bash
# Install Wazuh agent on Debian endpoint
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -
apt-get install wazuh-agent

# Configure manager IP and start agent
nano /var/ossec/etc/ossec.conf
systemctl start wazuh-agent
```

### 5. Log Collection & Alerting
- Configured Wazuh to collect syslog, auth.log, and dpkg logs from enrolled agents
- Triggered test security events (failed SSH logins, sudo usage) to validate alerting
- Verified alerts appeared in the Wazuh dashboard under the Security Events module
- Reviewed MITRE ATT&CK mapping for generated alerts

### 6. CIS Benchmark Hardening
- Ran the Wazuh SCA (Security Configuration Assessment) policy against the Debian 13 endpoint
- Reviewed initial benchmark score and identified failing controls
- Applied CIS Debian 13 hardening remediations including SSH configuration, password policies, and service hardening
- Re-ran the SCA scan and documented score improvement from 40% to 60%

```bash
# View SCA results in Wazuh dashboard
# Navigate to: Agents > SCA > CIS Debian Linux 13 Benchmark
```

---

## 🛠️ Tools and Technologies

| Tool | Purpose |
|---|---|
| Proxmox VE | Type 1 hypervisor for VM provisioning |
| Wazuh Manager | SIEM and XDR security monitoring platform |
| Wazuh Indexer | Log indexing and search (OpenSearch-based) |
| Wazuh Dashboard | Web-based security event visualization |
| Debian 13 | Linux endpoint OS for monitored agent |
| OpenSSH | Secure remote access to VMs |
| CIS Benchmarks | Security hardening compliance framework |

---

## 🧠 Skills Demonstrated

- Bare-metal hypervisor installation and configuration with Proxmox VE
- SIEM deployment and service troubleshooting in a virtualized environment
- Wazuh agent enrollment and endpoint monitoring configuration
- Log collection, parsing, and real-time security alerting
- MITRE ATT&CK framework mapping via Wazuh alert correlation
- CIS Benchmark SCA scanning, remediation, and score improvement
- Linux system hardening on Debian 13

---

## ⚠️ Note

This homelab is built for learning and documentation purposes. New modules will be added as the environment expands.
