🟣 WAKANDA CYBER DEFENSE NETWORK (WCDN)
⚡ AEGIS AUTOMATION PROTOCOL
Codename: OPERATION STORMBREAKER
🧭 MISSION OBJECTIVE

Design and deploy an automated cloud incident response pipeline using AWS native tools.
This system detects malicious activity using GuardDuty and instantly responds through Lambda, SNS, and EventBridge.

This project demonstrates:

Serverless automation

Cloud incident response

Event-driven architecture

Threat intelligence integration

GuardDuty custom threat lists

API event correlation

Automated containment

⚙️ AEGIS PIPELINE BLUEPRINT
GuardDuty Findings  →  EventBridge Rule  →  Lambda Auto-Response
                                ↓
                            SNS Alerts

🔥 OPERATION LOG (Methodology)
Phase 1 — Threat Detection Setup

Enabled GuardDuty

Created a custom threat list hosted in S3

Added known malicious IPs

Verified threat list ingestion in GuardDuty

Phase 2 — Triggering the Signal

Simulated multiple malicious behaviors:

Outbound attempts to malicious IPs

Suspicious recon traffic

Unauthorized port scanning

Invalid IAM actions

GuardDuty successfully generated findings.

Phase 3 — EventBridge Automation

Created EventBridge rule:

Trigger: GuardDuty Finding = HIGH / MEDIUM

Action: Invoke Lambda

Tested trigger using sample GuardDuty findings

Phase 4 — Lambda Auto-Containment

Created Lambda function ("JARVIS-Lockdown") to:

Stop compromised EC2 instance

Tag instance as isolated

Publish message to SNS

Log event to CloudWatch

Phase 5 — SNS Alerting

SNS notifications delivered:

Instance ID

Type of malicious activity

Time of detection

Containment action taken

Phase 6 — Validation

Verified:

EC2 instance status changed to stopped

CloudTrail captured API events

GuardDuty findings matched expected behaviors

SNS alerts delivered instantly

Lambda logs confirmed successful execution

🎯 MITRE ATT&CK MAPPINGS
Technique	MITRE ID	Tactic
Outbound C2 Traffic	T1071	Command & Control
Malicious IP Communication	T1588	Reconnaissance
Unauthorized Instance Behavior	T1078	Privilege Abuse
Automated Containment	T1562	Defense Evasion
🧠 LESSONS LEARNED

Event-driven automation drastically reduces response time

GuardDuty custom threat lists improve accuracy

Lambda is ideal for fast containment actions

SNS provides real-time visibility

Combined telemetry (GD + CT + VPC Logs) offers full context

🚀 NEXT STEPS

Add remediation for IAM key exposure

Quarantine EC2 instances using security groups

Trigger Slack or Teams webhook alerts

Expand automation to multi-account GuardDuty

📂 REPOSITORY STRUCTURE
WCDN-Aegis-Automation-Protocol/
│── README.md
│── lambda/
│     └── jarvis-lockdown.py   (optional if you want to upload)
│── eventbridge/
│     └── rule.json            (optional)
│── documentation/
│     ├── methodology.md
│     ├── findings.md
│     └── screenshots/
└── reports/
      └── executive-summary.pdf

📜 ETHICAL DISCLAIMER

All activities were performed in a secure cloud training environment.
No production systems or real organizations were impacted.
