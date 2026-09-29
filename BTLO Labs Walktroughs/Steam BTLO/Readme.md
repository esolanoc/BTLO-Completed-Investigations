# Scenario

G'day Defenders, looks like one of our new content developers is having some trouble. He suspects one of his dogs is playing games on his computer. He doesn't have time to look at this though so maybe you can handle this and answer the following questions.

Our content developer doesn't know how to start this forensic investigation but he said that he would just “Google it” when he got some time.

He also didn't install Steam in the standard location! 

# Executive Summary:

During a forensic review of a developer’s workstation, evidence revealed unauthorized installation and use of Steam games.
Analysis of VM artifacts showed that the account doglyfie was automatically logging into Steam, with games such as The Button and Quaver installed.
Additional findings included Steam ID, persona details, avatar hash, and achievement logs, confirming a policy violation involving personal gaming activity on a corporate system.

# Impact:
Policy Violation: Unauthorized use of corporate resources for gaming.

Risk of Data Exposure: Steam client and external connections could introduce vulnerabilities.

Reputation Risk: Misuse of company assets by employees.

# Root Cause:
Lack of application whitelisting and monitoring allowed Steam installation outside standard directories.

Insufficient endpoint controls failed to detect unauthorized software.

User negligence in separating personal and corporate activities.

# Investigation Walkthrough:

Games Installed: The Button, Quaver

Steam Auto-Login User: doglyfie

Steam Tag (Psychological Horror): 1721

Avatar Hash & Game: 0fa9bd3268de3aa948b9f7a63088afb6, The Button

Steam ID: 76561199466436896

Persona & OSINT Info: moxielysi, Canberra, Australian Capital Territory, AU, puppacup

Previous Account Name: desi

Recent Achievement: What's stopping you?, yes

Anti-Forensics Activity: /home/desi/snap/steam/common/.local/share/Steam/steamapps/common, DELETE FILES

# Indicators of Compromise (IOCs):


| Category        | Indicator                          | Notes                                      |
|-----------------|------------------------------------|--------------------------------------------|
| Games           | The Button, Quaver                 | Unauthorized installations                 |
| Username        | doglyfie                           | Auto-login Steam account                   |
| Steam Tag       | 1721                               | Psychological Horror category              |
| Avatar Hash     | 0fa9bd3268de3aa948b9f7a63088afb6   | Linked to The Button                       |
| Steam ID        | 76561199466436896                  | Unique account identifier                  |
| Persona Info    | moxielysi, Canberra, ACT, AU, puppacup | OSINT profile details                  |
| Previous Name   | desi                               | Historical account name                    |
| Achievement     | What's stopping you?, yes          | Most recent achievement                    |
| Anti-Forensics  | /home/desi/.../steamapps/common, DELETE FILES | Attempt to hide

We will analyze the triage artifacts from the internal user and investigate the different VM files images to detect the Policy Violation

In this case we are provided with the Steam evidence folder. We can see a few images from a virtual machine. We need to investigate them until we can find which one has the data we need to analyze.

<img width="901" height="507" alt="image" src="https://github.com/user-attachments/assets/3b2e56ce-5800-4d61-9ac0-c4e5cbae1a7a" />

---

# 📌Question # 1: What are the two games installed by the user? [List in alphabetical order] (Format: AGame, BGame)

 It looks like the user installed two games. Once we identify the correct vmk file, we can now go to each of the folders and display the data it contains.
After some time digging in folders and files, I found under the following path two files under application which we can dertemine the two games installed

/home/desi/snap/steam/common/.local/share/applications

---

<details>
<summary>Answer</summary>

# ✅ The Button, Quaver
</details>

---

# 📌 Questions # 2: What is the username that automatically gets logged in when steam opens? (Format: Username)

Let’s search for more!, under the same path, I found a folder called “steam” which contains a subfolder “config” , this is very interesting to analyze since we can find important information. During the analysis I noticed a file called loginusers.vdf that is commonly used by the Valve Steam gaming platform.

<img width="975" height="720" alt="image" src="https://github.com/user-attachments/assets/697d4285-052b-4c4d-92d7-2232719b27e9" />

---

<details>
<summary>Answer</summary>

# ✅ doglyfie
</details>

---

# 📌 Queston # 3: This is becoming a bit of a 'psychological horror' knowing that a dog is potentially playing games. How would Valve tag this? (Format: XXXX) (4 points)

Steam tags are normally stored under “appcache” subfolder, we can find the tag value for “psychological horror”
 
<img width="975" height="669" alt="image" src="https://github.com/user-attachments/assets/8d481dae-00c7-41e0-89fd-ac93dfb2a719" />

---

<details>
<summary>Answer</summary>

# ✅ 1721
</details>

---

# 📌 Queston # 4: What is the md5 hash of the avatar set for the user and what game is it from? (Format: MD5, game

After analyze the loginusers.vdf file, I found a folder named “avatarcache” located in the following path

<pre>
 /home/desi/snap/steam/common/.local/share/Steam/config/
</pre>

<img width="1074" height="826" alt="image" src="https://github.com/user-attachments/assets/3d5c4c91-87f4-45e2-9932-652f6af77d63" />

Note: Export the png hash file list to obtain the hash

---

<details>
<summary>Answer</summary>

# ✅ 0fa9bd3268de3aa948b9f7a63088afb6, The Button
</details>

---

# 📌 Question #5: What is the Steam ID for the account? (Format: ID) (2 points)

As mentioned in Question 2, the same file used there can also be used to answer this question.

<img width="975" height="720" alt="image" src="https://github.com/user-attachments/assets/91fdce63-0727-4872-99c8-1dd0b7fa1d74" />

---

<details>
<summary>Answer</summary>

# ✅ 76561199466436896
</details>

---

#📌 Question #6:  What is the persona, city, state, country, and real name for the user? (Format: persona, city, state, country, real name) (8 points)

We will have to do some OSINT investigation here. With the previous Steam ID

Search it using a Steam-specific OSINT tool such as SteamIDFinder: https://steamid.xyz/1506171168

<img width="786" height="738" alt="image" src="https://github.com/user-attachments/assets/d1ac7ea3-d4b9-40d3-bea9-831b9a504bce" />

---

<details>
<summary>Answer</summary>

# ✅ moxielysi, Canberra, Australian Capital Territory, AU, puppacup
</details>

---

# 📌 Question #7: What is the previous name of the account? (Format: Name) (4 points)

The specific Steam profile had only one user, I  matched the name found in the directory path (/home/desi). 

---

<details>
<summary>Answer</summary>

# ✅ desi
</details>

---

# 📌 Question #8: What is the name of the most recent achievement and how is it achieved in the same game from Q4? (Format: AchievementName, yes/no) (10 points)

Steam achievements are usually stored under the userdata’s config directory 

<pre>
/home/desi/snap/steam/common/.local/share/Steam/userdata/1506171168/config/librarycache/
</pre>

We need to investigate all of these json files, and find Achieved: true entries. 

<img width="1015" height="828" alt="image" src="https://github.com/user-attachments/assets/aed9f177-0962-4b35-97ca-e88d09cc57c6" />

---

<details>
<summary>Answer</summary>

# ✅ What's stopping you?, yes
</details>

---

## 📌 Question #9) It looks like the dog tried to hide what she was playing. Which directory did she perform anti-forensics on and what action did she do (select from MOVE|DELETE|COPY OVER|ADD FILES)? (Format: /full/directory/path, ACTION) (10 points)

We need to dig more through the folders and directories.

After a while I was able to findthe following path

<img width="858" height="832" alt="image" src="https://github.com/user-attachments/assets/7ec71a5a-ebc7-4725-b8e6-c956c85bad48" />

---

<details>
<summary>Answer</summary>

# ✅ /home/desi/snap/steam/common/.local/share/Steam/steamapps/common, DELETE FILES
</details>





