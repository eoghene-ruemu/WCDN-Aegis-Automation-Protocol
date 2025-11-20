# Findings  
## Automated Threat Detection & Response — WCDN Aegis Automation Protocol

This document summarizes all security findings, detections, automated responses, and validation results from the Aegis Automation Protocol. The goal of this project was to demonstrate a full cloud-native incident response cycle using GuardDuty, Security Hub, EventBridge, Lambda, and multiple forms of AWS logging.

Screenshots of findings and test results are located in `documentation/screenshots/`.

---

## 1. GuardDuty Findings

AWS GuardDuty successfully detected multiple categories of suspicious behavior originating from the compromised EC2 instance (“Hydra-Infiltrator”). These events were intentionally generated to validate that the detection pipeline was functioning correctly.

### **Key GuardDuty Alerts Identified:**

#### **Reconnaissance Activity**
- Port scans and unauthorized network probing  
- Detected via GuardDuty’s Recon:EC2Portscan finding type  
- Confirmed through VPC Flow Logs showing repeated connections on unusual ports

#### **Suspicious Outbound Traffic**
- EC2 instance attempted communication with known malicious IP addresses  
- Detected via GuardDuty’s UnauthorizedAccess:EC2/TrafficVolumeUnusual finding type  
- Custom threat list in S3 was used to cross-check IPs

#### **Brute Force Behavior**
- Multiple failed SSH login attempts from Hydra-Infiltrator  
- Detected via UnauthorizedAccess:EC2/SSHBruteForce  
- Confirmed with CloudTrail authentication events

These findings were the primary trigger for automated response workflows.

---

## 2. Security Hub Aggregated Findings

Security Hub centralized all GuardDuty alerts and normalized them for triage. It also provided compliance insights, but the core value in this project was alert aggregation.

### **Notable Observations:**
- Critical and High-severity GuardDuty findings were correctly surfaced  
- Standardized JSON structures allowed easy EventBridge filtering  
- Security Hub served as the “single source of truth” for alert visibility

---

## 3. EventBridge Trigger Behavior

An EventBridge rule (“Time-Stone Protocol”) was configured to trigger the Lambda quarantine function when specific GuardDuty findings occurred.

### **Trigger Verification:**
- High-severity GuardDuty findings successfully triggered the rule  
- EventBridge logs showed successful invocation attempts  
- No false positives or duplicate triggers were observed  

This automation step validated that the detection-to-response flow worked end-to-end.

---

## 4. Lambda Quarantine Function Findings

The Lambda function (“JARVIS-Lockdown-Hydra”) performed automated containment actions on the compromised instance.

### **Actions Taken by the Function:**
- Removed IAM instance profile  
- Attached an isolation security group  
- Tagged the instance as quarantined  
- Logged quarantine steps to CloudWatch Logs  

### **Validation:**
- CloudWatch logs confirmed the function executed without errors  
- The affected EC2 instance lost external connectivity as expected  
- IAM and security groups were updated immediately after invocation  

These results confirmed that the automated response mechanism operated as designed.

---

## 5. CloudTrail Findings

CloudTrail (“Tesseract Trail”) provided visibility into API-level activity.

### **Evidence Identified:**
- SSH brute-force attempts recorded as failed console logins  
- Attempts to modify IAM roles or policies  
- Unauthorized API calls originating from the compromised instance  
- IAM behavior anomalies detected in conjunction with GuardDuty findings  

CloudTrail correlated directly with GuardDuty’s behavioral findings, improving investigation accuracy.

---

## 6. VPC Flow Log Findings

VPC Flow Logs (“Quantum Realm Flow Logs”) provided network-layer evidence.

### **Key Observations:**
- Outbound traffic to blacklisted IP addresses  
- Repetitive connection attempts on ports 22, 3389, and non-standard ports  
- Abnormal spike in rejected traffic from Hydra-Infiltrator  
- Network flows aligned with reconnaissance and exfiltration patterns  

Flow logs served as a secondary validation layer for all GuardDuty alerts.

---

## 7. Post-Mission Validation

The following outcomes were confirmed after completing simulations and testing:

### **Detection Accuracy**
- GuardDuty generated credible findings for every simulated attack  
- No missing or incorrect alerts observed

### **Automation Reliability**
- EventBridge consistently invoked Lambda for qualifying events  
- No stale triggers or failures detected

### **Containment Effectiveness**
- Quarantine actions executed instantly and successfully isolated the compromised instance  
- EC2 security posture was restored to a safe state after automation completed

---

## 8. Lessons Learned

- Multi-source logging (CloudTrail + VPC Flow Logs + GuardDuty) significantly increases threat visibility  
- Automation reduces mean time to contain (MTTC) dramatically  
- IAM least-privilege is crucial — missing permissions can break automation  
- EventBridge filtering must be precise to avoid unnecessary triggers  
- Cloud response workflows require careful sequencing to avoid conflicts  

---

## Conclusion

The Aegis Automation Protocol successfully demonstrated a complete cloud-native detection and automated response framework. GuardDuty identified threats, Security Hub aggregated them, EventBridge routed critical findings, and Lambda executed containment with high reliability. Logging sources validated the findings, creating a robust and repeatable incident response pipeline aligned with real-world SOC practices.

