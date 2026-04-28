Reconnaissance & Enumeration
--

### Network Scanning

I started by scanning the target IP `10.48.186.153` to see which ports were open and what services were running.

<img width="914" height="494" alt="Screenshot 2026-04-28 152709" src="https://github.com/user-attachments/assets/1cfa6ee1-a86f-46fe-9ecd-6469abebbbf0" />

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

<img width="1362" height="914" alt="Screenshot 2026-04-28 153858" src="https://github.com/user-attachments/assets/e70b7d7d-44c7-447c-a1a1-7a2cdf9cabab" />


In the /2100 directory, I found a comment hinting at a .ticket file.

<img width="1356" height="549" alt="Screenshot 2026-04-28 154134" src="https://github.com/user-attachments/assets/f4726b3b-0948-4e5e-a1c5-6152f039684b" />


I navigated to http://10.48.186.153/island/2100/green_arrow.ticket and found a Base58 encoded string: RTy8yhBQdscX.

<img width="1360" height="380" alt="Screenshot 2026-04-28 154508" src="https://github.com/user-attachments/assets/97259434-5be1-446c-a50e-c64e7f362c38" />


Initial Access & Data Extraction
--
Decrypting the Token
I took the string RTy8yhBQdscX to CyberChef and decoded it from Base58.

Resulting Password: !#th3h00d

<img width="1914" height="705" alt="Screenshot 2026-04-28 155039" src="https://github.com/user-attachments/assets/34488520-4e68-4098-8a97-6a7b981ed471" />


FTP Exploitation
With the username vigilante and the password !#th3h00d, I logged into the FTP server.

<img width="804" height="224" alt="Screenshot 2026-04-28 155648" src="https://github.com/user-attachments/assets/c0b7a381-1bba-4ca7-83ed-e1db28bec0a5" />

Bash
--
ftp 10.48.186.153
Inside, I found and downloaded a zip file named ss.zip. I extracted it and found two files that were very useful:

passwd.txt

shado (This contained the password: M3tahuman)

System Access & User Flag
--
SSH Login
I used the password from the shado file to log in as the user slade via SSH.

<img width="815" height="601" alt="Screenshot 2026-04-28 161357" src="https://github.com/user-attachments/assets/974db635-cd3f-431a-98a4-8f4da43a6b30" />


ssh slade@10.48.186.153
# Password: M3tahuman
Capturing the User Flag
Once I was in the system, I checked the home directory and grabbed the first flag.

<img width="804" height="603" alt="Screenshot 2026-04-28 161616" src="https://github.com/user-attachments/assets/6e7c943e-a3f4-4664-a801-521956dffe37" />


slade@LianYu:~$ cat user.txt
# THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}

<img width="811" height="169" alt="Screenshot 2026-04-28 161649" src="https://github.com/user-attachments/assets/696a2e19-b822-4b3b-b6b8-f125f95d3604" />

Privilege Escalation to Root
--
Sudo Rights Enumeration
I checked my current user's sudo privileges to see how I could get root access.



slade@LianYu:~$ sudo -l
The Vulnerability: I saw that slade can run /usr/bin/pkexec as root with NOPASSWD.

<img width="809" height="279" alt="Screenshot 2026-04-28 161839" src="https://github.com/user-attachments/assets/61cd963a-07e2-484b-a996-7ddc2ca27dc2" />


Exploitation
I used the GTFOBins method for pkexec to break out of the restricted shell and spawn a root shell.


sudo /usr/bin/pkexec /bin/sh
Final Root Flag
I confirmed I was root and then read the final flag to finish the machine.

