# Scenario

A server with sensitive data was accessed by an attacker and the files were posted on an underground forum. This data was only available to a privileged user, in this case the ‘root’ account. Responders say ‘www-data’ would be the logged in user if the server was remotely accessed, and this user doesn’t have access to the data. The developer stated that the server is hosting a PHP-based website and that proper filtering is in place to prevent php file uploads to gain malicious code execution. The bash history is provided to you but the recorded commands don’t appear to be related to the attack. Can you find what actually happened?

---

# Executive Summary:

Exfiltration data was performed on one of the servers, the server has a on PHP web site where developers secured it to prevent  php file uploads to gain malicious code execution, however when the bash was reviewed, they realized the attack was not to inject malicious code but a privilege escalation happened so the ser was able to get information about the server.

# Impact:
Unauthorized access to a server, leakage of confidential documents, privilege escalation.

# Root Caused: 
Wrong PHP file website configuration

# Recommendation:
Removed privilege access to other users

Review any other host to make sure the malicious actor did not move to another system 

Monitor processes for  evidence of persistence 

# Investigation Walkthrough:

Initial Access: Attacker leveraged file upload bypass using .phtml extension to gain code execution.

Privilege Escalation: Exploited misconfiguration in python binary with SUID bit to escalate to root.

Persistence: Commands in bash history show attempts to maintain access, but attacker later removed traces (deleted PHP shell).

Reconnaissance: Used whoami, pwd, and tcpdump to confirm privileges and analyze network traffic.

Suspicious User: Presence of non-root user daniel discovered in system.

Malicious Script: Attempted download of linux-exploit-suggester.sh into /tmp directory.

Data Exfiltration: Sensitive files accessed with root privileges and later posted on underground forums.

# Indicators of Compromise (IOCs):

| Category        | Indicator                   | Notes                                                   |
|-----------------|-----------------------------|---------------------------------------------------------|
| User Account    | daniel                      | Non-root user present on server                         |
| Script          | linux-exploit-suggester.sh  | Malicious script downloaded to /tmp                     |
| Tool            | tcpdump                     | Used for packet capture and network analysis            |
| File Extension  | .phtml                      | Used to bypass PHP upload filter                        |
| Binary Misconfig| python (SUID)               | Exploited to escalate privileges to root                |

---

We are provided with a file bash history that we nee to analyze. Using cat we can read  the content of the file.

<img width="631" height="618" alt="image" src="https://github.com/user-attachments/assets/26572f26-4189-474c-9892-b1a51eedc017" />

---

## 📌Question #1: What user (other than ‘root’) is present on the server

Checking the file we can clearly see CLI commands that were executed during the attack some of them are the ones used when a privileged escalation happens, the malicious actor normally uses “whoami or pwd” to know where is he located withing the system. 

<img width="649" height="391" alt="image" src="https://github.com/user-attachments/assets/108b94ae-975f-47f8-9122-d9256c907632" />

<details>
<summary>Answer</summary>

# ✅ daniel
</details>

---

## 📌Question #2: What script did the attacker try to download to the server?

We can notice a normal behavior when malicious actors move to the tmp folder to download the malicious script, this is one with the intention that after the computer is reboot or power off, the files in this folder will be deleted.

<img width="674" height="242" alt="image" src="https://github.com/user-attachments/assets/82d8a7a0-f166-42a8-96cb-f5529343716a" />

## ✅ Answer: linux-exploit-suggester.sh

---

## 📌Question #3: What packet analyzer tool did the attacker try to use?

We see tcpdump command, this can confirm the use of tcpdump to analyze network packets

<img width="490" height="274" alt="image" src="https://github.com/user-attachments/assets/42d26ba5-98aa-4d93-bd1c-447ddceb2eff" />

## ✅ Answer: tcpdump

---

## 📌Question #4: What file extension did the attacker use to bypass the file upload filter implemented by the developer?

The malicious actor ran a the following command to exploit a vulnerability, we can see the user at the end removed a .phtml file to eliminate the trace. This extensions are normally used by developers, using the same exact extension the filters doesn’t’ trigger any suspicious alert

<img width="561" height="199" alt="image" src="https://github.com/user-attachments/assets/eb2d2c27-a009-4044-8e6f-005d88a6d182" />

## ✅ Answer: .phtml

---

## 📌Question #5: Based on the commands run by the attacker before removing the php shell, what misconfiguration was exploited in the ‘python’ binary to gain root-level access? 1- Reverse Shell ; 2- File Upload ; 3- File Write ; 4- SUID ; 5- Library load

One of tge commands used searches for files owned by root with the SUID bit set. SUID binaries allow the process to run with the owner's privileges (in this case, root) when executed.

## ✅ Answer: 4
