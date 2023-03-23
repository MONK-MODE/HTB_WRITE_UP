# Nibbles Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA nibbles 10.10.10.75 -T4``

![Capture](https://user-images.githubusercontent.com/123066149/227240454-70c72ed0-23f9-4a73-bbf7-2d1626cf86b0.PNG)

``nmap --script vuln 10.10.10.75``

![image](https://user-images.githubusercontent.com/123066149/227241004-243d9951-e593-41e5-9b31-42abffd71935.png)

``ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -u http://FUZZ.nibbles.htb/``

![image](https://user-images.githubusercontent.com/123066149/227240813-e1d32891-ce37-4321-9272-0cf16b66a07f.png)

# ${{\color{purple}Initial Foothold}}\ $

### View source code on ``http://10.10.10.75/``

![image](https://user-images.githubusercontent.com/123066149/227241302-296243dd-a25b-4f98-8ed1-2f9502c423be.png)

![image](https://user-images.githubusercontent.com/123066149/227241718-b21dd177-3cae-47a4-b552-743ce9e51f9b.png)

``dirb http://10.10.10.75/nibbleblog/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt``

![image](https://user-images.githubusercontent.com/123066149/227241970-5b07119e-77d8-4235-87b3-88792bed458c.png)

### I found the nibble version in /content/private/plugins/pages/db.xml :

![image](https://user-images.githubusercontent.com/123066149/227245234-617c10f8-0b17-43cb-8564-7683ce889222.png)


# ${{\color{purple}Exploitation}}\ $

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $
