# **Code Review**

<img src="https://avatars.githubusercontent.com/u/118199?s=200&v=4M" 
width="180" height="180" />

# Review Board

A tool for reviewing source code, documentation and other text-based files. It offers a powerful web-based interface with broad browser support for managing review requests and reviewing code, as well as command line tools to simplify the review request submission process.

>Access This From Website [ReviewBoard Installation Process](https://i-am-ghost.github.io/ReviewBoard/)

>Links For Reference

1. [**Wikipedia**](https://en.wikipedia.org/wiki/Review_Board)

2. [**ReivewBoard Official Site**](https://www.reviewboard.org/)

---

## **Installation Steps**

### **📌 Host Machine Setup**

Server will be in distributed in network/ lab machines. Where all the components are reachable and network administrator can deploy a virtual/ physical host to config it to host reeviewboard in network

✅This host machine **should be deployed in netowrk 24x7, 365 days, to maintain high availability** to rasie reviews by developers any time

✅ Choose a network from where clients can access this reviewbaord server when hosting is completed, make sure this machine is reachable by **FQDN** like this : **hostname.domain.subdoamin**
> To check reachability launch CLI from any client and fire ping command like this :

``` bash
ping hostname.domain.subdoamin
```

✅ Access the host via ssh as a **root** user & Update your server machine as a root, for the first time.

```bash
ssh root@hostname.domain.subdoamin
sudo yum -y install epel-release
sudo yum update -y
```

✅ Reboot Your Host from

```bash
sudo shutdown -r now
```

### **📌 Installing Packages in Host Machine**

Download the required packages to setup ReviewBoard in the targeted host machine

✅ Download ReviewBoard, Memcached And Install it

```bash
sudo yum -y install ReviewBoard memcached httpd openssl 
sudo yum -y install mod_ssl mod_wsgi
```

✅ Start httpd demon processs in the host

```bash
sudo systemctl enable httpd
sudo systemctl start httpd
```

✅ Download , MariaDB, MariaDB Server Packages And Install It

```bash 
sudo yum -y install mariadb mariadb-server
```

### **📌 Configuring Database**

✅ Edit the default MariaDB configuration file using the following command.

```bash
sudo nano /etc/my.cnf
```

✅ Add the following lines at the bottom of this **/etc/my.cnf**, so that the server is configured to use the UTF-8 encoding for text.
```bash
[client]
default-character-set=utf8
[mysqld]
character-set-server=utf8
```
✅ Once MariaDB is installed, run the following command to start MariaDB and enable it to automatically start at boot time using following commands.

```bash
sudo systemctl start mariadb.service
sudo systemctl start mariadb.service
```

✅ Now secure your MariaDB installation using the following command.

```bash
sudo mysql_secure_installation
```

**NOTE**

>You will be asked for current **root password**. As we have just install MariaDB, root password is not set. Press Enter key to proceed. Set a strong root password for your MySQL server and answer **Y** for all the other questions asked. All the questions asked are self explanatory. You can see the example from below.
```bash
    Set root password? [Y/n] n
    Remove anonymous users? [Y/n] y
    Disallow root login remotely? [Y/n] n
    Remove test database and access to it? [Y/n] y
    Reload privilege tables now? [Y/n] y
```
✅ MariaDB is secured, login to MariaDB shell with the following command. **Enter your root password when promted.**

```bash
mysql -u root -p
```

✅ Create a database for Review Board with the following command.

```bash
MariaDB [(none)]>CREATE DATABASE reviewdb;
```

✅ Create a username and password for Review Board with the following command

```bash
MariaDB [(none)]>CREATE USER 'reviewuser'@'localhost' IDENTIFIED BY 'password';
```

✅ Grant privileges to the Review Board database with the following command.

```bash
MariaDB [(none)]>GRANT ALL PRIVILEGES ON reviewdb.* TO 'reviewuser'@'localhost';
```

✅ You will need to run the FLUSH PRIVILEGES command so that the privileges table will be reloaded by MariaDB and we can use new credential

```bash
MariaDB [(none)]>FLUSH PRIVILEGES;
```

✅ Exit from the MariaDB console with the following command

```bash
MariaDB [(none)]>\q
```

> Once you are done, you can proceed to install Review Board. Please do not forgot to add ";" at the end of each SQL statements.  

### **🌐 Configure Review Board**

✅ rb-site is a binary to start ReviewBoard **Django** site and manage it

```bash
sudo rb-site install /var/www/hostname.domainname.subdomainname

#### Example : sudo rb-site /var/www/cent7.testdomain.com
####           Host Name FQDN ~ cent7.testdomain.com

        Domain Name: cent7.testdomain.com
        Root Path [/]:Press Enter
        Database Type: 1                      #Enter 1 for MySQL
        Database Name [reviewboard]: reviewdb
        Database Server [localhost]:          #Press enter to use default
        Database Username: reviewuser
        Database Password: password
        Memcache Server [localhost:11211]:    #Press enter to use default
        Username [admin]: siteadmin
        Password: your-password
        E-Mail Address: admin@testdomain.com
```
### **💻 Configure The Host Machine**

✅ Once you are finished, set the ownership of the Review Board files to the apache user using the following command

```bash
sudo chown -R apache:apache /var/www/cent7.testdomain.com
```

✅ Create a symbolic link for the Apache configuration file using the following command.

```bash
sudo ln -s /var/www/cent7.testdomain.com/conf/apache-wsgi.conf /etc/httpd/conf.d/cent7.testdomain.com.conf
```

✅ Next, restart Apache services with the following command.

```bash
sudo systemctl restart httpd
```

✅ Now start Memcached service and enable it to start at boot time using following commands.

```bash
sudo systemctl start memcached
sudo systemctl enable memcached
```

✅ Before accessing the Review Board web interface, you will need to allow HTTP service through firewall. You can do this by running the following command

```bash
sudo firewall-cmd --zone=public --permanent --add-service=http
sudo firewall-cmd --reload
```

### **🏆 Conclusion**

Once you are done, open your web browser and navigate the URL **http://cent7.testdomain.com** to access the Review Board Site from local client.

>**Congratulations! you have successfully installed Review Board on CentoS 7 server. You can now easily deploy it for your developers.**

---

## **Major Dependencies**

<img src="https://www.centos.org/assets/img/centos-logo-white.png"
width="130" height="35" /> <img src="https://www.python.org/static/img/python-logo@2x.png" width="130"
height="40" />


***

## FAQs

>What to do when Site is not up in browser, permission issue and throwing error?

Your review-board site might not be configured properly. Access the MySql Database as root. And delete the Mariadb SQL database using SQL query. Delete the review user, and delete the review-board site django folder **"/var/www/reviewboard.site.com/"** , which just now you created while installing. Remove the soft-link also...

Restart all the respected services.

And now start installation of mariadb using secure installation. And follow the steps again exactly as it mentioned above.


>What to do if Not able to access the admin console, of deployed review-board site ?

Access the host machine from which review-board website hosted as a **root** and then run this command to create a new superuser.
```bash
 rb-site manage /var/www/hostname.domainname.subdomainname createsuperuser
```
[Link For Reference](https://www.reviewboard.org/docs/manual/3.0/admin/sites/management-commands/) 

***

