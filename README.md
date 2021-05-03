

h2. Nagios Products
<pre>
* Nagios XI
* Nagios Core
* Nagios Fusion
* Nagios Log Server
* Nagios Network Analyzer 
</pre>
* Among them, Nagios Core is the only free available tool.

h2. Nagios Core Installation and Configuration
<pre>
* Please check the attachment file to install and configure nagios.
* If nagios download link does not work,please download it from this link https://sourceforge.net/projects/nagios/
* Enjoy nagios with your local host's services. 
</pre>
h2. Testing your local host's ssh service with Nagios Core

* If you don't have ssh server, please install it with following command
<pre>
 $ sudo apt update
 $ sudo apt install openssh-server
</pre>
* You can check the ssh-server with the following command
<pre>
 $ sudo systemctl status ssh
</pre>
* You can start/stop your ssh with the following command 
<pre>
 $ sudo systemctl start/stop ssh
</pre>
* Once you install ssh,you need to allow ssh in firewall
<pre>
 $ sudo ufw allow ssh
 $ sudo systemctl restart network-manager.service
</pre>
* You can close ssh service in firewall
<pre>
 $ sudo deny ssh_
 $ sudo systemctl restart network-manager.service
</pre>
* After you start your ssh service , you can check this service status in Nagios's Current status>Service. It took you about 2 minutes!!! Refresh it .
* Now, you can test your ssh service up or down with nagios. I hope you will enjoy it !!!

h2. Testing NCPA active check with Nagios Core

#  First download the https://raw.githubusercontent.com/NagiosEnterprises/ncpa/master/client/check_ncpa.py and place in this location /usr/local/nagios/libexec 
    If you don't want to download it , you can create py file and copy it.
#  You need to create check_ncpa command for nagios core and location is /usr/local/nagios/etc/objects. Add the following command in commands.cfg which is under /usr/local/nagios/etc/objects.
<pre>
define command {
 command_name    check_ncpa
 command_line    $USER1$/check_ncpa.py -H $HOSTADDRESS$ $ARG1$
}
</pre>
#  You need to create config file called ncpa.cfg under this location /usr/local/nagios/etc/servers and copy all of the command in the attachment config.odt .
#  You need to edit 2 things : address in "define host" section and mytoken which is your own token  in every sections.  

h2. Adding one more server's NCPA active check with Nagios Core

* Just copy the ncpa.cfg with another name in the same location (/usr/local/nagios/etc/servers) and then edit name , server ip and token.
* Enjoy it!!!

h2. How to receive Nagios'a notification mail using gmail

h3. Pre_requisites

# Turn on 2 step verification in your gmail account from security setting.
# Create app password (In this step , please select the *mail* and *window computer* ) and keep password.

h3. Installation and Configuration

* Install required package 
<pre>
$sudo apt install postfix bsd_mailx
</pre> 
* Enter the postfix directory
<pre>
$cd /etc/postfix
</pre>
* Edit the main.cf and add following instruction
<pre>
$sudo vim main.cf
</pre>
<pre>
relayhost =[smtp.gmail.com]:587
inet_interfaces = all
inet_protocols = ipv4
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_use_tls = yes
smtp_tls_CApath = /etc/ssl/certs  
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
</pre> 
@(Please check the certificate file path)@

* Need to add following command in /etc/postfix/sasl_passwd  
<pre>
[smtp.gmail.com]:587  abc@gmail.com(your gmail):1234567890987654(your generated app password)
</pre>
* You need to update the password database
<pre>
$sudo postmap /etc/postfix/sasl_passwd
<pre>
$sudo chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
</pre>
$sudo chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
</pre>
* Check your postfix status
<pre>
$sudo systemctl restart postfix.service
</pre>
* Try to send a test mail
<pre>
$ echo "Test Postfix Gmail Relay" | mail -s "Postfix Gmail" receivergmailaddress@gmail.com
</pre>
* Update the gmail address that you want to receive notification mail from Nagios in Nagios's contacts.cfg (/usr/local/nagios/etc/objects/contacts.cfg)
* And restart the nagios service
* If you don't understand the above steps, please check this following links
https://www.golinuxcloud.com/gmail-smtp-relay-server-postfix/
https://virtuallylg.wordpress.com/2020/02/10/configure-nagios-4-4-5-email-notification-using-gmail/



