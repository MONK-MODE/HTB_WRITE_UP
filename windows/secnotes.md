# Secnotes Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

nmap -sC -sV -oA secnotes 10.10.10.97 -T4

![image](https://user-images.githubusercontent.com/123066149/222884008-60c410d5-2f11-4ba0-9ca0-4e923373cffc.png)

nmap -p- -sV -oA fullsecnotes 10.10.10.97 -T4

![image](https://user-images.githubusercontent.com/123066149/222884018-91569f20-cedb-46b9-9601-867252fa1fb7.png)

nmap -p 8808 10.10.10.97 -sC -sV

![image](https://user-images.githubusercontent.com/123066149/222884064-c39d198f-5031-4133-b41a-ef1cdf261e42.png)


gobuster dir -u http://10.10.10.97/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x php,asp,a
spx,txt

![image](https://user-images.githubusercontent.com/123066149/222884045-90de364f-d65c-40aa-803e-7cef2ac312ef.png)

# ${{\color{purple}Initial Foothold}}\ $

# ${{\color{purple}Exploitation}}\ $

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $


