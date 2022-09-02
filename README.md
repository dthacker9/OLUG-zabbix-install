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

---
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
    ```
    
    - Enable Apache and run it at startup. 
    ```
    sudo systemctl enable --now httpd
    ```
    
    - Check that apache is running
    ```
    sudo systemctl status httpd`
    ```
2.  Install MariaDB on Rocky Linux 8.6
    -
      
    
      - Create zabbix user
    
   
