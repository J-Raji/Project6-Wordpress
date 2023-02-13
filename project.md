# Web Solution with WordPress

**Create a RedHat Instaance**

![Redhat Instance](./Images/Red.png)

`ssh -i "MyWordPress-key.pem" ec2-user@ec2-52-201-227-159.compute-1.amazonaws.com`

-Set ec2-user as user

![ec2-user](./Images/user.png)

## Launch an EC2 Instance-Webserver

1.Prepare a Web Server
- [] Create 3 volumes in the same AZ(each of 10GiB)
- 
![EBS Volume 1 added](./Images/vol1.png)

![EBS Volume 2 added](./Images/vol2.png)

![EBS Volume 3 added](./Images/vol3.png)

2.Confirm
`lsblk`

![EBS Volume  confirmed](./Images/stat.png)

3.`df -h`

![all available mount](./Images/mt.png)

4.
-[]`sudo gdisk /dev/xvdf`
-[]`sudo gdisk /dev/xvdg`
-[]`sudo gdisk /dev/xvdh`
-[]type p
-[]next n with 8e00
-w
-y

-[]`lsblk`

![xvdf1 created](./Images/xvdf1.png)

![xvdg1 created](./Images/xvdg1.png)

![xvdh1 created](./Images/xvdh1.png)

5.Install lvm2
[]`sudo yum install lvm2`

![lvm2 installed](./Images/lvm2.png)

[]`sudo lvmdiskscan`

![available partitions](./Images/lvmdiskscan.png)

6.to mark as physical volumes
[]`sudo pvcreate /dev/xvdf1`
[]`sudo pvcreate /dev/xvdf1`
[]`sudo pvcreate /dev/xvdf1`

![physical volumnes xvdf1,xvdg1,xvdh1 created](./Images/pvcreate.png)

8.Verify
[]`sudo pvs`

![pv confirmed](./Images/pvs.png)

9. Add all 3 volumes to volume group VG webdata-vg
[]`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

![vgcreate done](./Images/vgcreate.png)

10.Verify
[]`sudo vgs`

![vgs confirmed](./Images/vgs.png)

11.lvcreate utility to create 2 logical volumes(apps-lv and log-lv)
[]`sudo lvcreate -n apps-lv -L 14G webdata-vg`

![apps-lv confirmed](./Images/apps-lv.png)

[]`sudo lvcreate -n logslv -L 14G webdata-vg`

![logs-lv confirmed](./Images/logs-lv.png)

12.Confirm Logical volumes
[]`sudo lvs`

![lvs confirmed](./Images/lvs.png)

13.Verify entire setup
[]`sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![Vg and Lv details](./Images/Vg-Lv.png)

![pv details](./Images/pv.png)

[]`sudo lsblk`

![lsblk details with no mount points](./Images/sudo-lsblk.png)

14.Format the LV with ext4 filesystem
[]`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

![apps-lv formatted](./Images/mkfs-apps-lv.png)

[]`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![logs-lv formatted](./Images/mkfs-logs-lv.png)

15.Create /var/www/html/ to store website files
[]`sudo mkdir -p /var/www/html`

16.Create /home/recovery/logs to store backup of log data
[]`sudo mkdir -p /home/recovery/logs`

17.Mount /var/www/html on apps-lv logical volume
[]`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

18.Back up all files in /var/log to /home/recovery/logs
[]`sudo rsync -av /var/log/. /home/recovery/logs/`

![backups](back.png)

19.Mount /va/log on logs.Iv
[]`sudo mount /dev/webdata-vg/logs-lv /var/log`

20.Restore logs files back into /var/log/directory
[]`sudo rsync -av /home/recovery/logs/. /var/log`

![rsync](./Images/rsync.png)

21.Update /etc/fstab file so that the mount configuration will persist after restart of the server
[]`sudo blkid`

![blkid](./Images/blkid.png)

[]`sudo vi /etc/fstab`

- Include 
>UUID=23b081ee-a99f-489e-9cd8-fc80aafd0d36 /var/log ext4 defaults 0 0
UUID=31c3c22f-1224-4e30-b144-514bc9a6de26 /var/www/html ext4 defaults 0 0

### Step 1

1.Test the configuration and reload the daemon
`sudo mount -a`
`sudo systemctl daemon-reload`

2.Verify setup
`df -h`

![setup](./Images/setup.png)

### Step 2

**Create a RedHat-DB Instaance**

![Redhat-DB Instance](./Images/Red1.png)

`ssh -i "MyWordPress-key.pem" ec2-user@ec2-54-210-11-143.compute-1.amazonaws.com`

-Set ec2-user as user

![ec2-user](./Images/user1.png)

## Launch an EC2 Instance-Webserver

1.Prepare a Web Server
-Create 3 volumes in the same AZ(each of 10GiB)

![EBS Volume 1 added](./Images/voldb1.png)

![EBS Volume 2 added](./Images/voldb2.png)

![EBS Volume 3 added](./Images/voldb3.png)

3.Confirm
`lsblk`

![EBS Volume  confirmed](./Images/stat1.png)

4.`df -h`

![all available mount](./Images/mt1.png)

5.`sudo gdisk /dev/xvdf`
`sudo gdisk /dev/xvdg`
`sudo gdisk /dev/xvdh`
-type p
-next n with 8e00
-w
-y

`lsblk`

![xvdf1 created](./Images/xvdf-1.png)

![xvdg1 created](./Images/xvdg-1.png)

![xvdh1 created](./Images/xvdh-1.png)

6.Install lvm2
`sudo yum install lvm2`

![lvm2 installed](./Images/lvm-2.png)

`sudo lvmdiskscan`

![available partitions](./Images/lvmdiskscan1.png)

7.to mark as physical volumes
`sudo pvcreate /dev/xvdf1`
`sudo pvcreate /dev/xvdf1`
`sudo pvcreate /dev/xvdf1`

![physical volumnes xvdf1,xvdg1,xvdh1 created](./Images/pvcreate1.png)

8.Verify
`sudo pvs`

![pv confirmed](./Images/pvs1.png)

9.Add all 3 volumes to volume group VG webdata-vg
`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

![vgcreate done](./Images/vgcreate1.png)

10.Verify
`sudo vgs`
![vgs confirmed](./Images/vgs1.png)

11.lvcreate utility to create 2 logical volumes(db-lv and log-lv)
`sudo lvcreate -n db-lv -L 14G webdata-vg`

![db-lv confirmed](./Images/db-lv.png)

`sudo lvcreate -n logs-lv -L 14G webdata-vg`

![logs-lv confirmed](./Images/logslv.png)

**Blocker to rename /dev/webdata-vg/logslv to /dev/webdata-vg/logs-lv
`sudo lvrename /dev/webdata-vg/logslv /dev/webdata-vg/logs-lv`

12.Confirm Logical volumes
`sudo lvs`

![lvs confirmed](./Images/lvs1.png)

13.Verify entire setup
`sudo vgdisplay -v #view complete setup - VG, PV, and LV`

![Vg and Lv details](./Images/Vg-Lv1.png)

![pv details](./Images/Pv1.png)

`sudo lsblk`

![lsblk details with no mount points](./Images/sudo-lsblk1.png)

14.Format the LV with ext4 filesystem
[]`sudo mkfs -t ext4 /dev/webdata-vg/db-lv`

![db-lv formatted](./Images/mkfs-db-lv.png)

[]`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![logs-lv formatted](mkfs-logs-lv1.png)

15.Create /db to store website files
`sudo mkdir -p /db`

16.Create /home/recovery/logs to store backup of log data
`sudo mkdir -p /home/recovery/logs`

17.Mount /db on db-lv logical volume
`sudo mount /dev/webdata-vg/db-lv /db`

18.Back up all files in /var/log to /home/recovery/logs
`sudo rsync -av /var/log/. /home/recovery/logs/`

![backups](./Images/back1.png)

19.Mount /va/log on logs.Iv
`sudo mount /dev/webdata-vg/logs-lv /var/log`

20.Restore logs files back into /var/log/directory
`sudo rsync -av /home/recovery/logs/. /var/log`

![rsync](./Images/rsync1.png)

21.Update /etc/fstab file so that the mount configuration will persist after restart of the server
`sudo blkid`

![blkid](./Images/blkid1.png)

`sudo vi /etc/fstab`

- Include 
>UUID=2ee12954-887d-4a82-8905-4ce8b79c1dcc /var/log ext4 defaults 0 0
UUID=5284d287-762b-477d-813c-b970348c5f37 /db ext4 defaults 0 0

### continue

1.Test the configuration and reload the daemon
`sudo mount -a`
`sudo systemctl daemon-reload`

2.Verify setup
`df -h`

![setup](./Images/setup1.png)

### Step 3- Web Server

1.update 
`sudo yum -y update`

![webserver updated](./Images/update.png)

2.Install wget, apache and its dependencies
`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

![wget,apache installed](./Images/wget-apache.png)

3.Start Apache
 `sudo systemctl enable httpd`
`sudo systemctl start httpd`

![systemctl enabled](./Images/systemctl.png)

4.Install PHP and it’s depemdencies
`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

![php installed](./Images/php.png)

`sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

![utils](./Images/utils.png)

`sudo yum module reset php`
`sudo yum module enable php:remi-7.4`
`sudo yum install php php-opcache php-gd php-curl php-mysqlnd`

![mysqlnd](./Images/mysqlnd.png)

`sudo systemctl enable php-fpm`
`setsebool -P httpd_execmem 1`

![setsebool]](./Images/setsebool.png)

5.Restart Apache
`sudo systemctl restart httpd

6.Download wordpress and copy word press to
var/www/html
`mkdir wordpress`
`cd   wordpress`
`sudo wget http://wordpress.org/latest.tar.gz`

![wordpress created](./Images/wordpress.png)

`sudo tar xzvf latest.tar.gz`
`sudo rm -rf latest.tar.gz`
`sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php`
`sudo cp -R wordpress /var/www/html/`

7.Configure SELinux Policies
`sudo chown -R apache:apache /var/www/html/wordpress`
`sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`
`sudo setsebool -P httpd_can_network_connect=1`

![setsebool network created](./Images/vset-net.png)

### Step 4-Install MySQL on your DB server

`sudo yum update
`sudo yum install mysql-server

![mysql-server installed](./Images/mysql-server.png)

-Verify
`sudo systemctl restart mysqld`
`sudo systemctl enable mysqld`

![mysql-server systemctl ](./Images/mysql-systemctl.png)

### Step 5- configure DB to work with wordpress

`sudo mysql`
>CREATE DATABASE wordpress;`
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';

![myuser created](myuser.png)

GRANT ALL ON wordpress.* TO 'myuser'@'172.31.89.236';

![myuser granted](grant-myuser.png)

FLUSH PRIVILEGES;

![flushed](flush.png)

SHOW DATABASES;
exit
![show wordpress included ](show-wordpress.png)

-Configure MySQL server to allow connections from remote hosts
`sudo vi /etc/my.cnf/mysql.conf.d/mysqld.cnf`
`sudo vi /etc/my.cnf.d`
- Replace '127.0.0.1' to '0.0.0.0'
bind-address    =0.0.0.0

![Modify Bind Address](bind.png)

`sudo vi wp-config.php`
<?php

define( 'DB_NAME', 'wordpress');
define( 'DB_USER', 'myuser');
define( 'DB_PASSWORD', 'mypass');
define( 'DB_HOST','172.31.80.7');
define( 'DB_CHARSET', 'utf8');
?>

-disable the Apache otherwise the site won't be visible
`sudo systemctl disable httpd `

### Step 6-Configure Wordpress to connect to remote database
 
`sudo systemctl restart mysqld`

`sudo yum install mysql`

![Web installed with Mysql-client ](./Images/mysql-client.png)

sudo mysql -u myuser -p -h 172.31.80.7

![myuser able to access mysql ](./Images/myuser-log.png)

`show databases;`

![databases confirmed ](./Images/wordpress-databases.png)

###Configuring wp-config.php
`sudo wget https://wordpress.org/latest.tar.gz`
`sudo tar -xvf latest.tar.gz`
`sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php`
`sudo vi wordpress/wp-config.php`

![edit wp-config.php ](./Images/wp-config.png)

- copy
`sudo cp -R wordpress /var/www/html/`
-assign permissions
`sudo chown -R apache:apache /var/www/html/wordpress`
`sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`
`sudo chmod -Rf 775  /var/www/html`
`sudo vi /etc/httpd/conf.d/wordpress.conf`
-copy and paste
<VirtualHost *:80>
ServerAdmin myuser@18.204.218.183
DocumentRoot /var/www/html/wordpress

<Directory "/var/www/html/wordpress">
Options Indexes FollowSymLinks
AllowOverride all
Require all granted
</Directory>

ErrorLog /var/log/httpd/wordpress_error.log
CustomLog /var/log/httpd/wordpress_access.log common
</VirtualHost>

---
-restart
`sudo systemctl restart httpd`

Blocker found -unable to load address on browser

[Link to Wordpress](http://3.95.147.104/wordpress/)
