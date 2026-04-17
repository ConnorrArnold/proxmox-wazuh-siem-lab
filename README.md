# 🛡️ Proxmox Wazuh SIEM Lab

A personal on-premises SIEM homelab built on Proxmox VE, deploying Wazuh for centralized log collection, threat detection, and security monitoring. This repo documents the end-to-end process of provisioning virtual machines, deploying the Wazuh stack, recovering services, onboarding agents, and hardening systems to CIS benchmarks.

**Lab architecture:** a Proxmox VE host running a Wazuh all-in-one appliance VM (manager, indexer, dashboard) and a Debian 13 agent VM on an isolated lab network.

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
- Downloaded the Proxmox VE ISO and flashed it to a bootable USB drive
- Installed Proxmox on bare-metal hardware and completed initial configuration
- Accessed the Proxmox web UI and configured networking for VM connectivity
- Set up local storage for VM disk images and ISO uploads

### 2. Wazuh Server Deployment
- Downloaded the official Wazuh all-in-one OVA appliance
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
- Diagnosed the issue as a default `server.host` binding that only listened on localhost, combined with a self-signed certificate path mismatch
- Edited `/etc/wazuh-dashboard/opensearch_dashboards.yml` to bind to `0.0.0.0` and corrected the certificate paths
- Restarted the dashboard service and confirmed browser access over HTTPS

```bash
# Edit dashboard configuration
sudo nano /etc/wazuh-dashboard/opensearch_dashboards.yml

# Restart dashboard service
sudo systemctl restart wazuh-dashboard
```

### 4. Agent Deployment
- Provisioned a Debian 13 VM in Proxmox as the monitored endpoint
- Added the Wazuh APT repository using the modern signed-by keyring method (replacing the deprecated `apt-key`)
- Installed the Wazuh agent and registered it against the manager using the `WAZUH_MANAGER` environment variable
- Enabled the agent on boot and verified it appeared as active in the Wazuh dashboard

```bash
# Import Wazuh GPG key using modern signed-by method
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | \
  sudo gpg --dearmor -o /usr/share/keyrings/wazuh.gpg

# Add the Wazuh APT repository
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | \
  sudo tee /etc/apt/sources.list.d/wazuh.list

# Install the agent and point it at the manager
sudo apt-get update
sudo WAZUH_MANAGER="<manager-ip>" apt-get install -y wazuh-agent

# Enable and start the agent
sudo systemctl daemon-reload
sudo systemctl enable --now wazuh-agent
```

### 5. Log Collection & Alerting
- Configured Wazuh to collect syslog, auth.log, and dpkg logs from enrolled agents
- Triggered test security events (failed SSH logins, sudo usage) to validate alerting
- Verified alerts appeared in the Wazuh dashboard under the Security Events module
- Reviewed MITRE ATT&CK mapping for generated alerts

### 6. CIS Benchmark Hardening
- Ran the Wazuh SCA (Security Configuration Assessment) policy against the Debian 13 endpoint
- Reviewed the initial benchmark score and identified failing controls
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
| Proxmox VE | Type-1 hypervisor for VM provisioning |
| Wazuh Manager | Unified XDR and SIEM security monitoring platform |
| Wazuh Indexer | Log indexing and search (OpenSearch-based) |
| Wazuh Dashboard | Web-based security event visualization |
| Debian 13 | Linux endpoint OS for the monitored agent |
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
