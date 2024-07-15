# Linux Priv escalation Methodo

# ${{\color{purple}OS : Kernel : Hostname}}\ $

`cat /etc/issue`

`cat /proc/version`

`hostname`

`uname -a`

`uname -r`

# ${{\color{purple}Users}}\ $

`cat /etc/passwd`

`cat /etc/shadow`

`id`

`who`

`w`

`sudo -l`

# ${{\color{purple}Networking information}}\ $

`ifconfig -a`

`route`

`netstat -antup`

`arp -e`

# ${{\color{purple}Path}}\ $

`echo $PATH`

# ${{\color{purple}Check OPT}}\ $

`cd /opt`

`ls -lah`

# ${{\color{purple}Applications and Services}}\ $

`ps aux`

`ps aux | grep root`

`ls -ls /etc/grep.conf`

`ls -ls /var/wwww/html/`

### The followinf commands retrieve installed applications :

### Debian and derivatives use :
`dpkg -l`
### Fedora based distros use :
`rpm -qa`

# ${{\color{purple}SUID Programs}}\ $

`find /* -user root -perm -4000 -print 2>/dev/null`

# ${{\color{purple}Files and Files Systems}}\ $

### Use the following command to check for unmouted file systems :
`cat /etc/fstab`
### World writable directories :
`find / \( -wholename '/home/homedir*' -prune \) -o \( -type d -perm -0002 \) -exec ls -ld '{}' ';' 2>/dev/null | grep -v root`
### World writable directories for root :
`find / \( -wholename '/home/homedir*' -prune \) -o \( -type d -perm -0002 \) -exec ls -ld '{}' ';' 2>/dev/null | grep root`
### World writable files :
`find / \( -wholename '/home/homedir/*' -prune -o -wholename '/proc/*' -prune \) -o \( -type f -perm -0002 \) -exec ls -l '{}' ';' 2>/dev/null`
### Find world writable files in /etc :
`find /etc -perm -2 -type f 2>/dev/null`
### And the following command searches for world writable directories :
`find / -writable -type d 2>/dev/null`
### Search for binaries with special capabilities :
`getcap -r / 2>/dev/null`

# ${{\color{purple}Scripts }}\ $

### LinPEAS :
https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS

### LinENUM :
https://github.com/rebootuser/LinEnum

### Linux privilege escalation checker :
`wget http://www.securitysift.com/download/linuxprivchecker.py`

### Unix-privesc-check v1.4 :
https://pentestmonkey.net/tools/audit/unix-privesc-check

### PSPY :
https://github.com/DominicBreuker/pspy




