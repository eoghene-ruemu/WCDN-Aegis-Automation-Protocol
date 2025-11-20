# Methodology  
## Automation Workflow — WCDN Aegis Automation Protocol

This project implements a fully automated cloud incident response workflow using AWS-native services. The goal of the Aegis Automation Protocol was to detect threats originating from a compromised EC2 instance, generate actionable alerts through AWS GuardDuty and Security Hub, and trigger automated containment through an AWS Lambda quarantine function. This methodology outlines the architecture, tools, configuration steps, and decision-making processes used throughout the project.

---

## 1. Environment Setup

Two EC2 instances were deployed to simulate a normal application server and a compromised host. The “Hydra-Infiltrator” instance represented the attacker-controlled machine, used intentionally to generate suspicious traffic and invoke GuardDuty findings.

Initial setup tasks included:

- Launching EC2 instances with required IAM roles  
- Ensuring VPC logging and CloudTrail were enabled  
- Establishing a controlled environment to simulate malicious activity  

This baseline allowed consistent reproduction of threat patterns.

---

## 2. GuardDuty Configuration

AWS GuardDuty was enabled to provide continuous threat detection across:

- IAM behavior anomalies  
- EC2 network indicators  
- DNS anomalies  
- Unauthorized access patterns  
- Reconnaissance events  

A custom threat list was uploaded to an S3 bucket and integrated into GuardDuty’s detector settings to improve detection of known malicious IP addresses.

This setup ensured that any suspicious communication originating from the compromised EC2 instance would trigger high-fidelity alerts.

---

## 3. Security Hub Enablement

Security Hub was activated to aggregate GuardDuty findings, evaluate environment compliance, and centralize findings from multiple services.

Configuration included:

- Enabling foundational security controls  
- Integrating GuardDuty findings  
- Normalizing findings across AWS security services  

Security Hub served as the single pane of glass for alerts, compliance checks, and investigation paths.

---

## 4. SNS Alerting Pipeline

An SNS topic was configured to deliver high-severity alerts from Security Hub and GuardDuty to SOC analysts.

Configuration tasks:

- Creating the SNS topic for alert broadcasts  
- Assigning publish/subscribe permissions  
- Linking Security Hub to SNS through EventBridge rules  

This ensured that critical findings generated immediate notifications for incident responders.

---

## 5. Automated Containment — Lambda Quarantine Function

A Lambda function (`JARVIS-Lockdown-Hydra`) was created to perform automated quarantine actions when triggered.

Primary responsibilities of the function included:

- Identifying the compromised EC2 instance  
- Removing its IAM instance profile  
- Isolating the instance by modifying its security group  
- Tagging the instance to mark it as quarantined  

This automation reduced response time and demonstrated real-world cloud IR workflows.

---

## 6. EventBridge Rule (Automation Trigger)

An EventBridge rule (“Time-Stone Protocol”) was configured to invoke the Lambda function under specific conditions:

- High-severity GuardDuty findings  
- Indicators of compromise (IOC)  
- Unauthorized network activity  
- Credible threat behavior  

When such alerts were generated, EventBridge automatically triggered the Lambda function without analyst intervention.

This created a deterministic, automated response loop for cloud-based threats.

---

## 7. Logging & Monitoring Architecture

Three forms of logging were used to detect and verify malicious activity:

### **1. CloudTrail (“Tesseract Trail”)**
Captured API activity such as:

- IAM misuse  
- Unauthorized changes  
- Privilege escalation attempts  
- Suspicious console access  

### **2. VPC Flow Logs (“Quantum Realm Flow Logs”)**
Captured EC2-level network traffic:

- Outbound communication to malicious IPs  
- Unusual port scanning  
- Non-standard protocols  

### **3. Security Hub & GuardDuty Findings**
Provided curated intelligence from AWS’s threat models.

By correlating flow logs, API logs, and GuardDuty findings, the team validated detection accuracy and response behavior.

---

## 8. Testing & Attack Simulation

The “Hydra-Infiltrator” instance was used to generate:

- Port scans  
- Failed authentication attempts  
- Outbound calls to known malicious IPs  
- Attempts to escalate privileges  

Each action validated:

- GuardDuty detection  
- EventBridge routing  
- Lambda response  
- SNS notification delivery  

This produced repeatable evidence of real-world attack behavior.

---

## 9. Cleanup & Decommissioning

Once testing was complete, automated teardown steps restored the environment:

- Deleted EC2 instances  
- Removed SNS topics and EventBridge rules  
- Disabled specific logging components  
- Cleaned up S3 threat list objects  

This ensured cost efficiency and a clean reset for future testing.

---

## 10. Lessons Learned

Key technical takeaways:

- Automation drastically improves containment time  
- GuardDuty paired with Security Hub enhances signal accuracy  
- EventBridge is a reliable, scalable trigger mechanism  
- Logging sources must be correlated to validate threat behavior  
- IAM permissions must be carefully managed to avoid alerting gaps  

---

## Summary  
This methodology demonstrates a complete automation pipeline for cloud incident response, combining detection, aggregation, notification, and automated containment. It reflects industry-standard SOC and Cloud IR workflows and shows readiness for roles in cloud security, detection engineering, and automated incident response.

