# Friendzone Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -T4 -sC -sV -oA friendzone 10.10.10.123``

![image](https://user-images.githubusercontent.com/123066149/227715804-9215496e-3212-469c-9da1-272ade4a802c.png)

# ${{\color{purple}Initial Foothold}}\ $

### Replay creds in ssh

### Replay smb creds on ftp

### Try to find other things with user admin on SMB shares

### Add domain names to /etc/hosts ()

### Search on DNS side  :white_check_mark:

### SMB enum

``smbmap -H 10.10.10.123``

![image](https://user-images.githubusercontent.com/123066149/227715847-cc1680ce-78f5-4084-9049-abbb1956db7d.png)

``smbclient //10.10.10.123/general``

![image](https://user-images.githubusercontent.com/123066149/227715872-df527fba-0711-43e1-b6af-d8db2f05a723.png)

![image](https://user-images.githubusercontent.com/123066149/227715906-0a8e9703-d052-4a94-bb56-0ebed1a64826.png)

### Web enum

![image](https://user-images.githubusercontent.com/123066149/227716034-45ddf9e5-3391-4958-a484-8b4432dd6f9a.png)

![image](https://user-images.githubusercontent.com/123066149/227717200-c5afeb24-6b94-4087-b72d-1c78f0607f13.png)

### DNS enum

``dig axfr @10.10.10.123 friendzone.red``

![image](https://user-images.githubusercontent.com/123066149/227717361-e5234c86-9ff2-48f1-a030-ed286133ef53.png)

``dig axfr @10.10.10.123 friendzoneportal.red``

![image](https://user-images.githubusercontent.com/123066149/227717371-0b0457e9-2a73-4fb8-84d7-0a683de8e41f.png)

``cat transfertdezones | grep IN | grep friendzone |awk '{print $1}' |sort -u |sed 's/\.$//g'``

![image](https://user-images.githubusercontent.com/123066149/227717410-eb5cca38-32b9-4aa7-99e1-ffedca50dbe6.png)

``:%s/\n/ /g``

![image](https://user-images.githubusercontent.com/123066149/227717528-fe3d6f81-9df7-47fb-8c1c-c7cb5d0924c3.png)


# ${{\color{purple}Exploitation}}\ $

![image](https://user-images.githubusercontent.com/123066149/227717586-e965ab7e-c342-4c9b-bf7c-0d1aa3b29443.png)

``cat hosts | aquatone ``

![image](https://user-images.githubusercontent.com/123066149/227717634-2ac6ba4a-8b53-4614-8bfd-3a9b5e1a6043.png)

``file:///home/kali/Documents/htb/friendzone/aquatone_report.html#/``

![image](https://user-images.githubusercontent.com/123066149/227717700-c385116f-882a-4504-9a60-1cb5b4c600a7.png)

![image](https://user-images.githubusercontent.com/123066149/227717711-648aa72f-28d0-4f20-a493-02e73f3731d2.png)

![image](https://user-images.githubusercontent.com/123066149/227717731-05a021e8-9626-4868-b9ff-964a44ae592b.png)

![Capture](https://user-images.githubusercontent.com/123066149/227717846-1a2f0735-3c47-4733-9e4d-07e21c2726e6.PNG)

``php://filter/convert.base64-encode/resource=index``

![image](https://user-images.githubusercontent.com/123066149/227717866-cce155d7-e113-4c93-be7d-2b2573ab3a0c.png)

``echo -n azeazeazeeazea | base64 -d``

![image](https://user-images.githubusercontent.com/123066149/227717897-56948f7e-6460-4282-8487-ece63bd07159.png)

![image](https://user-images.githubusercontent.com/123066149/227718138-48a0fe3d-0cf6-44ca-b0f0-a0ff3d6c11f1.png)

![image](https://user-images.githubusercontent.com/123066149/227718179-67b46878-d50d-46b5-b124-9f7fc142fae2.png)

![image](https://user-images.githubusercontent.com/123066149/227718336-1144299c-c055-42c6-8568-cf98bd882486.png)

![image](https://user-images.githubusercontent.com/123066149/227718348-a2577025-134f-4d35-864b-6f0d3d5a7b92.png)

# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/227718434-6f461657-c273-42f4-804c-0ad805f2979c.png)

# ${{\color{purple}Point of Improvement}}\ $

* Check http and https for each domain
* Check dnstransferdezone for each domain
* Check multiple lfi to find the lfi
* No need file extension for the lfi

Prvesc: 

* You have to modify /usr/lib/python2.7/os.py
* Remove the os command in the revshell
* I put the script at the end of /usr/lib/python2.7/os.py to make it work 
