# Question: Configure smtp in localhost.

Solution:

To Configure smtp in localhost wee will use postfix .

(postix is a mail transfer agent software used for routing and delivering email.)

Steps:

1.To install package in system we need sudo access. By using sudo command we login as  root user.
```bash
sudo su
```

2. Install postfix using apt-get install postfix command.
```bash
sudo apt-get install postfix 
```
or
```bash
apt-get install postfix
```

3.  After installation a pop-up configuration will be visible on screen complete the configuration as per the requirements.

4. Now we have  to configure the sasl  with gmail credentials.(Simple Authentication and Security Layer)
      
    • Go to sasl directory present in /etc/postfix location
      
      ```bash
      cd /etc/postfix/sasl

      ```

    • create a sasl_passwd file using file editor to store gmail credentials.
      ```bash
      nano  sasl_passwd

      ```
      
    • save credentials in format shown below
   ```bash
      [smtp.gmail.com]:587 nishant.kumar@gmail.com:qodjkozoaqdmyqll
   ```
    • Create a hash database file using postmap command.
```bash
      postmap sasl_passwd
```
      
6. To use gmail to send mail  using postfix we have to do some configuration in main.cf file.
    main.cf  file present in location /etc/postfix


    • open main.cf file using file editor 
      ```bash
      nano main.cf
      ```
        

    • set relayhost value 
      ```bash
      relayhost = [smtp.gmail.com]:587
      ```



    • Also add some configuration at the end of the file
   ```bash
	#enable SASL authentication
	smtp_sasl_auth_enable = yes
	smtp_sasl_security_options = noanonymous
	smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd
	smtp_tls_security_level = encrypt
	smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
   ```   

7. After doing all configuration restart the postfix using systemctl command
```bash
	systemctl restart postfix
```
8. All the configuration  has been completed. Now test sending email

we will use sendmail command  to send the email.
```bash
sendmail nishantddubey4@gmail.com
subject: SMTP setup Test mail
This mail is for testing the SMTP setup in Linux
.
```
Note: After completion of mail writing use ‘.’ or ctrl+d to exit and mail will  get send to the recipient’s mail  





# Question: Create a user in your localhost, which should not be able to execute the sudo command.

Solution:

steps:

1. To create a user we have to be a root user.
```bash
	sudo su
```
2. To create a user in local host we can use useradd command .
```bash
useradd nishant
```
3. Add password for user using passwd commands.
```bash
passwd nishant
```
4. To restrict the sudo permission for the user we will remove it from sudo group.
```bash
deluser nishant sudo
```
Now user is unable to execute sudo commands.



# Configure your system in such a way that when a user type and executes a describe command from anywhere of the system it must list all the files and folders of the user's current directory.

Ex:- $ describe
$ content1 content2
Content3 content 4


Solution:

1. To achieve this for a particular user we can add aliasing in .bashrc file.

Steps:

1. Go to the home directory of that user by using cd commands
```bash
cd
```

2.Look for .bashrc file using ls -a command
```bash
ls -a  .bashrc
```

3. Open that file using editor like vi or nano
```bash
nano  .bashrc
```
4. Add the aliasing in the file or end of the file
```bash
alias describe = ‘ls’
```
and save the file.

5. After adding the aliasing run source .bashrc command so that it come into effect.
```bash
source  .bashrc
```
Now when you run  $ describe command it will display list of directories and files of the user’s current directory.

2. To achieve this for all user of the system we can add the aliasing globally in .bashrc file of  /etc.

Steps:
1. Switch to root user by using sudo su command.
```bash
sudo su
```
1. Go to the /etc directory  by using cd commands
```bash
cd /etc
```
2.Look for bashrc file using ls -a command
```bash
ls -a bashrc
```
3. Open that file using editor like vi or nano
```bash
vi  bashrc
```
4. Add the aliasing in the file or end of the file
```bash
alias describe = ‘ls’
```
and save the file.

5. After adding the aliasing run source .bashrc command so that it come into effect.
```bash
   source  .bashrc
```
Now when you run  $ describe command by any user of the  system it will display list of directories and files of the user’s current directory.



# Question: Users can put a compressed file at any path of the linux file system. The name of the file 	 will be research and the extension will be of compression type, example for gzip type extension will be .gz. You have to find the file and check the compression type and uncompress it. 

Solution:

To achieve thjs we can write a script with find a file starts with research. After that it will check the compression type of file and uncompress it if possible.


Explanations of all steps :

1. Create a script file with sudo access so that it can  decompress the file of any location in our file system.
    • Switch to root user 
	  
2. Create a script file using file editor.
eg . Decompress.sh (providing .sh is not mandatory to increase the readability I have given) 
       
3. write the script in file:
```bash
#!/bin/bash

echo Searching for research file  in your system...

#searhing the file

file=$(find / -name "research.*" 2>/dev/null )

#checking if the exist or not
if [ -z "$file" ];
then
 
  echo "Sorry! No research file available in your System"
  exit 1
fi

echo The available file is "$file"

#check file extension to find compression type
type="${file##*.}"

#uncompressing the file 

case "$type" in
#if giz compressed file found
gz) echo uncompressing  "$file"
    gzip -d "$file"
;;

#if zip compressed file found
zip) echo  uncompressing "$file"
     unzip "$file"
;;
# for other type of file
*) echo "unknown compression file type $file"
exit 1
;; 
esac

echo "File uncomprssed successfully"
```
```bash
file=$(find / -name "research.*" 2>/dev/null )
```
> it will search for the file whose name starts with research and ends with any extension.
Like research.txt , research.gip etc and store the file path in file variable.
```bash
if [ -z "$file" ];
then
  echo "Sorry! No research file available in your System"
  exit 1
fi
```
> this step will check that the availability of research file.
If the file variable is empty then it will execute this statement and display “Sorry! No research file available in your system.
And exit 1 will exit the execution  of script.
```bash
echo The available file is "$file"
```
>if file is available in system it will display the file name 
```bash
type="${file##*.}"
```
> in this step it will extract the extension of available research file and store it in type variable.
Eg if the research.gz file is available then it will extract the gz and store it in type variable.


Now we will use case statement to decompress the file.
```bash
Case "$type" in
#if giz compressed file found
gz) echo uncompressing  "$file"
    gzip -d "$file"
;;

#if zip compressed file found
zip) echo  uncompressing "$file"
     unzip "$file"
;;

#if bzip2 compressed file found
bz2) echo uncompressing "$file"
     bzip2 -d "$file"
;;

#if xz compressed file found
xz) echo uncompressing "$file"
    xz -d "$file"
;;

#for other type of file
*) echo "unknown compression file type $file"
exit 1
;; 

esac
```

it will check the file extension and decompress accordingly. If the file with different extension which cannot be  decompressed then it will display “unknown compression file type <with file path>”
```bash
echo "File uncomprssed successfully"
```
> After successful decompression of file it will display “File uncompressed successful”


# Configure your system in such a way that any user of your system creates a file then there should not be permission to do any activity in that file.

Solution:

Steps:

To set default permission of a file creation we can use umask command.

1. To apply this configuration to all users of system we have to add umask permission in bashrc file of /etc directory.

Become root user using sudo su command.
```bash
sudo su
```
Go the /etc dir and look for bashrc file.
```bash
cd /etc
```
```bash
ls bashrc
```
2. Open the file using file editor like vi or nano
```bash
   nano basrc
```
4. Set default configuration for file permission
```bash
umask  u-rwx
save the file
```

6. After adding the configuration run source bashrc command so that configuration come into effect.
```bash
source bashrc
```
Now if a user creates a file he will not able to any activity in that file.



# Question: Create a service with the name showtime , after starting the service, every minute it should print the current time in a file in the user home directory. 
Ex:-
sudo service showtime start -> It should start writing in file.
sudo service showtime stop -> It should stop writing in file.
sudo service showtime status -> It should show status.


Solution: 
To achieve this we can write a script which writes current time in users home directory.
And then create a systemd service unit  file which manage the execution of that script.
After creating the systemd we can stat stop and  check the status of service as per the requirements.

Steps:

1. For creating service we need sudo access so first switch user to root or we can also do that sudo access users.
```bash
sudo su
```  

2. switch to home directory of the root user using cd command.
```bash
   cd
```

4. Create a script file like showtime.sh  using file editor like  vim or  nano (giving .sh extension is not mandatory to increase the readability we can do that).
```bash
nano showtime.sh
```
4. create a scripts which writes current time in a file after every minutes in user’s home directory here user is root so it will write in root’s home directory with filename showtime.txt
So script is like :
```bash
#!/bin/bash
while true;
do 
echo $(date +%T) >> /root/showtime.txt
sleep 60
done
```
Script has been  completed. Now we will create service.

5. To create a systemd service we have to change the directory to /etc/systemd/system
```bash
cd /etc/systemd/system
``` 
6.  create a systemd unit file with showtime name (eg. showtime.service) using file editor.
```bash
nano showtime.service
```
7.  Write all the required configurations like:
```bash
[Unit]
Description=Showtime Service
After=network.target

[Service]
Type=simple
ExecStart=/root/showtime.sh
Restart=always

[Install]
WantedBy=multi-user.target
```
8.  Now reload and start the systemd service using systemctl commands:
    • To reload the  systemd configuration files to apply changes in services:
	```bash
 	systemctl daemon-reload
	```

    • To start the showtime service :
   ```bash
   	systemctl start showtime
```
    
The system get started it will start writing current time in showtime.txt file in user’s  home directory.
	
10. To check the status of service we uses systemctl status showtime.
```bash
systemctl status showtime
```
10. To stop the service we can use systemctl stop showtime.
```bash
systemctl stop showtime
```


