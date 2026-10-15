# Week 1 — Wazuh Mini-SOC Lab

**Blue Team Capstone | LabShop Security Operations Center**

## 1. Project Overview

This project documents the deployment of an isolated Mini Security Operations Center (SOC) for a fictional online retail company, LabShop.

The environment uses Wazuh SIEM to provide centralized security monitoring, endpoint visibility, and security event detection across Windows and Linux systems.

The infrastructure was built using VMware Workstation and PNETLab.

## 2. Technologies Used

- VMware Workstation
- PNETLab
- Ubuntu Server 24.04 LTS
- Windows 10 Enterprise Evaluation
- Docker Engine and Docker Compose
- Wazuh SIEM 4.14.8
- Wazuh Agents 4.14.8
- Microsoft Sysmon
- SwiftOnSecurity Sysmon Configuration
- Apache HTTP Server
- Git and GitHub

## 3. Lab Architecture

The laboratory consists of three virtual machines connected through an isolated SOC-LAN network.

| Virtual Machine | Operating System | IP Address | Role |
|---|---|---|---|
| siem-host | Ubuntu Server 24.04 LTS | 10.10.10.10 | Wazuh SIEM |
| win-client | Windows 10 Enterprise | 10.10.10.20 | Windows Endpoint |
| ubuntu-web | Ubuntu Server 24.04 LTS | 10.10.10.30 | Linux Web Server |

### Network Configuration

- SOC-LAN: 10.10.10.0/24
- Management/NAT: 192.168.22.0/24
- Only the SIEM host has a permanent NAT connection.
- Both monitored endpoints operate without default IPv4 routes.
- Wazuh Dashboard: https://10.10.10.10
- Apache Web Server: http://10.10.10.30

The Dashboard uses laboratory-generated TLS certificates, which may produce a browser trust warning.

## 4. Wazuh Deployment

Wazuh 4.14.8 was deployed using the official Wazuh Docker single-node configuration.

The deployment includes:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

Docker Engine version 29.9.0 and Docker Compose version 5.6.0 were used.

The required Linux kernel parameter was configured as:

vm.max_map_count = 262144

Persistent Docker volumes are used to retain Wazuh data.

## 5. Endpoint Monitoring

Two Wazuh agents were deployed successfully:

| Agent | Operating System | Status |
|---|---|---|
| win-client | Windows 10 | Active |
| ubuntu-web | Ubuntu 24.04 | Active |

Microsoft Sysmon was integrated into the Windows endpoint to collect detailed process creation telemetry.

Apache HTTP Server was installed on the Ubuntu endpoint to provide a monitored web service.

## 6. Security Detection Tests

Five controlled security events were generated and detected.

| Test | Endpoint | Result |
|---|---|---|
| Failed SSH Login | ubuntu-web | Detected |
| Successful Sudo Execution | ubuntu-web | Detected |
| Linux Local User Creation | ubuntu-web | Detected |
| Sysmon Process Creation | win-client | Detected |
| Failed Windows Logon | win-client | Detected |

The Linux user creation event was associated with MITRE ATT&CK technique T1136 — Create Account.

## 7. Starting the Lab

1. Start the PNETLab environment.
2. Start the siem-host virtual machine.
3. Wait for Docker and the Wazuh services to become available.
4. Start the Windows and Ubuntu endpoints.
5. Verify agent connectivity from the Wazuh Dashboard.

### Wazuh Container Verification

On the SIEM host:

    cd ~/wazuh-docker/single-node
    sudo docker compose ps

If the Wazuh stack is stopped:

    sudo docker compose start

For an initial deployment or to recreate missing containers from the existing Compose configuration:

    sudo docker compose up -d

Access the Dashboard:

    https://10.10.10.10

## 8. Stopping the Lab

1. Shut down the Windows and Ubuntu endpoints gracefully.
2. Connect to the SIEM host.
3. Stop the Wazuh Docker stack:

    cd ~/wazuh-docker/single-node
    sudo docker compose stop

4. Shut down the SIEM host.
5. Stop the PNETLab environment if required.

Do not use docker compose down -v because it removes named volumes.

## 9. Persistence Verification

The Wazuh stack was tested using:

    sudo docker compose stop
    sudo docker compose start

The SIEM host was also rebooted.

Previously generated security alerts remained available after these operations.

## 10. Container Memory Usage

Idle memory measurements:

| Component | Memory Usage | Memory Percentage |
|---|---|---|
| Wazuh Dashboard | 258.5 MiB | 1.62% |
| Wazuh Manager | 499.3 MiB | 3.12% |
| Wazuh Indexer | 1.504 GiB | 9.63% |

These values represent a measurement snapshot, not fixed resource limits.

## 11. Repository Structure

- architecture/ — Network diagram and IP addressing
- evidence/ — Deployment and detection screenshots
- journal.md — Lab activities, issues, and solutions
- README.md — Project overview and operating procedures

## 12. Security Considerations

- The SOC-LAN is isolated from direct internet access.
- Endpoint systems do not have permanent default IPv4 routes.
- Sensitive credentials, certificates, and private keys are excluded from version control.
- Live Wazuh data volumes are not stored in the GitHub repository.
- The environment is intended for authorized laboratory testing and education.

## 13. Project Status

Week 1 infrastructure deployment, endpoint integration, security event detection, and persistence testing have been completed.

Documentation and final repository preparation are in progress.
