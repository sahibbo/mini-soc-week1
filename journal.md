# Week 1 — Mini-SOC Lab Journal

**Project:** LabShop Mini-SOC
**Platform:** Wazuh 4.14.8
**Environment:** VMware Workstation / PNETLab
**Period:** October 2026

---

## Entry 01 — Virtual Infrastructure Deployment

**Task:** Prepare three virtual machines for the SOC laboratory.

**Activities:**
- Deployed Ubuntu Server as the central SIEM host.
- Prepared Windows and Ubuntu monitoring endpoints.
- Allocated system resources.
- Created clean installation checkpoints.

**Challenge:**
Preparing reusable virtual machines and preserving their initial installation states.

**Resolution:**
Verified operating system installations and created QCOW2 snapshots using PNETLab.

**Result:** Three virtual machines prepared for SOC deployment.

---

## Entry 02 — Network Configuration and Isolation

**Task:** Configure internal communication and external management connectivity.

**Activities:**
- Configured SOC-LAN: 10.10.10.0/24.
- Assigned static endpoint IP addresses.
- Configured a separate NAT interface for the SIEM host.
- Tested bidirectional ICMP connectivity.

**Challenge:**
Maintaining endpoint isolation while allowing the SIEM host to download installation packages.

**Resolution:**
Connected only the SIEM host to the NAT network and used the isolated SOC-LAN for endpoint communication.

**Result:** All three machines communicated successfully with 0% packet loss.

---

## Entry 03 — Docker and Wazuh Deployment

**Task:** Deploy the Wazuh 4.14.8 single-node SIEM.

**Activities:**
- Installed Docker Engine and Docker Compose.
- Configured vm.max_map_count=262144.
- Cloned the official Wazuh Docker repository.
- Generated SSL certificates.
- Started Manager, Indexer, and Dashboard containers.

**Challenge:**
The certificate generation container initially failed because the emulated virtual CPU did not expose the required CPU features.

**Resolution:**
Enabled host CPU feature passthrough through PNETLab QEMU configuration.

**Result:** Wazuh certificates generated successfully, and all three containers started.

---

## Entry 04 — Endpoint Agent Integration

**Task:** Connect Windows and Ubuntu endpoints to Wazuh.

**Activities:**
- Downloaded Wazuh Agent 4.14.8 packages on the SIEM host.
- Transferred the Windows installer through a temporary HTTP server.
- Transferred the Ubuntu DEB package using SCP.
- Installed and enabled both Wazuh Agent services.

**Challenge:**
The endpoints had no permanent internet connection for downloading installation packages.

**Resolution:**
Used the SIEM host as an internal package transfer point.

**Result:**
- win-client: Active
- ubuntu-web: Active

---

## Entry 05 — Sysmon and Apache Configuration

**Task:** Extend endpoint telemetry and deploy a monitored web service.

**Activities:**
- Installed Microsoft Sysmon on Windows.
- Applied the SwiftOnSecurity community configuration.
- Added the Sysmon Operational channel to the Wazuh Agent configuration.
- Prepared and transferred Apache packages to Ubuntu.
- Installed and started Apache HTTP Server.

**Challenge:**
Collecting Sysmon telemetry required additional configuration in the Windows Wazuh Agent.

**Resolution:**
Configured the eventchannel log source and restarted the Wazuh Agent service.

**Result:**
- Sysmon Event ID 1 successfully detected in Wazuh.
- Apache default page accessible at http://10.10.10.30.

---

## Entry 06 — Security Detection Testing

**Task:** Generate and investigate security events.

**Activities:**
1. Failed Linux SSH login.
2. Successful Linux sudo command.
3. Linux local user creation.
4. Windows Sysmon process creation.
5. Failed Windows logon.

**Challenge:**
Verifying that events were not only generated locally but also received and classified by Wazuh.

**Resolution:**
Used Wazuh Explore / Discover filters to inspect agent names, event IDs, rule descriptions, and severity levels.

**Result:** All five controlled security events were successfully detected.

**Notable Detection:**
Linux account creation generated Wazuh rule 5902 with severity level 8 and MITRE ATT&CK technique T1136.

---

## Entry 07 — Persistence and Resource Verification

**Task:** Validate recovery and data retention.

**Activities:**
- Stopped the Wazuh Docker stack.
- Restarted the containers.
- Verified previously generated alerts.
- Rebooted the SIEM host.
- Verified automatic container startup.
- Collected Docker memory statistics.

**Challenge:**
Ensuring previously indexed security alerts remained accessible after service and host restarts.

**Resolution:**
Used persistent Docker volumes and verified existing alert records through Wazuh Discover.

**Result:**
- Wazuh containers restarted successfully.
- Previous security alerts remained accessible.
- Docker memory measurements were recorded.

---

## Entry 08 — GitHub Documentation and Version Control

**Task:** Prepare project documentation for version control.

**Activities:**
- Initialized a local Git repository.
- Connected GitHub CLI to the personal GitHub account.
- Created the private GitHub repository.
- Added a .gitignore configuration.
- Prepared README.md and network addressing documentation.
- Created the project journal.

**Challenge:**
Keeping project documentation separate from live Wazuh configurations and sensitive files.

**Resolution:**
Configured Git exclusions for credentials, certificates, private keys, virtual machine images, and other sensitive artifacts.

**Result:** Local repository prepared for final evidence collection, review, and initial push.

---

## Lessons Learned

This laboratory reinforced several practical Blue Team skills:

- Designing isolated monitoring networks.
- Deploying and troubleshooting containerized SIEM infrastructure.
- Integrating Windows and Linux endpoint agents.
- Collecting Sysmon and Linux authentication telemetry.
- Investigating security events and Wazuh detection rules.
- Validating service recovery and data persistence.
- Maintaining structured technical documentation using Git.
