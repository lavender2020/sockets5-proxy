How to Setup a SOCKS5 Proxy Server Using SS5 on CentOS 7
========================================================

# Introduction  
In this article we are going to set up a Socks5 Proxy server using SS5 on CentOS 7. SS5 is a high performance SOCKS proxy server implementing SOCK4 and SOCKS5 protocols. As a proxy server, SS5 authenticates, profiles and processes network requests for clients. It establishes connections to application hosts for client applications. When the client attempts to access the network, the client connects to the SS5 daemon instead of the application host. A proxy server functions as an intermediary between your device and the Internet. The traffic directed through a proxy server will look as if it came from its IP address rather than your own. Proxy servers do not encrypt the traffic passing through them, which saves resources and allows accepting all the occurring connections from much larger numbers of users. SOCKS stands for SOCKet Secure which is an Internet protocol that routes network packets between a client and server through a proxy server. Its is an extension of the SOCKS protocol that supports advanced networking technologies, such as the UDP protocol and IPv6. Additionally, since SOCKS servers do not distinguish between network traffic types like HTTP for web browsing, SMTP for email, FTP for torrent files, etc. These are considered to be the most flexible for proxy server protocols. Its a general purpose proxy server that establishes a TCP connection to another server on behalf of a client and then routes all the traffic between the client and the server.  

SOCKS currently has two versions that is SOCKS5 and SOCKS4 but its version 4 only supports TCP application, while SOCKS5 version supports both applications. However, because of the fact that SOCKS5 also supports various authentication mechanisms and domain name resolution , which does not go with SOCKS4, the outgoing SOCKS proxy is normally SOCKS4 proxy. As a result, UDP applications are not supported normally. SOCKS5 proxy servers are very flexible and compatible with all kinds of Internet traffic like POP3 and SMTP for emails and FTP for uploading files to websites. They are also noticeably faster than services with encryption.That’s why they are more popular among users who do not need advanced security and encryption but do require the privacy and flexibility achieved by replacing their IP addresses.  

# Prerequisites
There are many proxy server software which can provide both SOCKS4 proxy and SOCKS5 proxy, but here we are going to setup using SS5 for Linux CentOS 7. Connect to your system using sudo privileged user or root credentials.   

# installation  
* ssh root@xx.x.xx.xx  
Run the commands below to install the latest version of EPEL repository for CentOS 7 and then update your system for missing updates and security patches.  

* yum install epel-release  
* yum update -y  

Establishing Dependencies    
The ss5 requires to have Development tools to be installed on the system along with some other packages. Install these packages using YUM package manager using below commands.    

* yum groupinstall 'Development Tools'  
* yum install gcc automake autoconf libtool make pam-devel yum-utils openldap-devel openssl-devel  
Press ‘Y’ key to continue installing the required packages along with some of its dependencies.    

* Installing SOCKS5 Proxy Server Using SS5 on CentOS 7  
Prepare the catalog first and download the SS5 packages using wget command as shown below within /opt/ directory.  

* mkdir /opt/ss5  
* cd /opt/ss5  
* wget http://sourceforge.net/projects/ss5/files/ss5/3.8.9-8/ss5-3.8.9-8.src.rpm  
Once the package has been downloaded, then check whether all dependencies are installed.    

* yum-builddep ss5-3.8.9-8.src.rpm  
You can also download the source package of ss5 using below command and then unpack the sources of SS5 and install the package.    

* wget http://sourceforge.net/projects/ss5/files/ss5/3.8.9-8/ss5-3.8.9-8.tar.gz  
* tar -zxf ss5-3.8.9-8.tar.gz    
* cd ss5-3.8.9  
Then within the same directory run ./configure command. This will be responsible for getting ready to build the software on your system. It makes sure all of the dependencies for the rest of the build and install process are available, and finds out whatever it needs to know to use those dependencies.  

* ./configure  

# ss5 install  

Once configure has done its job, we can invoke make to build the software. This runs a series of tasks defined in a Makefile to build the finished program from its source code.  

* make
Now that the software is built and ready to run, the files can be copied to their final destinations. The make install command will copy the built program, and its libraries and documentation, to the correct locations.  

* make install
Configuring SS5 Socks5 Proxy server  
We have installed the ‘SS5’ packages using its source code, now we will be making some of its required configuration changes in its configuration files located in /etc/opt/ss5/ directory. But, before that copy the original configuration files before making changes to revert in case you need to revert back changes.  

* cd /etc/opt/ss5/
* cp ss5.passwd ss5.passwd.org
* cp ss5.conf ss5.conf.org
Let’s start configuring SS5 by opening its configuration using any of your favorite command line editor like ‘vi’, ‘vim’ or ‘nano’.  

* vim /etc/ss5.conf
The configuration file contains four sections:  
'''
variable and flags  
authentication  
authorization  
bandwidth  
proxy  
dump  
routing  
balancing  
miscellaneous  
''' 

In each section, the SS5 daemon sequentially reads each line until it encounters a matching line for that section. The order of sections and the order of lines within a section are crucial to achieving the desired result. Every entry in a line must match. A few examples of customization as mentioned below.  

## SECTION AUTHENTICATION

The ‘auth’ directive sets the authentication policy.  

1) Allow unauthenticated access to the socks server to the world only from the following addresses:  

auth 0.0.0.0/0 - -  
permit - 192.168.xx.xx/32 - 0.0.0.0/0 - - - - -  
permit - 172.25.xx.yy/32 - 0.0.0.0/0 - - - - -  
2) Allow unauthenticated access from the socks server to the world from all addresses (open proxy):  

auth 0.0.0.0/0 - -  
permit - 0.0.0.0/0 - 0.0.0.0/0 - - - - -  
3) Allow access only with authentication, for example, user user with password password access from the socks server to the world.  

auth 0.0.0.0/0 - u  
permit u 0.0.0.0/0 - 0.0.0.0/0 - - - - -  
Save and close the file using 'wq!'.  

### ss5 auth

Next we need to add the user and password in '/etc/opt/ss5/ss5.passwd' file.  

* vim /etc/opt/ss5/ss5.passwd  
user password  
kash kash123  
You can put user and password separated by a space, one user/password per line to add multiple users. Make sure that this file is readable only by root that can be done issuing below commands.

* chown root.root /etc/opt/ss5/ss5.passwd
* chmod 750 /etc/opt/ss5/ss5.passwd
* user passwd

### Starting SS5 Service
SS5 has been installed and configured now run the SS5 server and check whether it is running fine by using below commands.  

* ss5 -u root -b 0.0.0.0:1080
Now run below commands to grep its port and process to confirm that is up and running.  

* netstat -anp | grep ss5
* ps -ef | grep ss5

### ss5 service

From the output as shown above, we can confirm that ss5 is running fine on port ‘10080’ . If you see any error message like the following in the logs,  

can't create pid file /var/run/ss5/ss5.pid  
 can't unlink pid file /var/run/ss5/ss5.pid  
Then make sure to create the directory '/var/run/ss5' and start ss5 again.  

### Managing SS5 Logs
In order to view SS5 operation logs, let’s run below command to know if there is any issue going on.  

* tail -f /var/log/ss5/ss5.log
[07/Mar/2017:12:27:23 GMT] [INFO] Copyright (C) 2002-2013 by Matteo Ricchetti -   
[07/Mar/2017:12:27:23 GMT] [INFO] Setting dynamic configuration.  
[07/Mar/2017:12:27:23 GMT] [INFO] Cleaning old configuration.  
[07/Mar/2017:12:27:23 GMT] [INFO] Loading and validating new configuration.  
[07/Mar/2017:12:27:23 GMT] [WARN] Duplicate auth lines in config file.  
[07/Mar/2017:12:27:23 GMT] [INFO] Loading configuration completed  
[07/Mar/2017:12:27:23 GMT] [INFO] Loading HA configuration completed  
[07/Mar/2017:12:27:23 GMT] [INFO] Switching to new configuration.  
[07/Mar/2017:12:27:23 GMT] [VERB] Role is ALONE.  
[07/Mar/2017:12:27:23 GMT] [INFO] Loading network interfaces.  
Remove the Duplicate ‘auth’ line from the SS5 configuration file as indication in the logs above.  

To configure the log rotation for ss5 logs, create a new file and put the following contents in that as shown below.  

* vim /etc/logrotate.d/ss5
var/log/ss5/ss5.log {  
daily  
rotate 3  
dateext  
compress  
missingok  
notifempty  
copytruncate  
}  
That’s it save and close the file.  

### Connecting to SS5 Proxy
Now, let’s check from another server to execute the request through SS5 Proxy, before that make sure that port ‘1080’ is allowed for your source system. Then run below command from the remote VM.  

* curl --socks5 destination_ip:1080 --proxy-user user:password http://ipinfo.io/ip
Then check ss5 logs of your SS5 Proxy server and you will see the below logs showing successful connection .

* tail -f /var/log/ss5/ss5.log
[07/Mar/2017:13:06:45 GMT] [10144] source_ip "" "CONNECT" STARTED 0 0 0 (source_ip:59286 -> destination_ip:80)  
[07/Mar/2017:13:06:45 GMT] [10144] source_ip "" "CONNECT" TERMINATED 245 75 0 (source_ip:59286 -> destination_ip:80)  

### Using SS5 Web Console  
SS5 statistics console reports socks server statistics such as number of connections, authentications and authorizations. SS5 balancing console shows virtual ip connections and sessions affinity.  

Follow the steps below to enable web console on your CentOS 7 VM.  

1) First step is to make sure that you have a web server installed on the same VM Apache or Nginx with CGI support.  

* yum install httpd -y
2) Next, copy the statmgr and balamgr binary file from source distribution (modules/mod_statistics/statmgr.cgi and modelus/mod_balance/balamgr.cgi) to your web server cgi-bin directory (i.e. /var/www/cgi-bin/) and set execution permissions to the file.  

* cp modules/mod_statistics/statmgr.cgi /var/www/cgi-bin/
* cp modules/mod_balance/balamgr.cgi /var/www/cgi-bin/
* chmod +x /var/www/cgi-bin/*
3) copy the SS5Logo.jpg file from source distribution (modules/mod_statistics/statmgr) to your web server images directory (i.e./var/www/html/)  

* cp /opt/ss5/ss5-3.8.9/modules/mod_balance/SS5Logo.jpg /var/www/html/  
4) Enable SS5_CONSOLE option into ss5.conf configuration file and then restart apache web service.  
set SS5_CONSOLE  
ss5 console  

5) Open your web browser followed the following URL to access SS5 Web console.  

http://xx.xx.xx.xx/cgi-bin/statmgr.cgi      
http://xx.xx.xx.xx/cgi-bin/balamgr.cgi    
Here is the SS5 Main menu you will see in your web browser.    
### ss5 statmgr  

When you click on the 'CONNECT counters', you will see stats about CONNECT command counters as shown in the image below.  

### connect counter

Similarly when you click on the Authentication counter, you will stats about the successful, failed, total and normal authentication counts.   

### auth counter

Conclusion   
That’s all, we have successfully implemented SOCKS5 Proxy Server Using SS5 on CentOS 7. As a proxy server, SS5 authenticates, profiles and processes network requests for clients. It establishes connections to application hosts for client applications. When the client attempts to access the network, the client connects to the SS5 daemon instead of the application host. When the clients request that SS5 perform network activities for the client the activities might includes ‘Connection’, ‘Bind’ and Udp Associated authentication. The SS5 protocol is independent of application protocols, and can assist with different networking services, including telnet, ftp, finger, whois, gopher, and WWW access.  
