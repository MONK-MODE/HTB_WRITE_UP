# Stocker Hack The Box Tips

nmap -sC -sV -oA stocker 10.10.11.196 -T4

22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)

80/tcp open  http    nginx 1.18.0 (Ubuntu)

# ${{\color{purple}Initial Recon}}\ $

ffuf -w /opt/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://FUZZ.stocker.htb/

![image](https://user-images.githubusercontent.com/123066149/221412809-e0e3d7d7-885d-42c7-ba78-1797f9af88d3.png)

![image](https://user-images.githubusercontent.com/123066149/221412903-86651091-7b69-4c0d-bcd6-edac6a36c7d6.png)

### After several techniques that I found in this resource :

https://book.hacktricks.xyz/pentesting-web/login-bypass

![1](https://user-images.githubusercontent.com/123066149/221413154-f34bf355-462e-4223-acd4-ff2e4fabd5d1.PNG)

### I managed to get an error message :

![2](https://user-images.githubusercontent.com/123066149/221413230-f9a77189-539d-44e8-b14d-9085b09ffcbe.PNG)

### I managed to bypass the authentication with this payload :

![3](https://user-images.githubusercontent.com/123066149/221413358-9bfebf0f-2452-424f-ba06-855b408927f2.PNG)

### I found this with burp sniper attack :

![4](https://user-images.githubusercontent.com/123066149/221413666-3af09096-91d9-4872-9c4b-f93a2ac1880c.PNG)

### Delet the url encode :

![5](https://user-images.githubusercontent.com/123066149/221413668-4c6ff2a1-c2af-442d-b2c2-289e23856004.PNG)

![6](https://user-images.githubusercontent.com/123066149/221413675-0fc96cf4-fd0a-4b51-9455-9e429ee8ffca.PNG)


# ${{\color{purple}Initial Foothold}}\ $

# ${{\color{purple}Exploitation}}\ $

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $
