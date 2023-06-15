# How to install CACTI without issues

After a long week, I want to publish the easy way to install **cacti** on **centos7**  on a virtual box

# Cacti

First, we need to understand what **cacti** is. Cacti is an open-source tool for monitoring systems (servers and network in real-time) where you can *analyze*, *interpret*, and *control* different env variables by visual charts. 

> First, we need to install **centos7** on your virtual box.

## Installing Cacti

- [ ] Lunch ***centos7*** on your virtual machine
- [ ] Open your terminal
- [ ] Access with your ***user*** and ***password***:
	> localhost login: ****
	> Password: *****

- [ ]  If you see **#**, means you are in
	> [root@localhost ~]# 

- [ ] Modify the time:
	> #rm -f /etc/localtime
	> #ln -s /usr/share/zoneinfo/UTC/etc/localtime
	
- [ ] To validate the date (must be your current date)
	> #date
	> Tue Feb 28 13:37:53 UTC 2017 

- [ ] Now is time to install the packages:
	> #yum install vim
	> #yum install wget 

- [ ] Some required commands to move us on RHEL - **centos**
	> #yum install httpd httpd-devel 
	 > #yum install mysql mysql-server 
	 > #yum install mariadb-server -y
	 > #yum install php-mysql php-pear php-common php-gd php-devel php php-mbstring php-cli
	 > #yum install php-snmp
	 > #yum install net-snmp-utils net-snmp-libs
	 > #yum install rrdtool

- [ ] Once you install **rrdtoll**, start and enable some services
	> #systemctl start httpd.service
    > #systemctl start mariadb.service
    > #systemctl start snmpd.service
    > #systemctl enable httpd.service
    > #systemctl enable mariadb.service
     >#systemctl enable snmpd.service

- [ ] Install epel and cacti
	> #yum install epel-release -y
	> #yum install cacti

- [ ] You need to set a **password** for the MariaDB

	> #mysqeladmin -u root password **** 

- [ ] Access to MariaDB
	> #mysql -u root -p
	> Enter ur password: ****
	
- [ ] Time to create a database
	> #MariaDB [(none)]> create database cacti;
	> #MariaDB [(none)]> GRANT ALL ON cacti.* TO cacti@localhost IDENTIFIED BY 'psswd';
	> #MariaDB [(none)]> FLUSH privileges;
	> #MariaDB [(none)]> quit;

- [ ] Find cacti path
	> #rpm -ql cacti | grep cacti.sql 
	> /usr/share/doc/cacti-1.1.16/cacti.sql
	
- [ ] Set path to the cacti db, **password** is required
	> #mysql -u cacti -p cacti < /usr/share/doc/cacti-1.1.16/cacti.sql
	> Enter ur password: ****

- [ ]  Open your fav editor **i.e. vi**
	> #vi  /etc/cacti/db.php
	
- [ ] Edit the following 
	> $database_type = "mysql";
    > $database_default = "cacti";
    > $database_hostname = "localhost";
    > $database_username = "cacti";
    > $database_password = "****"; // psswd del database
    > $database_port = "3306";
    > $database_ssl = false;

- [ ] Save and quit
	> :wq

- [ ] Firewall reload
	>  #firewall-cmd --permanent --zone=public --add-service =http
	> #firewall-cmd --reload

- [ ] Open your fav edit **vi**
	> #vi /etc/httpd/conf.d/cacti.conf

- [ ] Edit the following
	> < Directory /usr/share/cacti/>
       < IfModule mod_authz_core_c>
      # httpd 2.4
      Require all granted
      < /IfModule>
      <IfModule !mod_auth_core.c>
      # httpd 2.2
      Order deny, allow
      Deny from all
      Allow from all
      < /IfModule>
      < /Directory>
      .....
      <Directory /usr/share/cacti/>
      Order Deny, Allow
      Deny from all
      Allow from 192.168.0.111 // el ip de centOs7
      < /Directory>

- [ ] Save and quit
	> :wq

- [ ] Restart httpd service
	> #systemctl restart httpd.service

- [ ] Create a directory
	> #mkdir -p /var/log/cacti

- [ ] Move to the new directory
	> #cd /var/log/cacti/

- [ ]  Create a file
	> #touch cacti.log

- [ ] Open cacti file
	> #vi /etc/cron.d/cacti

- [ ] Edit 
	> */5 * * * * cacti /usr/bin/php /usr/share/cacti/poller.php > /dev/null 2>&1

- [ ] Save and quit
	> :wq
