# Scenario
Due to a sudden boom in Cloud services, a recently formed Australian-based company shifted from On-Prem to Cloud infrastructure. While moving to the Cloud, they unknowingly had a few misconfigurations, which a group of hackers leveraged to attack them. The attackers were successful in attacking and gaining access to their infrastructure. The company does not have an IR team ready to perform cloud-based investigations. They also said they manage their Cloud from Browser only, no CLI was configured.  

The investigation starts using Splunk server. We need to make sure what are our source types that are going to provide us with the data that we are going to analyze. Once we confirmed the sourcetype, we can start searching.  

---

# Executive Summary:

An Australian company that recently migrated to Cloud infrastructure suffered an attack due to **critical misconfigurations** in its services.  
Attackers exploited insecure access and successfully compromised multiple AWS resources.  
The investigation was conducted using **Splunk** as the analysis platform, leveraging **CloudTrail** and **VPC Flow Logs**.

---

# Investigation Walkthrough:

Initial Access:
- Attacker accessed the bucket **developers-configuration**.  
- Malicious IP identified: **18[.]216[.]138[.]52**.  

Credential Exposure:
- Object downloaded: **VPN-Profiles/DevelopersProfile_wg0.conf**.  
- Associated software: **WireGuard**.  
- Attacker leaked his own IP when connecting: **122[.]161[.]49[.]105**.  

Compromised EC2 Instances:
- Initial connection to private instance: **10.0.1.125**.  
- IAM role enumeration → **arn:aws:iam::764581110688:role/ec2-role**.  
- Persistence achieved via APIs: **CreateUser → CreateAccessKey → AttachUserPolicy**.  
- Identity created: **web_engg_2**.  
- Policy attached: *(Policy ARN identified in logs)*.  
- Additional SSH access to private instance: **10.0.2.32**.  
- Reverse shell detected to attacker on suspicious port.  

---

# Impact
- **Data Exfiltration**: Sensitive objects downloaded from S3.  
- **Persistence**: Creation of IAM users and keys with attached policies.  
- **Privilege Escalation**: Use of IAM roles to expand access.  
- **Infrastructure Compromise**: Multiple EC2 instances accessed and reverse shell established.  

---

# Recommendations
- Implement **Cloud Security Posture Management (CSPM)** to detect misconfigurations.  
- Enable **MFA** and enforce periodic IAM key rotation.  
- Configure **AWS Config + GuardDuty** for real-time alerts.  
- Restrict S3 bucket access using **least privilege policies**.  
- Establish a **Cloud Incident Response Playbook**.  

---

#  IOCs (Indicators of Compromise)

| Category        | Indicator                                      | Notes                                      |
|-----------------|-----------------------------------------------|--------------------------------------------|
| S3 Bucket       | developers-configuration                      | Bucket accessed by attacker                 |
| Attacker IP     | 18[.]216[.]138[.]52                           | IP associated with S3 access                |
| Object/File     | VPN-Profiles/DevelopersProfile_wg0.conf       | File downloaded from S3                     |
| Software        | WireGuard                                     | Software linked to configuration file       |
| Attacker IP     | 122[.]161[.]49[.]105                          | IP leaked via WireGuard connection          |
| EC2 Instance    | 10.0.1.125                                    | Compromised private instance                |
| IAM Role ARN    | arn:aws:iam::764581110688:role/ec2-role       | Role enumerated by attacker                 |
| IAM APIs        | CreateUser, CreateAccessKey, AttachUserPolicy | APIs used for persistence                   |
| IAM Identity    | web_engg_2                                    | IAM user created                            |
| Policy ARN      | (Identified in logs)                          | Policy attached to IAM user                 |
| EC2 Instance    | 10.0.2.32                                     | Instance accessed via SSH                   |
| Reverse Shell   | (Attacker IP, Port)                           | Reverse shell connection detected           |


---

# Question #1 Which S3 bucket's object was accessed by the attacker?
  
<details>
<summary>Answer</summary>

# ✅ developers-configuration
</details>
---

### Question #2  
**What was the attacker IP associated in S3 access? [Defanged IP]**  
**Answer:** 18[.]216[.]138[.]52  

---

### Question #3  
**What object/file did the attacker then access/download that would allow access to their environment?**  
**Answer:** VPN-Profiles/DevelopersProfile_wg0.conf  

---

### Question #4  
**Based on the previous question, what is the software associated with the file?**  
**Answer:** wireguard  

---

### Question #5  
**Using the previously mentioned file, one of the attackers accidentally connected via main system leading to his IP address getting leaked. What is the IP address of the Attacker? [Defanged IP]**  
**Answer:** 122[.]161[.]49[.]105  

---

### Question #6  
**What was the Private IP of the EC2 instance to which the attacker connected?**  
**Answer:** 10.0.1.125  

---

### Question #7  
**The attacker performed further enumeration while being inside the EC2 instance and found a role that could be used further by assuming it. What was the ARN of the role?**  
**Answer:** arn:aws:iam::764581110688:role/ec2-role  

---

### Question #8  
**Using the role Attacker targeted IAM to achieve persistence in the environment. Provide the APIs used for it in order of its usage.**  
**Answer:** CreateUser, CreateAccessKey, AttachUserPolicy  

---

### Question #9  
**Provide the name of the IAM Identity created during Persistence.**  
**Answer:** web_engg_2  

---

### Question #10  
**What policy was attached to the Identity later? Provide the policy ARN.**  
**Answer:** (Identificado en logs)  

---

### Question #11  
**Another EC2 instance was found to be accessed by the Attacker using SSH. Find its Private IP Address.**  
**Answer:** 10.0.2.32  

---

### Question #12  
**From the above EC2 instance, attacker then created a reverse shell. Find the Reverse Shell IP & port. [Defanged IP]**  
**Answer:** (Attacker IP, Port)  

---
