# Bounty Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

nmap -sC -sV -oA bounty 10.10.10.93
80/tcp open  http    Microsoft IIS httpd 7.5

dirb http://10.10.10.93/ -w /usr/share/wordlists/dirb/common.txt
gobuster dir -u http://10.10.10.93 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x aspx,asp

# ${{\color{purple}Initial Foothold}}\ $

### My goubster command allowed me to find the file /transfer.apsx which allows to upload on the server 

gobuster dir -u http://10.10.10.93 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x aspx,asp

![1](https://user-images.githubusercontent.com/123066149/220401946-fe0162ee-df1e-464c-a47e-e15b6e1fbd5e.PNG)

![image](https://user-images.githubusercontent.com/123066149/220402400-1e3f9ba5-fe23-4065-81c3-d6977054fde9.png)

### And the /UploadedFiles directory :

![image](https://user-images.githubusercontent.com/123066149/220402804-2b5b447c-7a21-4f48-85c5-606e5ccc6c37.png)

### I decided to bruteforce a valid extension for this file uploade forms :

![image](https://user-images.githubusercontent.com/123066149/220403441-8a066e40-36cd-4899-93ed-620cf87938ad.png)
![image](https://user-images.githubusercontent.com/123066149/220403611-c9f3b6cd-c12f-4c23-b15e-bc6e34d9c8b8.png)

### Load extension.txt i used hacktrycks to find the right extensions according to the type of server :

https://book.hacktricks.xyz/pentesting-web/file-upload

![image](https://user-images.githubusercontent.com/123066149/220445347-d01adb51-b57f-45f5-b5b2-5ba5dbe30298.png)

### For this attack is important to find the extension that the server will accept. 
### It's with the length of the answer that I find the right extension.

![2](https://user-images.githubusercontent.com/123066149/220445721-12856fa5-e575-4748-a557-29699d739e19.PNG)


# ${{\color{purple}Exploitation}}\ $ **:white_check_mark:**

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $
