1. Reconnaissance & Enumeration
--

### Network Scanning
I started by scanning the target IP `10.48.186.153` to see which ports were open and what services were running.

bash
--
nmap 10.48.186.153
What I found:

Port 21 (FTP): Running vsFTPd 3.0.2

Port 22 (SSH): OpenSSH

Port 80 (HTTP): Apache Web Server

Port 111 (rpcbind)

Web Directory Discovery
I visited the web server and saw an "Arrowverse" themed page. To find hidden files, I ran gobuster to brute-force the directories.

Bash
--
gobuster dir -u [http://10.48.186.153/island](http://10.48.186.153/island) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
My findings:

Found a directory at /island.

After a recursive scan, I discovered another path at /island/2100.

Hunting for Hints
I inspected the source code of the pages I found:

On the /island page, I found a hidden string: vigilante.

In the /2100 directory, I found a comment hinting at a .ticket file.

I navigated to http://10.48.186.153/island/2100/green_arrow.ticket and found a Base58 encoded string: RTy8yhBQdscX.

2. Initial Access & Data Extraction
--
Decrypting the Token
I took the string RTy8yhBQdscX to CyberChef and decoded it from Base58.

Resulting Password: !#th3h00d

FTP Exploitation
With the username vigilante and the password !#th3h00d, I logged into the FTP server.

Bash
--
ftp 10.48.186.153
Inside, I found and downloaded a zip file named ss.zip. I extracted it and found two files that were very useful:

passwd.txt

shado (This contained the password: M3tahuman)

3. System Access & User Flag
--
SSH Login
I used the password from the shado file to log in as the user slade via SSH.

Bash
--
ssh slade@10.48.186.153
# Password: M3tahuman
Capturing the User Flag
Once I was in the system, I checked the home directory and grabbed the first flag.

Bash
--
slade@LianYu:~$ cat user.txt
# THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
4. Privilege Escalation to Root
Sudo Rights Enumeration
I checked my current user's sudo privileges to see how I could get root access.

Bash
--
slade@LianYu:~$ sudo -l
The Vulnerability: I saw that slade can run /usr/bin/pkexec as root with NOPASSWD.

Exploitation
I used the GTFOBins method for pkexec to break out of the restricted shell and spawn a root shell.

Bash
--
sudo /usr/bin/pkexec /bin/sh
Final Root Flag
I confirmed I was root and then read the final flag to finish the machine.


Bash
--
# whoami
root
# cat /root/root.txt
