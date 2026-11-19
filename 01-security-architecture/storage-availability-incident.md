# Infrastructure Availability Incident — SSD Storage and Management Access

**Environment:** Personally operated Proxmox cybersecurity laboratory  
**Incident window:** November 17–18, 2026 (observed diagnostics)  
**Status:** Service recovered; underlying fault **not conclusively resolved**  
**Audience:** Infrastructure, cybersecurity operations, and IT leadership

## Executive summary

A storage availability incident interrupted a Proxmox-hosted Ubuntu administrative server used as an SSH jump host for management access to the lab firewall. Proxmox reported a missing logical volume, and subsequent inspection showed the physical Samsung SATA SSD temporarily exposed as a **0-byte device**. Kernel diagnostics recorded repeated target communication failures.

A controlled host restart restored access to the SSD-backed LVM-thin storage and enabled the Ubuntu server to start. Administrative SSH connectivity was then restored through a restricted management route, and a backup of the Ubuntu VM was reported complete. The fault was **not** demonstrated to be permanently repaired; storage hardware or connectivity remains an open risk.

## Business and operational impact

- **Service availability:** Ubuntu administration/jump-host VM could not be started while its boot disk storage was unavailable.
- **Management access:** Normal SSH path from a management workstation through the Ubuntu jump host to OPNsense was disrupted.
- **Additional exposure:** A second security-monitoring VM had SSD-backed virtual volumes in the pre-restart device-mapper inventory; its actual data integrity and impact were **not verified**.
- **Security consideration:** Troubleshooting avoided indiscriminately opening SSH from the upstream/WAN-facing network or disabling firewall protections.
- **Data loss:** No data loss was confirmed, but full filesystem and application-integrity validation was not performed.

## Verified technical evidence (sanitized)

| Observation | Evidence | Interpretation |
| --- | --- | --- |
| Proxmox storage error | `no such logical volume ssd-vg/ssd-vg` | SSD-backed thin pool unavailable to Proxmox |
| Missing LVM group | `vgs` initially listed only the primary volume group | Underlying SSD VG was not discoverable |
| Physical device anomaly | Samsung SATA SSD showed `0B` capacity | Device inaccessible at the block layer |
| Kernel errors | Repeated `hostbyte=DID_BAD_TARGET` for the SSD | Storage target could not be addressed successfully |
| SMART identification failure | `INQUIRY failed` during incident | Drive was not responding to identification |
| After restart | SSD reported normal ~119 GiB capacity; SSD storage became active | Storage access restored |
| SMART health | Overall health self-assessment: `PASSED` | Not proof against intermittent cable or controller faults |
| SMART counters | Reallocated sectors: 0; uncorrectable errors: 0; CRC errors: 67 | Media counters reassuring; historical interface errors merit investigation |
| Access recovery | SSH to Ubuntu jump host, then to OPNsense, succeeded | Management path recovered |
| Backup | Operator reported VM backup completed | Recovery artifact exists; restore test still pending |

The **67 CRC errors are a cumulative historical count**. They do not prove that a faulty SATA cable caused this particular incident. Hardware or cable failure has not been conclusively isolated.

## Incident timeline

1. **Detection:** Ubuntu VM startup failed with an LVM storage error.
2. **Scoping:** VM configuration referenced the unavailable SSD-backed LVM-thin storage; a separate disk reference was found on primary storage, but its contents were not verified.
3. **Diagnosis:** Block-device inspection showed the SSD at 0 B; LVM did not discover its volume group; kernel and SMART checks indicated communication failure.
4. **Recovery:** After a Proxmox host restart, the SSD and LVM-thin storage were detected and the Ubuntu VM started.
5. **Service verification:** Ubuntu was able to reach the lab firewall internally. SSH from the management workstation was restored through a host-specific route via the firewall's upstream-facing address; SSH from Ubuntu to the firewall worked.
6. **Data protection:** A VM backup to separate primary-host storage was reported complete.
7. **Open follow-up:** Inspect/replace SATA cable if warranted, inspect power/port connections, monitor counters and kernel errors, and test a restore.

## Decisions and tradeoffs

| Decision | Rationale | Residual risk |
| --- | --- | --- |
| Avoid LVM recreation, formatting, or VM disk reassignment during diagnosis | Preserve potential recoverability and reduce accidental data loss | Recovery delayed until physical storage returned |
| Use read-only inspection first | Distinguish missing VG definition from unreachable physical media | Diagnosis cannot conclusively determine failed component |
| Preserve segmented management approach | Avoid loosening WAN exposure to solve an administrative access problem | Jump host remains an operational dependency |
| Back up recovered Ubuntu VM | Reduce the consequence of a repeat SSD disappearance | Backup usability not yet proven by restore |
| Treat SMART PASS cautiously | Overall self-assessment does not exclude intermittent communication faults | Drive should remain under observation |

## Corrective actions and owner checklist

- [x] Restore access to SSD storage and restart affected administration VM.
- [x] Validate administrator-to-jump-host and jump-host-to-firewall SSH access.
- [x] Report completion of backup to separate physical storage.
- [ ] Verify backup archive and perform a non-destructive test restore to an isolated VM ID.
- [ ] Review current and historical SATA link reset / block I/O logs.
- [ ] Inspect or replace SATA data cable and reseat power connectors during planned downtime.
- [ ] Record SMART `CRC_Error_Count` baseline (67) and check for increases.
- [ ] Confirm health and recoverability of security-monitoring VM disks.
- [ ] Document a recovery-point objective (RPO), recovery-time objective (RTO), and backup schedule.
- [ ] Assess a second management-access method for jump-host outages.

## Leadership lessons

**Availability is a security operations requirement.** Segmentation protects the lab, but a single inaccessible jump host can slow administration during an outage. A reliable operational design requires alternate access methods, recovery-tested backups, validated telemetry, and explicit ownership of remaining hardware risks.

**Outcome:** Access recovered and a backup was reported complete; permanent root-cause remediation and restore testing remain pending.

## Evidence handling

This case study is sanitized from administrator-provided diagnostic commands and operational troubleshooting. Internal addresses, device serial numbers, personal identifiers, and unrelated infrastructure details are omitted. No synthetic test results are presented as observed events.
