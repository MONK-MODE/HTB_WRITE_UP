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

`ipconfig -a`

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

### The followinf commands retrieve installed applications :

### Debian and derivatives use :`dpkg -l`

### Fedora based distros use :`rpm -qa`













