# Nest Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -p- -oA fullnest 10.10.10.178``

445/tcp  open  microsoft-ds

4386/tcp open  unknown


``telnet 10.10.10.178 4386``

![image](https://user-images.githubusercontent.com/123066149/219978019-3bb70e9b-0f76-4ca8-8b82-0b4edb6179f5.png)


HQK Reporting Service V1.2

![image](https://user-images.githubusercontent.com/123066149/219978012-ef2de3c1-ee16-4f12-a81d-e5d1c908e88e.png)

# ${{\color{purple}Initial Foothold}}\ $

smbclient -U 'guest%' -L //10.10.10.178  **:white_check_mark:**

![nest](https://user-images.githubusercontent.com/123066149/219977952-10bf82e2-17dd-4847-9662-7c0574cb3f03.PNG)

smbmap -u 'guest%' -p '' -H 10.10.10.178

![nest2](https://user-images.githubusercontent.com/123066149/219977955-11587394-53bb-4003-96db-4444483d1f9e.PNG)


# ${{\color{purple}Exploitation}}\ $ 

# ${{\color{purple}Privilege Escalation}}\ $

# ${{\color{purple}Points of Improvement}}\ $
