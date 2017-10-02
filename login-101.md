<h2> Getting started </h2>

* To access the cluster you will need to log into one of the following login nodes.

 - eofe4.mit.edu   running centos 6
 - eofe5.mit.edu   running centos 6
 - eofe7.mit.edu   running centos 7


<h2> LOGGING IN WITH SSH ON WINDOWS</h2>

1. Download PuTTY [https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html] and run the executable.
2. In the Session tab, enter the remote host (one of the login nodes listed above) and select SSH as the connection type.
3. In the Auth tab (located in SSH), browse for and select your private key
4. Click Open and enter your login credentials (username and passphrase generated at account request.)


<h2> LOGGING IN WITH SSH ON LINUX/MAC </h2>

We have included a config file to streamline the process of logging into the cluster, 
Extract the .zip from account creation into your home (~) directory.

> unzip /path/to/file.zip -d ~

Logging into Centos 6 environment 

>  ssh -F ~/engaging-key/linux/config eofe4.mit.edu

 OR

>  ssh -F ~/engaging-key/linux/config eofe5.mit.edu    

Logging into Centos 7 environment

>  ssh -F ~/engaging-key/linux/config eofe7.mit.edu   

<h2> Troubleshooting </h2>

1. Login to the cluster is via SSH key only, if you forget your SSH key passphrase or lose your keys then you will need to generate a new set of SSH keys and send the public portion (id_rsa.pub) to engaging-admin@techsquare.com

2. If you are logging in from a different computer you will need to either copy your private key (engaging-key) generated at account creation to the other computer, or you will need to generate another set of keys and send them to engaging-admin@techsquare.com
 

