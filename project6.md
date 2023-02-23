##  **PROJECT 6 - WEB SOLUTION WITH WORDPRESS**

## STEP 1 – PREPARATION OF WEB-SERVER


### WEB-server instance created and EBS volumes were attached

![web-server-volume](./images/web-server-volume.PNG)

### lsblk command was used to confirmed the attached volumes to the web-server

`lsblk`

![checking-presence-of-disks](./images/checking-presence-of-disks.PNG)

### Confirming the mounts and free space on my web-server

`df -h`

![confirming-mounting-point](./images/confirming-d-mountpoint-available.PNG)

### Creation of the partitions on the disks earlier created

`sudo gdisk /dev/xvdf`

`sudo gdisk /dev/xvdg`

`sudo gdisk /dev/xvdh`

![sudo-gdisk](./images/sudo-gdisk.PNG)
![sudo-gdisk](./images/sudo-gdisk2.PNG)
![sudo-gdisk](./images/sudo-gdisk3.PNG)

![db-partion-table-created](./images/db-partion-table-created.PNG)

![db-partion2-table-created](./images/db-partion2-table-created.PNG)

![db-partion3-table-created](./images/db-partion3-table-created.PNG)

### Using lsblk utility to confirm the newly configured partitions on te disk

`lsblk`

![confirming-partition-created](./images/confirming-partition-created.PNG)

### Installation of lvm2

`sudo yum install lvm2`

![db-lvm2-installation](./images/db-lvm2-installation.PNG)

![db-lvm2-installation1](./images/db-lvm2-installation1.PNG)

### Physical volumes creation

`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![db-dev-created](./images/db-dev-created.PNG)

### Verifying physical volumes created

`sudo pvs`

![confirming-physical-vol-created](./images/confirming-physical-vol-created.PNG)

### Using vgcreate utility to add PVs created to a volume group called webdata-vg

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

`sudo vgs`

![vg-creation](./images/vg-creation.PNG)
![confirming-vg-creation](./images/confirming-vg-creation.PNG)

### Using lvcreate utility to create logical volume

`sudo lvcreate -n apps-lv -L 14G webdata-vg`

`sudo lvcreate -n logs-lv -L 14G webdata-vg`

![logical-volue-creation](./images/logical-volue-creation.PNG)

![logical-volume-creation](./images/logical-volume-creation.PNG)

### Verifying logical volume created

`sudo lvs`

![web-sudo-lvs](./images/web-sudo-lvs.PNG)

### Verifying the set up so far

`sudo vgdisplay -v`

`sudo lsblk`

![web-sudo-vgdisplay](./images/web-sudo-vgdisplay.PNG)

![web-sudo-lsblk](./images/web-sudo-lsblk.PNG)

### Creation of filesystem

`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![filesystem-creation](./images/filesystem-creation.PNG)

![filesystem-creation](./images/filesystem-creation-4-logslv.PNG)

### Creation of /var/www/html directory to store website files

`sudo mkdir -p /var/www/html`

![web-sudo-mkdir](./images/web-sudo-mkdir.PNG)

### Creation of /home/recovery/logs to store backup of log data

`sudo mkdir -p /home/recovery/logs`

![web-sudo-mkdir](./images/web-sudo-mkdir.PNG)

### Mounting on logical volumes created earlier

`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

![web-sudo-mkdir](./images/web-sudo-mkdir.PNG)

### Using rsync utility to backup all the file in the log directory /var/log into /home/recovery/logs, which is required before mounting the file system

`sudo rsync -av /var/log/. /home/recovery/logs/`

![backing-up-files-in-log-directory](./images/backing-up-files-in-log-directory.PNG)

### Mounting /var/log on logs-lv logical volume which will cause contents on /var/log to be deleted and the reason for the preceeding step to backup its content

`sudo mount /dev/webdata-vg/logs-lv /var/log`

![logs-mounting](./images/logs-mounting.PNG)

### Restoring log files back into /var/log directory

`sudo rsync -av /home/recovery/logs/. /var/log`

![backing-up-files-in-log-directory1](./images/backing-up-files-in-log-directory1.PNG)

### Updating /etc/fstab file so that the mount configuration will persist after restart of the server


### Firstly, checking for the block ID

`sudo blkid`

`sudo vi /etc/fstab`

![sudo-blkid](./images/sudo-blkid.PNG)

![backing-up-files-in-log-directory1](./images/mount-a-rtn-noerr.PNG)

![vi-etc-fstab](./images/vi-etc-fstab.PNG)

### Testing the configuration and daemon-reload

`sudo mount -a`

`sudo systemctl daemon-reload`

![backing-up-files-in-log-directory1](./images/mount-a-rtn-noerr.PNG)

![reload-daemon](./images/reload-daemon.PNG)

## STEP 2 – PREPARATION OF DATABASE-SERVER

### DATABASE-server instance created and EBS volumes were attached

![db-volume](./images/db-vol.PNG)

### lsblk command was used to confirmed the attached volumes to the db-server

`lsblk`

![confirming-dbpartion-created](./images/confirming-dbpartion-created.PNG)

### Confirming the mounts and free space on my database-server

`df -h`

![db-df-h](./images/db-df-h.PNG)

### Creation of the partitions on the disks earlier created

`sudo gdisk /dev/xvdf`

`sudo gdisk /dev/xvdg`

`sudo gdisk /dev/xvdh`

![db-partion-table-created](./images/db-partion-table-created.PNG)

![db-partion2-table-created](./images/db-partion2-table-created.PNG)

![db-partion3-table-created](./images/db-partion3-table-created.PNG)

### Using lsblk utility to confirm the newly configured partitions on the disk

`lsblk`

![confirming-dbpartion-created](./images/confirming-dbpartion-created.PNG)

### Installation of lvm2

`sudo yum install lvm2`

![db-lvm2-installation](./images/db-lvm2-installation.PNG)

![db-lvm2-installation1](./images/db-lvm2-installation1.PNG)

### Physical volumes creation

`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

![db-dev-created](./images/db-dev-created.PNG)

### Verifying physical volumes created

`sudo pvs`

![confirming-physical-vol-created](./images/confirming-physical-vol-created.PNG)

### Using vgcreate utility to add PVs created to a volume group called webdata-vg

`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`

`sudo vgs`

![db-vg-create](./images/db-vg-create.PNG)
![db-sudo-vgs](./images/db-sudo-vgs.PNG)

### Using lvcreate utility to create logical volume

`sudo lvcreate -n db-lv -L 20G vg-database`

![db-logicalvol-creation](./images/db-logicalvol-creation.PNG)

### Verifying logical volume created

`sudo lvs`

![db-sudo-lvs](./images/db-sudo-lvs.PNG)

### Creation of mount point

`sudo mkdir /db`

![db-mount-creation](./images/db-mount-creation.PNG)

### Verifying the set up so far

`sudo vgdisplay -v`

`sudo lsblk`

![web-sudo-vgdisplay](./images/web-sudo-vgdisplay.PNG)

![web-sudo-lsblk](./images/web-sudo-lsblk.PNG)

### Creation of filesystem

`sudo mkfs.ext4 /dev/vg-database//db-lv`

![db-filesystem-creation](./images/db-filesystem-creation.PNG)

### Mounting on db

`sudo mount /dev/vg-database/db-lv /db`

![db-mount](./images/db-mount.PNG)

### Confirming the mounting

`df -h`

![db-updated-fstab](./images/db-updated-fstab.PNG)


### Updating /etc/fstab file so that the mount configuration will persist after restart of the server

### Firstly, checking for the block ID

`sudo blkid`

`sudo vi /etc/fstab`

![db-vi-fstab](./images/db-vi-fstab.PNG)

![db-vi-fstab-edited](./images/db-vi-fstab-edited.PNG)

### Testing the configuration and daemon-reload

`sudo mount -a`

`sudo systemctl daemon-reload`

![db-confirming-fstab-noerr](./images/db-confirming-fstab-noerr.PNG)

![db-sudo-daemon-reload](./images/db-sudo-daemon-reload.PNG)

## STEP 3 – INSTALLATION OF WORDPRESS ON WEB-SERVER

### Updating the repository

`sudo yum -y update`

![web-yum-update](./images/web-yum-update.PNG)
![web-yum-update](./images/web-yum-update1.PNG)
![web-yum-update](./images/web-yum-update2.PNG)
![web-yum-update](./images/web-yum-update3.PNG)
![web-yum-update](./images/web-yum-update4.PNG)
![web-yum-update](./images/web-yum-update5.PNG)
![web-yum-update](./images/web-yum-update6.PNG)
![web-yum-update](./images/web-yum-update7.PNG)
![web-yum-update](./images/web-yum-update8.PNG)
![web-yum-update](./images/web-yum-update9.PNG)
![web-yum-update](./images/web-yum-update10.PNG)
![web-yum-update](./images/web-yum-update11.PNG)
![web-yum-update](./images/web-yum-update12.PNG)
![web-yum-update](./images/web-yum-update13.PNG)
![web-yum-update](./images/web-yum-update14.PNG)
![web-yum-update](./images/web-yum-update15.PNG)
![web-yum-update](./images/web-yum-update16.PNG)
![web-yum-update](./images/web-yum-update17.PNG)
![web-yum-update](./images/web-yum-update18.PNG)
![web-yum-update](./images/web-yum-update19.PNG)
![web-yum-update](./images/web-yum-updateLast.PNG)

### Installing wget, Apache and it's dependencies

`sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

![web-json](./images/wiget-apache-installation.PNG)

### Enabling and starting Apache

`sudo systemctl enable httpd`

`sudo systemctl start httpd`

![sudo-systemctl-enable2](./images/sudo-systemctl-enable2.PNG)

![sudo-systemctl-start1](./images/sudo-systemctl-start1.PNG)

![apache-test-page](./images/apache-test-page.PNG)

### Installation of PHP and it's dependencies

`sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm`

`sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm`

`sudo yum module list php`

`sudo yum module reset php`

`sudo yum module enable php:remi-7.4`

`sudo yum install php php-opcache php-gd php-curl php-mysqlnd`

`sudo systemctl start php-fpm`

`sudo systemctl enable php-fpm`

`setsebool -P httpd_execmem 1`

![epel-repository-installation-update](./images/epel-repository-installation-update.PNG)

![sudo-remi-installation](./images/sudo-remi-installation.PNG)

![web-module-list-php](./images/web-module-list-php.PNG)

![phpreset](./images/phpreset.PNG)

![enabling-phpremi](./images/enabling-phpremi.PNG)

![sudo-php-curl](./images/sudo-php-curl.PNG)

![sudo-webserver-systemctl](./images/sudo-webserver-systemctl.PNG)

![web-systemclt-enable-php](./images/web-systemclt-enable-php.PNG)

![sudo-setsebool(./images/sudo-setsebool.PNG)

### Restarting apache

`sudo systemctl restart httpd`

![start-httpd](./images/start-httpd.PNG)

### Downloading wordpress, extracting and copying wordpress to var/www/html

`mkdir wordpress`

`cd wordpress`

`sudo wget http://wordpress.org/latest.tar.gz`

`sudo tar xzvf latest.tar.gz`

`sudo rm -rf latest.tar.gz`

`sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php`

`sudo cp -R wordpress /var/www/html/`

![mkdir-wordpress](./images/mkdir-wordpress.PNG)

![cd-into-wordpress](./images/cd-into-wordpress.PNG)

![wordpress-download](./images/wordpress-download.PNG)

![wordpress-extraction](./images/wordpress-extraction.PNG)

![cp-wp-var-www-html](./images/cp-wp-var-www-html.PNG)

![copy-to-config-php](./images/copy-to-config-php.PNG)

### Configuring security-enhanced linux policies for redhat 

`sudo chown -R apache:apache /var/www/html/wordpress`

`sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R`

`sudo setsebool -P httpd_can_network_connect=1`

![start-httpd](./images/web-sudo-chown.PNG)

![web-chcon-t-httpd](./images/web-chcon-t-httpd.PNG)

![web-sudo-setsbool](./images/web-sudo-setsbool.PNG)

![web-chown-to-apache](./images/web-chown-to-apache.PNG)

web-chown-to-apache
## STEP 4  – INSTALLATION OF MYSQL ON DATABASE-SERVER

### MYSQL installation

`sudo yum update`

`sudo yum install mysql-server`

![web-yum-update](./images/web-yum-update.PNG)

![mysql-server-installation](./images/mysql-server-installation.PNG)

### Restart and enable mysqld services and then verify they are up and running

`sudo systemctl restart mysqld`

`sudo systemctl enable mysqld`

`sudo systemctl status mysqld`

![db-restar-mysqld](./images/db-restar-mysqld.PNG)

![web-yum-update](./images/web-enable-mysqld.PNG)

![web-mysqld-status](./images/web-mysqld-status.PNG)

## STEP 5 – CONFIGURATION OF DATABASE-SERVER TO WORK WITH WORDPRESS

### MYSQL installation

`sudo yum update`

`sudo mysql`

![db-sudo-mysql](./images/db-sudo-mysql.PNG)

## STEP 6 – CONFIGURATION OF WORDPRESS TO CONNECT TO REMOTE SERVER

### MYSQL installation

`sudo yum update`

`sudo mysql`

![db-sudo-mysql](./images/db-sudo-mysql.PNG)

### Setting of binding address on database-server

`sudo vi /etc/my.cnf`

![db-vi-etc-mycnf](./images/db-vi-etc-mycnf.PNG)

![db-etc-mycnfd](./images/db-etc-mycnfd.PNG)

![db-start-mysqld](./images/db-start-mysqld.PNG)

### Web-server setting of php config

`sudo vi wp-config.php`

![web-sudo-vi-wp-config-php1](./images/web-sudo-vi-wp-config-php1.PNG)

![web-sudo-vi-wp-config-php](./images/web-sudo-vi-wp-config-php.PNG)

![web-sudo-systemctl-restart](./images/web-sudo-systemctl-restart.PNG)

### De activating the appache test page

`sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup`

![web-renaming-apache-wlcpg](./images/web-renaming-apache-wlcpg.PNG)

### Connecting to the database-server from Web-server

`sudo mysql -h 192.31.61.64 -u wordpress -p`

![login-to-db-frm-web](./images/login-to-db-frm-web.PNG)

### Accessing wordpress link via browser

[wordpress link](https://3.86.13.146/wp-admin/)

![wppage-on-browser](./images/wppage-on-browser.PNG)

![wpinstallation-successful](./images/wpinstallation-successful.PNG)

![wp-page-after-login](./images/wp-page-after-login.PNG)

