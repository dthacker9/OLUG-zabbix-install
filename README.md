# OLUG-zabbix-install

This is instructions for installing the [Zabbix](https://www.zabbix.com) on Rocky Linux.  
The current versions used in this document are:
 - Operating System:  Rocky Linux 8.6
 - Zabbix 	   :  6.2 LTS
 - MariaDB         :  10.6
 - PHP:       : 7.4

Hardware Requirements for this document:
 - CPU: 8 Core
 - Memory: 8 GB
 - Disk Space : 500GB

Zabbix can run on less hardware resources.  See the documentation. 
These instruction are assuming you are logged in as root.  --

# These instructions are intended for using Zabbix in a home lab. You may want to make your server more secure than this.

---
 1. Install LAMP on Rocky Linux 8.6
    - Install Apache, enable the Apache service, verify that it's running
    ```
    sudo dnf install @httpd 
    sudo systemctl enable --now httpd
    sudo systemctl status httpd
    ```
 2. Open the firewall to allow web traffic
    - Add http, https to allowed services and reload
    ```
    firewall-cmd --zone=public --add-service=http  --permanent
    firewall-cmd --zone=public --add-service=https --permanent
    firewall-cmd --zone=public --add-port=10050/tcp --permanent
    firewall-cmd --zone=publix --add-port=10051/tcp --permanent
    firewall-cmd --reload
    firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens18
  sources:
  services: cockpit dhcpv6-client http https ssh
  ports:
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

3. Install MariaDB
   - Create the MariaDB repo file 
    ```
    vi /etc/yum.repos.d/mariadb.repo
    ```
   - put this lines into mariadb.repo
     ```
     [mariadb]
     name = MariaDB
     baseurl = http://yum.mariadb.org/10.6/rhel8-amd64
     gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
     gpgcheck=1
     module_hotfixes=1
     ```
   - install MariaDB server and friends
     ```
     dnf install MariaDB-server MariaDB-client MariaDB-backup
     ```
   - Start up MariaDB server and check status
     ```
     systemctl enable --now mariadb
     systemctl status mariadb
     ```
   - Run the mariadb secure installation script (Save the MariaDB root password in a safe place)
     ```  
     /usr/bin/mariadb-secure-installation
     ```
   - Check root login
     ```
     mysql -u root -p
     ```
 4. Install PHP 
    - Install PHP with a minumum version of 7.4 along with other needed utilities
      ```
       dnf module install php:7.4
       dnf install php php-curl php-fpm php-mysqlnd
      ```
    - Enable the php-fm service with auto-start and verify status
      ```
      systemctl enable --now php-fpm
      systemctl status php-fpm
      ````
5. Install the Zabbix server and front End
   - Add the Zabbix repository
     ```
     rpm -Uvh https://repo.zabbix.com/zabbix/6.2/rhel/8/x86_64/zabbix-release-6.2-1.el8.noarch.rpm
     dnf clean all
     ```
   - Switch PHP version (redundant?)
     ```
     dnf module switch-to php:7.4
     ```
   - Install the Zabbix server, front end, and setup scripts
     ```
     dnf install zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf zabbix-sql-scripts zabbix-selinux-policy zabbix-agent
     ```
6. Create and configure Zabbix databas.
   - Create the database and zabbix database user:
   ```
   mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 10
Server version: 10.6.9-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> create database zabbix character set utf8mb4 collate utf8mb4_bin;
Query OK, 1 row affected (0.001 sec)

MariaDB [(none)]> create user zabbix@localhost identified by 'yourpassword';
Query OK, 0 rows affected (0.093 sec)

MariaDB [(none)]>grant all privileges on zabbix.* to zabbix@localhost;
Query OK, 0 rows affected (0.077 sec)

MariaDB [(none)]> set global log_bin_trust_function_creators = 1;
Query OK, 0 rows affected (0.001 sec)

MariaDB [(none)]> quit;
```
- Import the initial schema and data. You will be prompted for your newly created Zabbix database password.
  ``` zcat /usr/share/doc/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
  ```
  time passes...
- Disable log_bin_trust_function_creator after importing the database schema.
  ```
  # mysql -uroot -p
  password
  mysql> set global log_bin_trust_function_creators = 0;
  mysql> quit;
- Configure the zabbix database password in the server
  ```
  vi /etc/zabbix/zabbix_server.conf
   
  DBPassword=password
  ```
- Restart the Zabbix Server and Agent
  ```
  # systemctl restart zabbix-server zabbix-agent httpd php-fpm
  # systemctl enable zabbix-server zabbix-agent httpd php-fpm
  ```
  Browse to http://your-name-or-ip-here/zabbix
  
7. Perform intial Setup (You're nearly there....)
8. Set your language and press "Next Step"
9. Check all prerequisites.  If they all are "OK" then press "Next step"
10. Configure DB Connection: take all the defaults fill in your Zabbix database password.  "Next step"
11. Settings: Set your server name. This is the name that you will point all the host's agents to.  Set your time zone. 
12. Verify the summary is correct.  Next Step
13. Congratulations!! You did it!  Enjoy the beverage of your choice!
14.The inital login is User: Admin  Password: zabbix.   You need to change that now!
15. Go forth and monitor!

Patches welcome.
dthacker9@gmail.com

