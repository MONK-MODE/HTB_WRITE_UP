# SwagShop Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA swagshop -T4 10.10.10.140``

![image](https://user-images.githubusercontent.com/123066149/231065471-bde63e29-1e78-4f86-aebb-f94e96af6dcc.png)

``nmap -p- -sV -oA fullswagshop -T4 10.10.10.140``

![image](https://user-images.githubusercontent.com/123066149/231065533-3be37214-477a-4fab-bbdb-7b670a374360.png)

# ${{\color{purple}Initial Foothold}}\ $

``magescan scan:all http://swagshop.htb``

![image](https://user-images.githubusercontent.com/123066149/231065661-35a6a5cd-5e5d-4d78-8382-f6d4e559d487.png)

### Create a user :

![image](https://user-images.githubusercontent.com/123066149/231065875-1bc5b323-4ca7-439d-9f16-3ac7e038bb16.png)

### You have customer in the http request you can change that to admin :

![image](https://user-images.githubusercontent.com/123066149/231065995-8dae12f7-c3dc-48e9-8727-8885ed025277.png)

``searchsploit magento``

![image](https://user-images.githubusercontent.com/123066149/231066344-7bca929b-f9ce-4db3-8576-13ba8af28b95.png)

### You have to use the second exploit to creat a user :
### Change the url value :

![image](https://user-images.githubusercontent.com/123066149/231066602-168e5ed6-dd81-41f1-a677-93e60ed2436d.png)

![image](https://user-images.githubusercontent.com/123066149/231066664-e2b9aea0-fcfe-4b2d-9a05-403a2c28bb76.png)

### After that you can use the authenticated exploit :
### Change the 4 values :

![image](https://user-images.githubusercontent.com/123066149/231066908-4125db05-a5c7-436f-b382-3db20b7b4e90.png)

### It's possible to ad a proxy and see the request in burp :

``#br.set_proxies({"http": "localhost:8080"})``

![image](https://user-images.githubusercontent.com/123066149/231067062-191de0db-474f-4652-bd41-8811dd84813c.png)

### In thi case it's good to user pdb to help you in the script debuging :

``import pdb``

![image](https://user-images.githubusercontent.com/123066149/231067195-ac94300a-3802-49a7-b323-6ef0ab381f21.png)

``pdb.set_trace()``

![image](https://user-images.githubusercontent.com/123066149/231067316-51f840fd-c458-404d-9b34-af2a6df1be6a.png)

![image](https://user-images.githubusercontent.com/123066149/231067433-9cf988cb-157f-4b1a-bab1-b38a44cf991a.png)

###  use ``c`` to cotinue :

![image](https://user-images.githubusercontent.com/123066149/231067641-a6416f08-b896-46e8-a8a5-1f6259aecba7.png)


# ${{\color{purple}Exploitation}}\ $

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $
