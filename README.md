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

### **Step 1: Remote Policy Deployment**
The following image demonstrates the execution of the PowerShell remediation script within the Azure console. By using `gpupdate /force`, I ensured the NIST 800-53 requirements were applied immediately to the system registry.

<img width="1273" height="678" alt="New Rules" src="https://github.com/user-attachments/assets/b7fe90e7-024c-43b4-bbdc-44a358344af1" />

*Figure 1: Executing NIST-compliant password policies via the Azure backbone.*

### **Step 2: Post-Remediation Audit**
Validation is a critical SOC function. The output below confirms that the Windows Server instance now aligns with **IA-5** standards, specifically verifying the 12-character minimum and 90-day expiration cycle.

<img width="1162" height="438" alt="Sucess" src="https://github.com/user-attachments/assets/e2a519f8-18be-49c4-a6c2-5d66e827c19d" />

*Figure 2: Successful audit of Net Accounts parameters showing 100% compliance.*

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
## 🔍 The SOC "Fraud Hunter" Perspective
In a **Security Operations Center (SOC)** environment, identifying the "Fraud Hunter" mindset means recognizing that "bad actors" prioritize the path of least resistance. This perspective is essential for proactive defense, incident detection, and high-level security analysis.

### **Technical Hardening & Access Control**
* **Mitigating Credential Stuffing:** Moving to a **12-character minimum** exponentially increases the cost and time required for an attacker to crack passwords via automated tools or brute-force, reducing the risk of unauthorized access.
* **Preventing Persistence:** Enforcing **password history** ensures that even if a credential is leaked, a simple reset to a previous known password will not allow a threat actor back into the system, a critical step for successful incident remediation.

### **💡 Key SOC Technical Wins**
* **Remote Remediation:** Developed the ability to manage and harden assets when traditional tools are blocked—a vital skill for **SOC Analysts** and **Incident Responders** during active containment.
* **Compliance Mapping:** Successfully mapped **NIST 800-53** controls to a live, production-style cloud environment, ensuring defense-in-depth aligns with industry frameworks.
* **Technical Troubleshooting:** Overcame command syntax errors (e.g., `/UNIQUEPW` vs. `/UNIQUENESS`) to ensure successful policy deployment across the cloud architecture.

## 🚀 Scalability & Future Improvements

In a production enterprise environment, maintaining a "Fraud Hunter" defense requires shifting from manual remediation to automated, continuous compliance. To scale this project across thousands of nodes, I have identified the following strategic improvements:

### **1. Policy-as-Code (PaC) & Automation**
* **Transition to Azure Policy:** Moving beyond manual **Azure Run Command** execution to implementing **Azure Policy Guest Configuration**. This allows for "Audit if not exists" or "Deploy if not exists" logic, ensuring that any new VM spun up in the environment is automatically hardened to NIST 800-53 standards without manual intervention.
* **Desired State Configuration (DSC):** Utilizing **PowerShell DSC** to enforce the "Net Accounts" state. If a local admin or bad actor attempts to revert the password length to a weaker setting, DSC will automatically detect the drift and "re-apply" the 12-character minimum.

### **2. Global Infrastructure Deployment**
* **Group Policy Objects (GPO):** For domain-joined assets, these NIST controls would be centralized via **Active Directory GPOs**. This ensures inheritance across specific **Organizational Units (OUs)**, providing a uniform security baseline for all workstations and servers.
* **Infrastructure as Code (IaC):** Integrating these hardening scripts into **Terraform** or **Bicep** templates. By baking security into the deployment phase, we reduce the "window of vulnerability" between VM creation and first-run hardening.

###  **3. Advanced Monitoring & SIEM Integration**
* **Log Forwarding:** Configuring the Windows Event Log to forward **Event ID 4739** (Domain Policy Change) to **Microsoft Sentinel**. 
* **Alert Logic:** Creating a Kusto Query Language (KQL) alert to trigger an investigation if a "Net Accounts" change is detected outside of a documented maintenance window, signaling a potential attempt by a threat actor to weaken the system's defenses.

### **🛡️ Verification & Audit**
Successfully verified through the **Azure Portal** output console, confirming all `Net Accounts` parameters align with **NIST 800-53 IA-5** standards for secure identification and authentication.
