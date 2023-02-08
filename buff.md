# Buff WRITE UP

# ${{\color{purple}Box Info}}\ $

### Buff is an easy difficulty Windows machine that features an instance of Gym Management System 1.0. This is found to suffer from an unauthenticated remote code execution vulnerability. Enumeration of the internal network reveals a service running at port 8888. The installation file for this service can be found on disk, allowing us to debug it locally. We can perform port forwarding in order to make the service available and exploit it.

# ${{\color{purple}Enumeration}}\ $

`nmap -sC -sV -oA buff 10.10.10.198` or `nmap -p- -sV -oA bufffull 10.10.10.198`

### Nmap Scan Output
![buff1](https://user-images.githubusercontent.com/123066149/217452638-5fc3ff09-ae61-4a64-a9f4-a4a825245dc8.png)

# ${{\color{purple}Web - Enumeration(8080)}}\ $ 
