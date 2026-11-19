# 01 — Enterprise Security Architecture

**Project type:** Independent cybersecurity homelab  
**Status:** Current-state documentation in progress  
**Technology:** Proxmox VE, OPNsense, Linux

## Executive summary

This project documents the design and operation of a segmented virtual laboratory used to explore enterprise network security, firewall policy, infrastructure operations, and security monitoring. The emphasis is on explaining architectural decisions and risk tradeoffs as well as demonstrating technical implementation.

## Business and security objectives

- Separate internal administration, test services, and security monitoring workloads.
- Control and document traffic between network segments.
- Centralize relevant security telemetry for investigation.
- Maintain a repeatable approach to change management, validation, and recovery.

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
| Proxmox VE | Virtualization host and virtual networking | Sanitized VM and bridge inventory |
| OPNsense | Routing, firewall enforcement, network segmentation | Sanitized interface and rule summaries |
| Linux server(s) | Internal services and log reception | Sanitized service inventory |
| DMZ workload(s) | Controlled test systems | Sanitized network and service descriptions |
| Security monitoring | Collection and analysis of lab telemetry | Data-flow description and alert examples |

## Security design and risk decisions

_To be completed after the current-state assessment._

- **Trust boundaries:** Which systems can initiate connections across segments?
- **Least privilege:** Which network flows are explicitly permitted, and why?
- **Logging:** What is collected, retained, and reviewed?
- **Recovery:** How are configuration changes tested and reversed?
- **Residual risk:** What limitations remain due to hardware, architecture, or lab scope?

## Evidence and validation plan

- [ ] Capture sanitized Proxmox VM inventory and network bridge configuration.
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
