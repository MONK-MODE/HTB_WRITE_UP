# Arctic Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

### For this box I added the T5 option to my scan because I knew that a common scan would not be enough.
### I also wanted to see the difference between T4 and T5 but it was minimal for me 

nmap -sC -sV -oA arctic 10.10.10.11 -T5

![image](https://user-images.githubusercontent.com/123066149/220932396-c5773103-b471-4d3c-b5e5-51a619a37150.png)

![image](https://user-images.githubusercontent.com/123066149/220932465-d8737908-3574-4299-b174-7ddb20ea4f36.png)


# ${{\color{purple}Initial Foothold}}\ $

### After a google search I realize that the FMTP protocol is an equivalent of a web server to store files :
### On this box it's on the 8500 port

![image](https://user-images.githubusercontent.com/123066149/220934868-d67af8f1-7bfd-42c2-94bd-8aed78e3953d.png)

### I go to the amin section :

![image](https://user-images.githubusercontent.com/123066149/220935241-2a89a9c0-525f-4bef-8e8e-e2ec52f89f6b.png)

And now, we can see and Adobe coldfusion version 8 :

![image](https://user-images.githubusercontent.com/123066149/220935941-ebb041fa-b10f-4455-a9f6-c15d441be454.png)

### As we can see there is an lfi :

![image](https://user-images.githubusercontent.com/123066149/220937986-a4f79541-5760-446e-9d10-bbad6ea21e6a.png)

``hashid 2F635F6D20E3FDE0C53075A84B68FB07DCEC9B03``

![image](https://user-images.githubusercontent.com/123066149/220938321-30ea552b-5772-4418-850c-42c99d6cd362.png)

### I did not go further on this track because there is an unauthanticated rce :

![image](https://user-images.githubusercontent.com/123066149/220939537-b8df9049-69c7-471a-9366-ca3e58c38b88.png)

https://www.exploit-db.com/exploits/50057

``searchsploit -x cfm/webapps/50057.py``

# ${{\color{purple}Exploitation}}\ $

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $
