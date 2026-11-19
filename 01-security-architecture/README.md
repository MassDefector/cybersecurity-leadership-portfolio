# 01 — Enterprise Security Architecture

**Project type:** Independent cybersecurity homelab  
**Status:** Current-state documentation in progress — host and VM inventory captured on 2026-11-18  
**Technology:** Proxmox VE, OPNsense, Linux

## Executive summary

This project documents the design and operation of a segmented virtual laboratory used to explore enterprise network security, firewall policy, infrastructure operations, and security monitoring. The emphasis is on explaining architectural decisions and risk tradeoffs as well as demonstrating technical implementation.

## Business and security objectives

- Separate internal administration, test services, and security monitoring workloads.
- Control and document traffic between network segments.
- Centralize relevant security telemetry for investigation.
- Maintain a repeatable approach to change management, validation, and recovery.

## Verified virtualization baseline (2026-11-18)

Source: administrator-supplied output from `pveversion`, `qm list`, `pvesh ... network`, and `free -h`. Details below are intentionally generalized for public publication.

- **Hypervisor:** Proxmox VE 9.1.1, kernel 6.17.2-1-pve.
- **Host memory:** 31 GiB reported; 14 GiB used and approximately 17 GiB available at collection; 8 GiB swap configured, none in use.
- **Virtual machines:** Seven configured; six running at capture time.
- **Running roles inferred from VM names:** OPNsense firewall (2 GiB allocated), two Alpine test endpoints (512 MiB each), a red-team router (1 GiB), a red-team test endpoint (512 MiB), and a Wazuh SIEM (8 GiB).
- **Stopped workload:** One Ubuntu lab VM (2 GiB allocated).
- **Virtual bridges:** Five active bridges: one host-addressed bridge and four without host IP addresses shown. Physical uplink binding, VM network attachments, and firewall enforcement remain unverified.
- **Capacity caution:** RAM allocation is not a measure of guest utilization. No claims are made about the SIEM being fully operational based on VM power status alone.

## Previously used lab network segments

| Segment | Lab subnet | Intended purpose |
| --- | --- | --- |
| LAN | `10.10.10.0/24` | Internal systems and administration |
| DMZ | `10.10.20.0/24` | Isolated test services |
| SOC | `10.10.30.0/24` | Security monitoring |

**Verification pending:** These subnets reflect earlier lab exercises; current interface assignments, firewall rules, and active workloads must be revalidated before this document is treated as a current-state architecture. Public diagrams will use illustrative addresses or redacted details.

## Architecture inventory — to be verified

| Component | Function | Evidence required |
| --- | --- | --- |
| Proxmox VE | Verified host and virtual bridges | VM-to-bridge mapping and physical uplink binding |
| OPNsense | Running VM; security enforcement not yet verified | Sanitized interface and rule summaries |
| Linux server(s) | Internal services and log reception | Sanitized service inventory |
| DMZ workload(s) | Controlled test systems | Sanitized network and service descriptions |
| Security monitoring | Wazuh SIEM VM running; ingestion not verified | Data-flow description and alert examples |

## Security design and risk decisions

_To be completed after the current-state assessment._

- **Trust boundaries:** Which systems can initiate connections across segments?
- **Least privilege:** Which network flows are explicitly permitted, and why?
- **Logging:** What is collected, retained, and reviewed?
- **Recovery:** How are configuration changes tested and reversed?
- **Residual risk:** What limitations remain due to hardware, architecture, or lab scope?

## Evidence and validation plan

- [x] Capture initial sanitized Proxmox VM inventory and active bridge list.
- [ ] Verify VM-to-bridge attachments, NIC uplinks, and the full bridge configuration.
- [ ] Confirm current OPNsense interfaces and security zones.
- [ ] Document permitted and denied inter-segment flows.
- [ ] Create an accurate, sanitized architecture diagram.
- [ ] Validate segmentation with controlled tests.
- [ ] Document at least three risks and their mitigations.
- [ ] Write a one-page executive summary of findings and next steps.

## Portfolio integrity and safety

All exercises take place on personally owned or explicitly authorized infrastructure. Published artifacts will exclude passwords, tokens, real identifying information, sensitive configuration details, and any military operational data. Planned capabilities will not be represented as implemented until verified.

## Lessons learned

_To be added after evidence collection and validation._
