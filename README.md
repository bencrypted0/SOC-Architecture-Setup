# Enterprise SOC Lab From Scratch

Welcome to the repository for the **Enterprise SOC Lab Build**! This project demonstrates how to design, build, and validate a fully functional Security Operations Center (SOC) environment using open-source tools and a hybrid cloud infrastructure.

This repository holds the architecture documentation, automation workflows, and execution cheatsheets to supplement the comprehensive step-by-step practical guide.

## The Practical Guide (Medium Series)
The full, detailed walkthrough of how this lab was built from the ground up is available on Medium. It is broken down into four parts:

- **[Part 1: VPC & SIEM Setup (Wazuh) - Placeholder Link]**
- **[Part 2: Endpoint Forensics (Velociraptor) - Placeholder Link]**
- **[Part 3: SOAR & Automation Pipeline (n8n & AI Integration) - Placeholder Link]**
- **[Part 4: Case Management (Jira) & Attack Simulation (Atomic Red Team) - Placeholder Link]**

---

## 📂 Repository Contents

- `SOC Lab Architecture.md`: High-level diagram and breakdown of the entire architecture, network flow, and tool stack.
- `n8n_SOAR_Workflow.json` *(or similar)*: The fully exported n8n pipeline that handles log ingestion, AI risk scoring, summarization, and ticket creation.
- `Atomic_Red_Team_Cheatsheet.md` *(or similar)*: Commands and parameters used to simulate adversarial techniques (e.g., T1055, T1547.001) for validating our detection engineering.

---

## Architecture & Tool Stack Overview

This lab utilizes a hybrid approach: **Cloud Hosted Infrastructure** (AWS) for the heavy lifting, combined with **Local Endpoints** capturing realistic telemetry.

| Layer               | Tool                    | Purpose                                                         |
| :------------------ | :---------------------- | :-------------------------------------------------------------- |
| **Infrastructure**  | AWS VPC / EC2           | Secure networking (Bastion Host) and cloud server hosting.      |
| **SIEM**            | Wazuh                   | Log ingestion, FIM, correlation, and alerting.                  |
| **EDR**             | Velociraptor            | Endpoint forensics, live VQL hunts, and agent management.       |
| **SOAR**            | n8n                     | Orchestration pipelines, AI enrichment, and logic routing.      |
| **AI Model**        | Gemini / OpenRouter API | Alert summarization, parsing, and automated risk scoring.       |
| **Case Management** | Jira Cloud              | Incident tracking, populated with enriched data & MITRE fields. |
| **Comms**           | Slack                   | SOC alert notifications for the team.                           |
| **Testing**         | Atomic Red Team         | MITRE ATT&CK mapped attack simulations.                         |

### The Data Flow

1. **Telemetry Generation**: Wazuh & Velociraptor agents on local Windows/Linux endpoints stream security events to the Wazuh Manager in AWS.
2. **Detection**: Wazuh normalizes the logs. When an alert hits Rule Level 7 or above, a webhook is fired.
3. **Orchestration & Enrichment**: n8n catches the webhook, identifies the OS, and uses the open-source LLM/Gemini API to attach a risk score and an analyst summary.
4. **Automated Response**:
   - **High Severity**: A highly detailed ticket is auto-generated in Jira, perfectly mapped to the MITRE ATT&CK framework.
   - **Medium/Low Severity**: A notification is sent to the SOC team's Slack channel.

---

## Getting Started

If you are following along with the practical guide, you will need:
1. An **AWS Account** (Note: this setup traverses beyond the Free Tier; monitor your EC2 runtime).
2. Local Virtual Machines (Windows & Linux) to act as endpoints.
3. A free **Jira Cloud** account.
4. An **OpenRouter API Key** or **Google Gemini API Key** for the LLM n8n nodes.
5. A **Slack Workspace** for webhook integrations.

> [!NOTE]
> Ensure you import the n8n workflow file provided in this repository to instantly set up your SOAR pipeline! Simply replace the credential nodes with your own API keys.

---
*Disclaimer: This repository and the associated guides are for educational purposes. Always ensure you have permission before running attack simulations on any network.*
