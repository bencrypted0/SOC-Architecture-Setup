
# SOC Lab Architecture & Automation Build

**Project Plan v1.0** · Open Source Tooling · Cloud + Local Hybrid · End-to-End Detection & Response

---

## Overview

| Field           | Detail                                                               |
| --------------- | -------------------------------------------------------------------- |
| Approach        | Hybrid (AWS Cloud Infrastructure + Local VM Endpoints)               |
| Phases          | 5 Phases                                                             |
| Primary SIEM    | Wazuh                                                                |
| Cost            | Open Source Tools (AWS Infrastructure Cost varies depending on usage)|
| ATT&CK Coverage | Mapped per attack simulation (Atomic Red Team)                       |

**Objective:** Design, build, and validate a fully functional SOC lab using open-source tooling. The lab demonstrates end-to-end capability — log ingestion, threat detection, SOAR automation, AI-driven risk scoring, case management, and EDR forensics — with simulated attacks validating each layer.

---

## Architecture Data Flow

```
Cloud Hosted Infrastructure (AWS VPC with Bastion Host) + Local Endpoints
```

```
Endpoints (Windows/Linux VMs)
    │  Wazuh Agent (Telemetry & Security Events)
    │  Velociraptor Agent (Live Forensics & Management)
    ▼
Wazuh Manager (SIEM)
    │  Ingest → Normalize → Correlate
    │  Custom Detection Rules & FIM Alerts
    │  Webhook trigger on rule level ≥ 7
    ▼
n8n (SOAR)
    │  Receive alert webhook
    ├─► Google Gemini API (OS Classification, Risk Scoring, Summarization)
    │  Normalize enriched data
    ▼
Severity Switch (n8n Logic)
    ├─► [HIGH] ──► Jira Cloud (Create Incident Ticket with MITRE mappings)
    └─► [MED/LOW] ─► Slack (Send Notification)
```

---
## Tool Stack

| Layer             | Tool                   | Purpose                                                      |
| ----------------- | ---------------------- | ------------------------------------------------------------ |
| Infrastructure    | AWS VPC / EC2          | Networking, Bastion Host, and server hosting                 |
| SIEM              | Wazuh                  | Log ingestion, correlation, alerting                         |
| EDR               | Velociraptor           | Endpoint forensics, live VQL hunts, agent management         |
| SOAR              | n8n                    | Orchestration pipelines, AI enrichment, routing              |
| AI Model          | Gemini, OpenRouter API | Alert summarization, parsing, and risk scoring               |
| Case Management   | Jira Cloud             | Incident tracking, populated with enriched data/MITRE fields |
| Communications    | Slack                  | SOC alert notifications                                      |
| Attack Simulation | Atomic Red Team        | MITRE ATT&CK mapped attack simulations                       |

---

## Build Phases

### Phase 1: Infrastructure & SIEM Deployment (Wazuh)
- Provision AWS VPC with public subnet, IGW, and Bastion Host for secure SSH access.
- Deploy Wazuh single-node Docker setup on an EC2 instance (`c7i-flex.large`).
- Install and enroll Wazuh agents on local Linux and Windows endpoints.

### Phase 2: Endpoint Forensics (Velociraptor)
- Deploy Velociraptor Server on an EC2 instance (`t3.medium`) with secure GUI access.
- Generate client configurations and deploy agent to endpoints.
- Establish baseline connectivity for live VQL hunts and artifact collection.

### Phase 3: SOAR & Automation (n8n)
- Deploy n8n via Docker on an EC2 instance.
- Configure Wazuh's built-in Shuffle integration (webhook) to forward alerts with level >= 7 to n8n.
- Integrate Google Gemini into the n8n pipeline for automated parsing, risk scoring, and alert summarization.

### Phase 4: Case Management (Jira)
- Set up a Jira Cloud project ("SOC_Board") with custom fields (MITRE Tactic, Rule ID, Risk Score, etc.).
- Import advanced n8n workflow combining Wazuh alerts, Gemini enrichment, and routing.
- Configure n8n to generate Jira tickets for High severity alerts, and Slack notifications for Med/Low severity alerts.

### Phase 5: Attack Simulation & Validation
- Install Invoke-AtomicRedTeam on the Windows endpoint.
- Simulate MITRE ATT&CK techniques (e.g., Process Injection T1055, T1547.001).
- Validate the entire pipeline: Wazuh detects -> n8n enriches via AI -> Jira creates ticket with MITRE mapping -> Slack notifies the team.

---
