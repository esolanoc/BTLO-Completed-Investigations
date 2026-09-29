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

The investigation starts using splunk server, we need to make sure what are our source types that are going to provide us with the data that we are going to analyze

<img width="1365" height="738" alt="image" src="https://github.com/user-attachments/assets/bd32699d-3ed9-4300-bfa3-28761faa9ce3" />

Once we confirmed the sourcetype, we can start or searching. I added S3 bucket to the search and I immediately see a few amount of events.

<img width="1352" height="730" alt="image" src="https://github.com/user-attachments/assets/d5257bc8-46c3-4403-937d-0e2d3205dfe4" />

---

# Question #1 Which S3 bucket's object was accessed by the attacker?

---
I went through all interesting fields and notice “eventName” this contains two events that called my attention “GetObject” and ListObject’ this means the attacker performed some interaction by listing or downloading a specific object withing the AWS cloud


Then if we check for  requestParameters.bucketName we can identify the name

<img width="1344" height="397" alt="image" src="https://github.com/user-attachments/assets/67c65e90-4399-4ffd-bebf-e968fe75e1fd" />

---

<details>
<summary>Answer</summary>

# ✅ developers-configuration
</details>

---

# Question #2 What was the attacker IP associated in S3 access? [Defanged IP]

---

With our last searched, we saw two potential IP’s , if we filter by userIdentity we can see that there is one IP that doesn’t belong to an user, in this case the malicious actor

<img width="1339" height="506" alt="image" src="https://github.com/user-attachments/assets/d077183a-6a3d-40e5-94d6-549daf245ab0" />

---

<details>
<summary>Answer</summary>

# ✅ 18[.]216[.]138[.]52  
</details>

---

# Question #3 What object/file did the attacker then access/download that would allow access to their environment?
---


For this I selected the bucketname and the Paremeterkey which is the object that the attacker tried to read 

<img width="1361" height="464" alt="image" src="https://github.com/user-attachments/assets/2ba5c3a4-5e4f-4c96-8191-cbf133f44956" />

---

<details>
<summary>Answer</summary>

# ✅ VPN-Profiles/DevelopersProfile_wg0.conf   
</details>

---

# Question #4 Based on the previous question, what is the software associated with the file?

---
Here what I did was a little of google search to know if there is a specific software

<img width="749" height="755" alt="image" src="https://github.com/user-attachments/assets/58e04993-6e6a-4da6-8f2e-0276541e63c6" />

---

<details>
<summary>Answer</summary>

# ✅ wireguard   
</details>

---

# Question #5 Using the previously mentioned file, one of the attackers accidentally connected via main system leading to his IP address getting leaked. What is the IP address of the Attacker? [Defanged IP]

---

I had to research about wireguard and checking the config file I saw the file make connections to a specific port 51820. I used the sourcetyoe VPC flow to check for network connections and I was able to identified the IP that make connections to that port

<img width="1258" height="399" alt="image" src="https://github.com/user-attachments/assets/9c678029-9140-44fb-aa3d-727f064240cd" />

---

<details>
<summary>Answer</summary>

# ✅ 122[.]161[.]49[.]105 
</details>   

---

# Question #6 What was the Private IP of the EC2 instance to which the attacker connected?

---
Here we can use the same search and just add destination address to know here the IP is connecting to

<img width="1298" height="391" alt="image" src="https://github.com/user-attachments/assets/66419dd6-7d2b-4fef-b139-8f44e08b2cec" />

---

<details>
<summary>Answer</summary>

# ✅ 10.0.1.125
</details>      

---

# Question #7 The attacker performed further enumeration while being inside the EC2 instance and found a role that could be used further by assuming it. What was the ARN of the role?

---

I searched CloudTrail for the role activity, to get a good view of the roles the attacker was working with.

<img width="1353" height="452" alt="image" src="https://github.com/user-attachments/assets/a8aabac6-ddfc-441c-83e0-da1a2ae09e6d" />

---

<details>
<summary>Answer</summary>

# ✅ arn:aws:iam::764581110688:role/ec2-role  
</details>      

---

# Question #8 Using the role Attacker targeted IAM to achieve persistence in the environment. Provide the APIs used for it in order of its usage.

---

This was got me thinking I little, so I had to research about API persistence technicuqes and I learned that you should look for events related to the creation or modification of credentials, roles, users, and storage configurations. So I identified these and I used the following filter to find those API’s, once found I sort by event time to know the order of how this was applied

<img width="1358" height="628" alt="image" src="https://github.com/user-attachments/assets/df594b79-4e5e-4647-a1c8-16cf27b0d65c" />

---

<details>
<summary>Answer</summary>

# ✅ CreateUser, CreateAccessKey, AttachUserPolicy
</details>      
   

---

# Question #9 Provide the name of the IAM Identity created during Persistence.

---
Using the sa,e exatc search before and cheking at the raw data and going to the CreateUser AP, we can find the user name that was created

<img width="942" height="475" alt="image" src="https://github.com/user-attachments/assets/9e7e3349-4f9b-4285-8947-b88a7a6ca3bc" />

---

<details>
<summary>Answer</summary>

# ✅  web_engg_2  
</details>  
 
---

# Question #10 What policy was attached to the Identity later? Provide the policy ARN.

---
Here I used the same filter before but I modify th eeventName field to show policy API, then I search for policy to get the name

<img width="1336" height="413" alt="image" src="https://github.com/user-attachments/assets/2fd29501-bb60-46e4-a8d9-de5ec949194a" />

---

<details>
<summary>Answer</summary>

# ✅  arn:aws:iam::aws:policy/AdministratorAccess 
</details>  


---

# Question #11 Another EC2 instance was found to be accessed by the Attacker using SSH. Find its Private IP Address.

---

I know that SSH runs ono port 22, so I used VPC sourcetype and filter by port 22 then I count by srcaddr and dstaddr to see what privtae IP was connecting to port 22

<img width="1363" height="642" alt="image" src="https://github.com/user-attachments/assets/5a168c06-2929-4ac2-9889-5c4bd4b61098" />

---

<details>
<summary>Answer</summary>

# ✅  10.0.2.32  
</details>  

---

# Question #12 From the above EC2 instance, attacker then created a reverse shell. Find the Reverse Shell IP & port. [Defanged IP]

---
I used the above isntance to filter by that srcaddr, I found results for port 443, 80, 123 that doesn’t have to o with reverse shell, so only two records were found

<img width="1355" height="621" alt="image" src="https://github.com/user-attachments/assets/4d2249d7-093b-4922-84c0-58f95076bb40" />

---

<details>
<summary>Answer</summary>

# ✅  3[.]15[.]209[.]50, 13337
</details>  (Attacker IP, Port)  

---
