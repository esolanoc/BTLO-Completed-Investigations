## 🛡️ BTLO Name: LOG ANALYSIS - PRIVILEDGE ESCALATION

## Scenario

A server with sensitive data was accessed by an attacker and the files were posted on an underground forum. This data was only available to a privileged user, in this case the ‘root’ account. Responders say ‘www-data’ would be the logged in user if the server was remotely accessed, and this user doesn’t have access to the data. The developer stated that the server is hosting a PHP-based website and that proper filtering is in place to prevent php file uploads to gain malicious code execution. The bash history is provided to you but the recorded commands don’t appear to be related to the attack. Can you find what actually happened?

---

We are provided with a file bash history that we nee to analyze. Using cat we can read  the content of the file.

<img width="631" height="618" alt="image" src="https://github.com/user-attachments/assets/26572f26-4189-474c-9892-b1a51eedc017" />

---

## 📌Question #1: What user (other than ‘root’) is present on the server

Checking the file we can clearly see CLI commands that were executed during the attack some of them are the ones used when a privileged escalation happens, the malicious actor normally uses “whoami or pwd” to know where is he located withing the system. 

<img width="649" height="391" alt="image" src="https://github.com/user-attachments/assets/108b94ae-975f-47f8-9122-d9256c907632" />

## ✅ Answer:  daniel

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
