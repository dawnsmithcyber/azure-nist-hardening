# Azure Cloud Hardening: NIST 800-53 Technical Control Mapping

**Project Focus:** Hardening an Azure Windows Server instance by mapping and implementing NIST 800-53 IA-5 controls. This project demonstrates advanced remote remediation via Azure Run Command and PowerShell to enforce enterprise-grade password complexity and persistence prevention.

## 📋 Project Overview
This project demonstrates the practical application of the **NIST 800-53** security framework within a Microsoft Azure environment. The focus was on translating high-level compliance requirements into concrete system configurations to harden a Windows Server instance against unauthorized access.

## 🎯 Objectives
* Implement **Access Control (AC)** and **Identification and Authentication (IA)** standards.
* Utilize **Azure Run Command** for remote system remediation, simulating an environment where standard management tools (RDP/Admin Center) are restricted.
* Apply a **"Fraud Hunter"** mindset to disrupt common credential-based attack vectors and lateral movement.

## 🛠️ The Challenge: Restricted Management Interfaces
During deployment, standard GUI management tools—such as Windows Admin Center and Remote Desktop (RDP)—were restricted due to Network Security Group (NSG) rules.
* **Solution:** I pivoted to **Remote Remediation**, utilizing the Azure backbone to execute PowerShell scripts directly against the VM instance. This ensured the security posture was hardened without requiring interactive desktop access.

## ⚙️ Technical Implementation (NIST 800-53 IA-5)
I executed the following configurations via the Azure Run Command interface to satisfy **IA-5 (Authenticator Management)** requirements:

| Control | Setting | Defense Mechanism |
| :--- | :--- | :--- |
| **Min Password Length** | 12 Characters | Increases entropy to significantly mitigate Brute Force and Credential Stuffing. |
| **Password History** | 5 Unique Passwords | Prevents Password Cycling, a common habit where users rotate between known keys. |
| **Max Password Age** | 90 Days | Limits the "shelf-life" of potentially compromised credentials. |

## 🖼️ Technical Evidence & Verification
To ensure the security posture was successfully updated without interactive login (RDP), I utilized the Azure Run Command interface to push the remediation script directly to the VM instance.

### **Step 1: Baseline Vulnerability Assessment**
Before applying NIST controls, an authenticated **Nessus** scan was performed to identify existing security gaps. The scan identified multiple **High-Risk** vulnerabilities related to software lifecycle management (Docker Desktop).

<img width="1278" height="691" alt="Baseline Scan" src="https://github.com/user-attachments/assets/efbe8079-5831-44f9-8c83-fa809a531351" />
*Figure 1: Initial Nessus scan identifying high-risk vulnerabilities and configuration gaps.*

### **Step 2: Remote Policy Deployment**
The following image demonstrates the execution of the PowerShell remediation script within the Azure console. By using `gpupdate /force`, I ensured the NIST 800-53 requirements were applied immediately to the system registry.

<img width="1266" height="720" alt="Azure Run Command" src="https://github.com/user-attachments/assets/549e9c88-4254-4621-9896-a58e8bc6d3f0" />
*Figure 2: Executing NIST-compliant password policies and software updates via the Azure backbone.*

### **Step 3: Post-Remediation Audit**
Validation is a critical SOC function. I performed a follow-up credentialed scan to verify that all High-Severity risks were mitigated.

<img width="1275" height="678" alt="Final Scan" src="https://github.com/user-attachments/assets/f3556483-b43d-44ed-a884-b3467cf6fb38" />
*Figure 3: Final Nessus dashboard confirming 100% remediation of High-Severity risks.*

> **Note on Environmental Risk Acceptance:** A "Medium" finding remains on the final graph (Plugin #51192). This finding remains because the lab environment uses internal encryption that hasn't been signed by a public third party. In a production SOC environment, this is documented as an accepted risk for internal-only laboratory traffic.

## 💻 PowerShell Remediation Script
```powershell
# Enforce NIST 800-53 Password Policies via Net Accounts
net accounts /minpwlen:12
net accounts /uniquepw:5
net accounts /maxpwage:90

# Force policy update and verify configuration
gpupdate /force
net accounts
```
---
---

## 🔍 The SOC "Fraud Hunter" Perspective
In a **Security Operations Center (SOC)** environment, identifying the "Fraud Hunter" mindset means recognizing that "bad actors" prioritize the path of least resistance. This perspective is essential for proactive defense, incident detection, and high-level security analysis.

### **Technical Hardening & Access Control**
* **Mitigating Credential Stuffing:** Moving to a **12-character minimum** exponentially increases the cost and time required for an attacker to crack passwords via automated tools or brute-force, significantly reducing the risk of unauthorized access.
* **Preventing Persistence:** Enforcing **password history** ensures that even if a credential is leaked, a simple reset to a previous known password will not allow a threat actor back into the system—a critical step for successful incident remediation.

### **💡 Key SOC Technical Wins**
* **Vulnerability Management Lifecycle:** Successfully identified **CVE-2025-13743** and **CVE-2026-2664**, mapped them to software lifecycle gaps, and verified remediation through authenticated scanning.
* **Remote Remediation:** Developed the ability to manage and harden assets when traditional tools (RDP/Admin Center) are blocked—a vital skill for **SOC Analysts** and **Incident Responders** during active containment.
* **Compliance Mapping:** Successfully mapped **NIST 800-53** controls to a live, production-style cloud environment, ensuring defense-in-depth aligns with industry frameworks.
* **Technical Troubleshooting:** Overcame command syntax errors (e.g., `/UNIQUEPW` vs. `/UNIQUENESS`) to ensure successful policy deployment across the cloud architecture.

## 🚀 Scalability & Future Improvements
In a production enterprise environment, maintaining a "Fraud Hunter" defense requires shifting from manual remediation to automated, continuous compliance.

### **1. Policy-as-Code (PaC) & Automation**
* **Transition to Azure Policy:** Moving beyond manual execution to implementing **Azure Policy Guest Configuration**. This allows for "Deploy if not exists" logic, ensuring any new VM is automatically hardened to NIST 800-53 standards upon creation.
* **Desired State Configuration (DSC):** Utilizing **PowerShell DSC** to enforce the "Net Accounts" state. If a local admin attempts to revert settings, DSC will automatically detect the drift and "re-apply" the 12-character minimum.

### **2. Global Infrastructure Deployment**
* **Group Policy Objects (GPO):** For domain-joined assets, these NIST controls would be centralized via **Active Directory GPOs** to ensure a uniform security baseline across all organizational units (OUs).
* **Infrastructure as Code (IaC):** Integrating these hardening scripts into **Terraform** or **Bicep** templates to eliminate the "window of vulnerability" between VM creation and first-run hardening.

### **3. Advanced Monitoring & SIEM Integration**
* **Log Forwarding:** Configuring Windows Event Logs to forward **Event ID 4739** (Domain Policy Change) to **Microsoft Sentinel**.
* **Alert Logic:** Creating a **Kusto Query Language (KQL)** alert to trigger an investigation if a "Net Accounts" change is detected outside of a documented maintenance window, signaling a potential attempt to weaken system defenses.

## 🛡️ Verification & Audit
Successfully verified through the **Azure Portal** output console and **Nessus** authenticated scans, confirming all `Net Accounts` parameters align with **NIST 800-53 IA-5** standards for secure identification and authentication.
