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



# ${{\color{purple}Exploitation}}\ $
 
# ${{\color{purple}Privilege Escalation}}\ $
 
# ${{\color{purple}Points of Improvement}}\ $
