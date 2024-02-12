#Question: Configure smtp in localhost.

Solution:

To Configure smtp in localhost wee will use postfix .

(postix is a mail transfer agent software used for routing and delivering email.)

Steps:

1.To install package in system we need sudo access. By using sudo command we login as  root user.
>> sudo su


2. Install postfix using apt-get install postfix command.
>> sudo apt-get install postfix 
or
>> apt-get install postfix


3.  After installation a pop-up configuration will be visible on screen complete the configuration as per the requirements.

4. Now we have  to configure the sasl  with gmail credentials.(Simple Authentication and Security Layer)
      
    • Go to sasl directory present in /etc/postfix location
      
      >> cd /etc/postfix/sasl


    • create a sasl_passwd file using file editor to store gmail credentials.
      
      >> nano  sasl_passwd
      
    • save credentials in format shown below
      [smtp.gmail.com]:587 nishant.kumar@gmail.com:qodjkozoaqdmyqll

    • Create a hash database file using postmap command.
      >> postmap sasl_passwd
      
      
5. To use gmail to send mail  using postfix we have to do some configuration in main.cf file.
    main.cf  file present in location /etc/postfix


    • open main.cf file using file editor 
      
      >> nano main.cf
        

    • set relayhost value 
      
      relayhost = [smtp.gmail.com]:587
      



    • Also add some configuration at the end of the file
	
	smtp_sasl_auth_enable = yes
	smtp_sasl_security_options = noanonymous
	smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd
	smtp_tls_security_level = encrypt
	smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
	     

6. After doing all configuration restart the postfix using systemctl command
>> systemctl restart postfix
    
7. All the configuration  has been completed. Now test sending email

we will use sendmail command  to send the email.

>> sendmail nishantddubey4@gmail.com
subject: SMTP setup Test mail
This mail is for testing the SMTP setup in Linux
.
  
Note: After completion of mail writing use ‘.’ or ctrl+d to exit and mail will  get send to the recipient’s mail  





# Question: Create a user in your localhost, which should not be able to execute the sudo command.

Solution:

steps:

1. To create a user we have to be a root user.
>> sudo su





2. To create a user in local host we can use useradd command .
>> useradd nishant

3. Add password for user using passwd commands.
>> passwd nishant



4. To restrict the sudo permission for the user we will remove it from sudo group.
>> deluser nishant sudo



Now user is unable to execute sudo commands.


