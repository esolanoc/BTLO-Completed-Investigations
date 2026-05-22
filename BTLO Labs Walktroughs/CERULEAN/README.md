# 🛡️Suspicious RDP Connections to the Production Department

## 🚨Alert
### In this scenario, the SIEM alerted the Production Department about suspicious RDP connections, specifically to the computer of user Jane (the department head). It was later determined that the attacks were malicious.
## 🕵️We will analyze the triage artifacts on Jane's computer and investigate the RDP connections to detect a possible data exfiltration.

Here, we are provided with the investigation files. We can see that the image of Jane's computer was extracted using the KAPE tool, and a preliminary report was generated with the Magnet AXIOM Examiner tool(a comprehensive digital forensics platform designed to acquire, analyze, and report electronic evidence from computers, mobile devices, and cloud services).

<img width="909" height="506" alt="imagen" src="https://github.com/user-attachments/assets/956845e3-8e3c-4c8b-8dff-cacb2b6e29ab" />

## 📌Question # 1: When did Jane receive the malicious mail from an attacker pretending to be from IT Support? Check the web history to help us better timeline the series of events. (Format: YYYY-MM-DD HH:MM:SS UTC) 

## 🕵️Jane apparently received a malicious email from an attacker impersonating an IT support staff member. Let's use Magnet AXIOM Examiner.

After analyzing the information and focusing on the browser history, we found that Jane did indeed receive the malicious email. However, the information is unclear, and we're not certain that this was the email where it all started.

<img width="975" height="487" alt="imagen" src="https://github.com/user-attachments/assets/1eb5804b-1cb3-4526-8b40-2f21c7ca1ddf" />

Let's analyze this record a little more in-depth using another forensic tool called Hindsight. We'll run the following command:

<pre>
C:\Users\BTLOTest\Desktop\Tools\Hindsight>hindsight.py -i "C:\Users\BTLOTest\Desktop\Investigation\Kape_Triage_Jane\C\Users\Jane\AppData\Local\Google\Chrome\User Data\Default" -o C:\Users\BTLOTest\Desktop\output
</pre>

<img width="975" height="399" alt="imagen" src="https://github.com/user-attachments/assets/b631db5b-279b-49b8-93f9-7668340283d5" />

We can see how Hindsight analyzed the browser history and web artifacts. Now, we can open the file we generated, named "output," with Timeline Explorer.

Let's filter by the URL we found earlier using Magnet AXIOM Examiner to confirm when Jame received the malicious email.

<img width="975" height="190" alt="imagen" src="https://github.com/user-attachments/assets/f64d0395-f8e0-4f03-b4ef-d8fe625368bd" />

## ✅ Answer: 2024-11-05 20:45:03 UTC

## 📌Question # 2: The threat actor immediately, after RDP’ing, tries to log into other storage-based resources. What is the one with the most traffic? (Format: Storage Name)

The attacker, immediately after connecting via RDP, attempted to log in to other storage-based resources. We see that the attacker used Google Drive.

<img width="975" height="524" alt="imagen" src="https://github.com/user-attachments/assets/de9f00e1-8b6a-4f2f-8c6f-05b1c7e9fcba" />

## ✅ Answer: Google Drive

## 📌Question # 3: It looks like the threat actor’s motive is data exfiltration via RDP. What ITM ID corresponds with this technique? (Format: XXXXX.XXX)

Based on our findings, the attacker appears to have used programs to store information, most likely exfiltrating data.
Reviewing the ITM (Insider Threat Matrix) to understand the attacker's actions, we can see the following: An individual uses a cloud storage service, such as Dropbox, OneDrive, or Google Drive, to extract data.

<img width="975" height="503" alt="imagen" src="https://github.com/user-attachments/assets/391938a1-af1a-475f-8dfc-a01a0d749f0a" />

## ✅ Answer: IF001.001

## 📌Question # 4: Let’s step back for a bit. Jane’s account mistakenly has admin rights. What role did they assign to her? (Format: Job Role(Title)) 

## 🕵️It appears that Jane was mistakenly granted administrator privileges. A search of Windows Account artifacts reveals that Jane's role is Database Specialist and she is part of the Administrators Group in Windows.

<img width="975" height="289" alt="imagen" src="https://github.com/user-attachments/assets/e70ede74-d716-4589-b450-0653b284ff22" />

## ✅ Answer: MSFT Admin (Database Specialist)

## 📌Question # 5: It seems like she downloaded Slack before the RDP session. Our main point of communication is Teams, so this is strange. What is the installation date and time of this software? (Format: YYYY-MM-DD HH:MM:SS UTC) 

## 🕵️Jane downloaded Slack, a channel-based messaging tool designed for work teams. This raised many suspicions since the organization uses Teams. The download of the application is shown here.

<img width="975" height="431" alt="imagen" src="https://github.com/user-attachments/assets/4bf15719-fb78-4699-908a-763971165361" />

## ✅ Answer: 2024-11-05 20:08:55 UTC

## 📌Question # 6: There is enough evidence of Slack being used on Jane’s machine. Can you provide the unofficial URL being utilized for communication? (Format: hxxps://url.tld)

 ## ✅ Answer: https://ceruleaninc.slack.com/

## 📌Question # 7: Provide the initial time and Origin IP Address for the RDP connections to Jane’s workstation. (Format: MM/D/YYYY H:MM:SSS XX UTC, XXX[.]XXX[.]XXX[.]XXX) 

## 🕵️With this information, we can now determine the exact time the RDP connection was initiated from Jnae's machine and the IP address used.

<img width="975" height="382" alt="imagen" src="https://github.com/user-attachments/assets/1a587a03-36d8-42e0-8bae-fd0eebda6a43" />

## ✅ Answer: 11/5/2024 8:58:38 PM UTC, 104[.]203[.]174[.]169	

## 📌Question # 8: Our Project Venus plans were leaked, triggering the defenses. What are the four documents in alphabetical order? (Hint: examine the Windows Defender Logs) (Tip: remove 'project venus' and 'cerulean' from the document names). (Format: Doc1, Doc2, Doc3, Doc4)

## 🕵️Finally, we can see that the attacker managed to extract the following files from the Windows Defender Logs.

<img width="975" height="508" alt="imagen" src="https://github.com/user-attachments/assets/dafd096f-3c17-44e7-acc6-4c63c0bf67b4" />

## ✅ Answer: Energy Storage, Research, Solar Panel Tech, Wind Turbine Design


