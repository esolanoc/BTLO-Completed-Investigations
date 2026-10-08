# Summary
Zeta-9 Corporation operates a hybrid cloud infrastructure supporting its Quantum Research Division, where highly sensitive data is stored across secure cloud environments. Authorized personnel access this data through a web-based research portal that serves as the primary interface for ongoing projects. Following the breach, C.R.I.S.I.S must extend their investigation into Zeta-9’s cloud environment — analyzing activity, identifying signs of lateral movement, and determining whether the threat actor has already pivoted into the cloud infrastructure. Time is critical; stopping them before they move deeper into the network may be the only way to contain the damage.


---


# Question # 1: Analyze the AWS CloudTrail logs and identify the attacker's IP address. Note that legitimate users were working remotely from India (6 points)

---

In this first question we are going to simply filter by sourcetype=cloudtrail and search for the sourceIPAddress field. In this specific case we can only see one IP which is the potential malicious IP.

<img width="1916" height="436" alt="imagen" src="https://github.com/user-attachments/assets/be955467-2b34-4904-ae2d-fcbbe64ebec1" />

---
<details>
<summary>Answer</summary>

# ✅ 172.235.129.221
</details>

---

# Question # 2: The attacker performed reconnaissance on EC2 instances. What specific API call/EventName was generated during this reconnaissance activity? (4 points)

---

Here we only need to  add  eventName field to the search in order to show the list of events made, we can clearly see a few but only one confirms the attacker did a recon.

<img width="1905" height="466" alt="imagen" src="https://github.com/user-attachments/assets/a84d3e83-6524-4ffa-ae1d-894ebaf6cd63" />

---

<details>
<summary>Answer</summary>

# ✅ DescribeInstances
</details>

---

# Question # 3: After gathering information about EC2 instances, the attacker attempted to find instance passwords to establish connections. Identify the secretID that contained the Windows instance password (6 points)

---

Keep your search to the eventame field, what do you think sounds like a password…? Yes! The eventName” GetSecretValue”  probably contains the secure information we need. If we search the paremeters in the raw data we can see the secret ID.

<img width="1893" height="773" alt="imagen" src="https://github.com/user-attachments/assets/1061aaef-deee-4f43-997d-3c950ff38d70" />

---

<details>
<summary>Answer</summary>

# ✅ zeta9/windows/admin-password
</details>

---

# Question # 4: The attacker discovered and targeted S3 buckets to download sensitive data. Find how many unique S3 buckets were targeted as well as total files were downloaded from all buckets. (6 points)

---

With the eventName “getObject” we can see the request parameters made by the attacker and if we filter by bucketname and key we can find the 3 S3 buckets found and the 5 files.

<img width="1882" height="605" alt="imagen" src="https://github.com/user-attachments/assets/d08b4101-57b2-44bb-a0f8-307e293c061f" />

---

<details>
<summary>Answer</summary>

# ✅ 3, 5
</details>

---

# Question # 5: Through analysis of the compromised EC2 instance's browsing history, the attacker found traces leading to a secret web portal used by restricted individuals. Using cross-correlation with other log sources, identify the URL of this secret portal. (4 points)

---

Here we need to verify other sourcetype in order to find the portal, I tried using “AppServiceHTTPLogs “ which game me a field called Host, if we look at it we can confirm the name of the portal.

<img width="1908" height="529" alt="imagen" src="https://github.com/user-attachments/assets/715d54d6-c38b-4c87-8d1d-13413eb9a3b1" />

---

<details>
<summary>Answer</summary>

# ✅ zeta9-research-portal.azurewebsites.net
</details>

---

# Question # 6: The attacker pivoted to another cloud environment by exploiting a vulnerability. Provide the complete command used for this cross-cloud activity (6 points)

---

I had to review all the sourcetypes to find this one, during a while I was able to see under AppServiceHTTPLogs and filter by the attacker IP we found earlier and then I created a table to and sort by time to see the first cmd command the attacker did.

<img width="1896" height="664" alt="imagen" src="https://github.com/user-attachments/assets/e8561afe-c9c8-427a-958b-688072e72594" />


Using cyberchef we can decode the url to find the answer

<img width="1898" height="768" alt="imagen" src="https://github.com/user-attachments/assets/26bcf617-c603-4115-90ba-0c1a5737efc7" />

---

<details>
<summary>Answer</summary>

# ✅ curl -H secret:4ebc6d54-f421-4321-81c4-fd9e29d28a0f 'http://169.254.130.3:8081/msi/token?api-version=2017-09-01&resource=https://management.azure.com/'
</details>

---

# Question # 7: After gaining access to the Azure environment, the attacker was able to list and access data from cloud storage services. Identify the name of the specific storage blob container that was targeted. (6 points)

---
In this question we are going to focus our search in StorageBlobLogs, after the attacker access Azure Storage Blob from cloud storage service of Zeta 9 research. the first event is to ListContainers which  returs sa list of the containers under the specified storage account.

<img width="979" height="409" alt="imagen" src="https://github.com/user-attachments/assets/68647e65-3100-4769-96b9-5a14de527dfd" />

---

<details>
<summary>Answer</summary>

# ✅ quantum-research-secrets
</details>

---


# Question # 8: Determine how many files the attacker successfully downloaded from the Azure blob storage during the attack. (6 points)

---

If we keep our search in thye StorageBlob sourcetype, and filter by Operationname we can see the Blobs the attacker got 

<img width="1884" height="439" alt="imagen" src="https://github.com/user-attachments/assets/0878d9e0-b151-461d-88f4-76111b42dd38" />

---

<details>
<summary>Answer</summary>

# ✅ 6
</details>

---


# Question # 9: To gain access to the secret division systems, the attacker defaced the organization's website by deploying malicious content. Provide the URL that hosted the defaced website code. (6 points)

---

We can go back to the AppServiceLogs and filter again by our IP, then if we search trhough all fields we can see CsUriQuery filter, here the attacker changed the index.com with this other information

<img width="1466" height="436" alt="imagen" src="https://github.com/user-attachments/assets/407d0cd6-c4e7-4077-b815-8ca3a3524f6a" />

I tried searching the website but it was already defaced 

<img width="1295" height="712" alt="imagen" src="https://github.com/user-attachments/assets/45caa230-064c-404f-8c6c-d2516ddede9c" />

---

<details>
<summary>Answer</summary>

# ✅ https://pastebin.com/raw/sBEs83q3
</details>

---
