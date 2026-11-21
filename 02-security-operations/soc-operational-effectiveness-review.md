# SOC Operational Effectiveness — Executive Assessment

**Assessment date:** 20 November 2026 | **Environment:** Authorized, single-operator virtual cybersecurity lab  
**Audience:** Cybersecurity Operations Director / Information Security Officer | **Decision:** Continue controlled pilot; do not characterize as production-ready

## Executive decision brief

**Overall posture: Functional detection pilot with material operational-readiness gaps.** The lab has demonstrated a real blocked cross-zone SSH attempt, OPNsense firewall logging, rsyslog collection, Wazuh agent forwarding, `pf` decoding, and manager-side level-5 alert generation. A separate live host-event test generated a level-5 alert. Simulated firewall tests established level-10 management-access matching and level-12 repeated-access correlation, plus negative tests against unrelated source addresses and alternate destinations. These are meaningful control validations, but they do **not** establish comprehensive monitoring coverage, sustained availability, incident-response performance, or production suitability.

**Recommended decision:** Retain the present architecture for testing while prioritizing monitoring evidence, restore-tested recovery, and documented incident ownership ahead of new detection rules.

## 1. Operational readiness — verified versus open

| Capability | Status | Evidence or limitation |
| --- | --- | --- |
| Segmentation control | **Verified for one flow** | OPNsense blocked one controlled DMZ → TEST-NET TCP/22 attempt; three SYN retries logged |
| Network security detection | **Verified live** | Firewall → remote rsyslog → LAB-Ubuntu Wazuh agent → manager `pf` decoder → rule `100104`, level 5 |
| Host detection | **Verified live** | LAB-Ubuntu journal test event → rule `100108`, level 5, in manager `alerts.json` |
| Prioritized detection logic | **Verified offline** | Rules `100105` (level 10) and `100106` (level 12) triggered under simulated conditions |
| Negative rule tests | **Verified offline** | Mixed sources avoided level-12 escalation; alternate destination matched level-5 base rule rather than level-10 management rule |
| Dashboard indexing / investigation workflow | **Indexing verified for tested alerts; case workflow open** | Three known rule-100104 live alerts searchable in dashboard; acknowledgement and closure evidence outstanding |
| Sustained telemetry / retention | **Rotation recovered; ongoing assurance open** | Linux logrotate service successfully executed after permission correction and remote OPNsense syslog resumed; post-rotation Wazuh ingestion, completeness and retention still unverified |
| Recovery readiness | **At risk** | Previous SSD outage interrupted the administrative jump host; backup reported, restore test outstanding |

*Interpretation:* A live firewall alert establishes that one monitored control worked at that time, **not** that all zones are isolated or all threats are detected. The alternate-destination test does not independently prove the `same_dstip` operator, because the prerequisite management rule matches only one protected destination.

## 2. Principal risks and executive treatment decisions

| Priority | Risk / business consequence | Evidence / uncertainty | Recommended treatment |
| --- | --- | --- | --- |
| **High** | Monitoring and management access disrupted by storage or jump-host dependency | SSD temporarily became inaccessible; service restored after restart; underlying cause unresolved | Inspect connection/hardware, monitor errors, test VM restore, document alternate administrative access |
| **High** | Incomplete visibility creates missed or late detection | Wazuh dashboard search confirmed for three known firewall alerts, but collector restricts firewall collection to blocked DMZ/TEST-NET events; full source inventory and dashboard coverage unmeasured | Approve log-source scope, identify excluded high-value events, validate each source with a tracked test |
| **High** | Unowned alerts can remain untriaged even when detection succeeds | Rules generate alerts; no verified case acknowledgement, escalation, handoff or closure workflow | Adopt a single-operator RACI and response playbook; record triage and containment decisions |
| **Medium** | Inter-zone rules may permit unintended paths | One deny path verified; complete policy review, exceptions and red-router forwarding not validated | Inventory allow/deny paths, review exceptions, run representative controlled tests |
| **Medium** | Correlation may overcount packet retransmissions or miss certain conditions | Three blocked SYN packets produced three alerts; synthetic correlation fired, but duplicate handling and real-world alert precision unmeasured | Define event-versus-incident grouping, test distinct attempts, quantify duplicate volume and tune only with evidence |

**Risk acceptance:** None of the above is assumed accepted. The lab owner should document acceptance or remediation after evaluating time, impact and recoverability.

## 3. Response governance — proposed operating model

In this single-operator lab, the same person may fill multiple roles, but the **decision steps remain separate**:

| Responsibility | Proposed owner / control |
| --- | --- |
| Control and detection owner | Lab security lead: rules, data sources, validation evidence and change history |
| Alert triage and severity | Analyst role: confirm source, target, timeline, asset criticality and supporting events; distinguish retries from unique attempts |
| Containment authorization | Incident lead: approve specific firewall/isolation change, expected impact, rollback and validation plan |
| Recovery and closure | Infrastructure role: restore service; incident lead verifies logs, access, functionality, evidence preservation and lessons learned |

**Decision rule:** A blocked attempt alone is not a confirmed compromise. Prioritize escalation based on repeated attempts, protected asset sensitivity, corroborating host activity and evidence of successful access. The Wazuh rule severity is an input to triage, not an automatic business-impact classification.

## 4. Proposed management dashboard — definitions before targets

| KPI | Measurement | Current baseline |
| --- | --- | --- |
| Verified log-source coverage | Validated reporting sources / approved in-scope sources | **Unmeasured** — denominator not approved |
| Detection-to-alert delay | Manager alert time minus trustworthy source time, with synchronized clocks | **Unmeasured** across repeated tests |
| Collection continuity | Successful scheduled probe windows / scheduled windows | **Unmeasured** |
| Actionable-alert ratio | True actionable investigations / analyst-reviewed alert groups | **Unmeasured**; group retransmissions first |
| Mean time to acknowledge | Analyst acknowledgement minus first qualifying alert | **Unmeasured** |
| Recovery assurance | Successfully restore-tested critical VM backups / critical VM backups selected for test | **Not established** |

**Governance:** First establish repeatable baselines, then set service objectives; avoid invented performance percentages and premature 24×7 SOC claims.

## 5. 30 / 60 / 90-day improvement roadmap

| Horizon | Priority actions | Exit evidence / decision gate |
| --- | --- | --- |
| **Days 1–30: Stabilize** | Complete storage/connection checks; verify backup integrity and isolated restore; create telemetry inventory; record verified dashboard alert search and expand indexing coverage checks; implement case template and on-call/owner decision tree | Restore evidence, signed log-source inventory, one end-to-end dashboard-searchable alert and one documented triage case |
| **Days 31–60: Measure** | Run repeatable source heartbeat/ingestion probes; inspect rotation and retention; review firewall exceptions and red-router paths; test false-positive grouping and targeted rule correlation | Initial KPI baseline, retention record, zone test matrix, documented alert-tuning decisions |
| **Days 61–90: Operationalize** | Tabletop an escalation and recovery; rehearse a reversible containment; measure acknowledgement and restoration; review residual risks and capacity | Closed incident exercise, approved playbook, risk register with accountable owners, 90-day leadership review |

These are **proposed milestones, not completed accomplishments**. The schedule assumes a single operator and may be resequenced for hardware or safety dependencies.

## Executive conclusion

The strongest demonstrated capability is **a complete, evidence-backed preventive-to-detective control chain**: denied cross-zone traffic was captured and elevated into Wazuh analytics. The largest remaining weakness is **operational assurance**: a detection can fire while the team still lacks verified coverage, recoverable infrastructure, reliable case handling and meaningful performance measures.

The recommended next investment is **reliability, visibility and governance before more tools**. This converts a functioning lab into a credible example of risk-based cybersecurity operations leadership.

---

### Evidence and scope

- [Security Operations & Monitoring — live detections and offline rule tests](README.md)
- [Enterprise Security Architecture — network scope and control limitations](../01-security-architecture/README.md)
- [Storage Availability Incident — recovery and unresolved hardware risk](../01-security-architecture/storage-availability-incident.md)

*All claims are based on operator-provided test outputs and lab documentation. Synthetic rule tests are labeled as such. This assessment is not an audit, production certification, or claim of enterprise-scale SOC management.*
