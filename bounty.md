# Bounty Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

nmap -sC -sV -oA bounty 10.10.10.93
80/tcp open  http    Microsoft IIS httpd 7.5

dirb http://10.10.10.93/ -w /usr/share/wordlists/dirb/common.txt
gobuster dir -u http://10.10.10.93 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x aspx,asp

# ${{\color{purple}Initial Foothold}}\ $

### My goubster command allowed me to find the file /transfer.apsx which allows to upload on the server **:white_check_mark:**

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

ASP: .asp, .aspx, .config, .ashx, .asmx, .aspq, .axd, .cshtm, .cshtml, .rem, .soap, .vbhtm, .vbhtml, .asa, .cer, .shtml

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


# ${{\color{purple}Exploitation}}\ $ 

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $
