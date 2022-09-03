# OLUG-zabbix-install

This is instructions for installing the [Zabbix](https://www.zabbix.com) on Rocky Linux.  
The current versions used in this document are:
 - Operating System:  Rocky Linux 8.6
 - Zabbix 	   :  6.0 LTS
 - MariaDB         :  10.6

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
    # firewall-cmd --reload
    # firewall-cmd --list-all
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
 4. Install PHP













1. Install the Zabbix Repostory
```
# rpm -Uvh https://repo.zabbix.com/zabbix/6.2/rhel/8/x86_64/zabbix-release-6.2-1.el8.noarch.rpm
# dnf clean all
```
2. Switch DNF module version for PHP
```
# dnf module switch-to php:7.4
```
3. Install the Zabbix server, frontend, agent
```
# dnf install zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf zabbix-sql-scripts zabbix-selinux-policy zabbix-agent
```
1. Install LAMP on Rocky Linux 8.6
    - Install Apache
    ```
    sudo dnf install @httpd
    ```  ```
    
    - Enable Apache and run it at startup. 
    ```
    sudo systemctl enable --now httpd
    ```
    
    - Check that apache is running
    ```
    sudo systemctl status httpd
    ```

    
   
