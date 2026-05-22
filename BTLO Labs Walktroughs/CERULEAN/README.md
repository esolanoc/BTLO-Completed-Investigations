# 🛡️Suspicious RDP Connections to the Production Department

## 🚨Alert
### In this scenario, the SIEM alerted the Production Department about suspicious RDP connections, specifically to the computer of user Jane (the department head). It was later determined that the attacks were malicious.
### 🕵️We will analyze the triage artifacts on Jane's computer and investigate the RDP connections to detect a possible data exfiltration.

Here, we are provided with the investigation files. We can see that the image of Jane's computer was extracted using the KAPE tool, and a preliminary report was generated with the Magnet AXIOM Examiner tool(a comprehensive digital forensics platform designed to acquire, analyze, and report electronic evidence from computers, mobile devices, and cloud services).

<img width="909" height="506" alt="imagen" src="https://github.com/user-attachments/assets/956845e3-8e3c-4c8b-8dff-cacb2b6e29ab" />

## ✅Question # 1: When did Jane receive the malicious mail from an attacker pretending to be from IT Support? Check the web history to help us better timeline the series of events. (Format: YYYY-MM-DD HH:MM:SS UTC) 

### 🕵️Jane apparently received a malicious email from an attacker impersonating an IT support staff member. Let's use Magnet AXIOM Examiner.

After analyzing the information and focusing on the browser history, we found that Jane did indeed receive the malicious email. However, the information is unclear, and we're not certain that this was the email where it all started.

<img width="975" height="487" alt="imagen" src="https://github.com/user-attachments/assets/1eb5804b-1cb3-4526-8b40-2f21c7ca1ddf" />


