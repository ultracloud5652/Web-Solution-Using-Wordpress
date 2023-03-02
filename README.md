# Web-Solution-Using-Wordpress

WordPress is a free and open-source content management system (CMS) written in hypertext preprocessor (PHP) language and paired with a MySQL or MariaDB database with supported HTTPS. Features include a plugin architecture and a template system, referred to within WordPress as "Themes". WordPress was originally created as a blog-publishing system but has evolved to support other web content types including more traditional mailing lists and Internet fora, media galleries, membership sites, learning management systems (LMS) and online stores. WordPress is used by 42.8% of the top 10 million websites as of October 2021.

To function, WordPress has to be installed on a web server, either part of an Internet hosting service like WordPress.com or a computer running the software package WordPress.org in order to serve as a network host in its own right.

## Three-Tier Architecture

![3 tiers](https://user-images.githubusercontent.com/117458922/222128899-e55486b9-0787-42c3-bc97-c879c567e2fa.png)

A three-tier architecture is a client-server architecture in which the functional process logic, data access, computer data storage and user interface are developed and maintained as independent modules on separate platforms.

Three-tier architecture is a software design pattern and a well-established software architecture.

Three-tier architecture allows any one of the three tiers to be upgraded or replaced independently.

The user interface is implemented on any platform such as a desktop PC, smartphone or tablet as a native application, web app, mobile app, voice interface, etc. It uses a standard graphical user interface with different modules running on the application server.

The relational database management system on the database server contains the computer data storage logic.

The middle tiers are usually multitiered.

Since the three are not physical but logical in nature, they may run in different servers both in on-premises based solutions, as well as in software-as-a-service (SaaS).

## Benefits of Three-Tier Architecture

* it provides great freedom to development teams who can independently update or replace only specific parts of the application without affecting the product as a whole.

* The application can be scaled up and out rather easily by detaching the front-end application from the databases that are selected according to the individual needs of the customer.

* New hardware, such as new servers, can also be added at a later time to deal with massive amounts of data or particularly demanding services.

* A three-tier-architecture also provides a higher degree of flexibility to enterprises who may want to adopt a new technology as soon as it becomes available.

* Critical components of the application can be encapsulated and retained while the whole system keeps evolving organically.

* Development cycle or upgrade times are significantly improved ensuring minimal disruption in customer’s experience.

Different teams can work on different sections of the application rather than on the full stack according to their areas of expertise, improving their efficiency and speed.

#  The Three Tiers in a Three-Tier Architecture

##  Presentation Tier
Occupies the top level and displays information related to services commonly available on a web browser or web-based application in the form of a graphical user interface (GUI).

It constitutes the front-end layer of the application and the interface with which end-users will interact directly.

This tier is usually built on web development frameworks, such as CSS or JavaScript, and communicates with other tiers by sending results to the browser and other tiers in the network through API calls.

##  Application Tier
This tier is also called the middle tier, logic tier, business logic or logic tier — is pulled from the presentation tier.

It controls the application’s core functionality by performing detailed processing and is usually coded in programming languages, such as Python, Java, C++, .NET, etc.

##  Data Tier
Houses database servers where information is stored and retrieved.

Data in this tier is kept independent of application servers or business logic, and is managed and accessed with programs, such as MongoDB, Oracle, MySQL, and Microsoft SQL Server.

* In this project, We will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively. The following steps will be followed to achieve our aim and objectives of this project.

## 3-Tier Setup For This Project

* A Laptop or PC to serve as a client
* An EC2 Linux Server as a web server (This is where we will install WordPress)
* An EC2 Linux server as a database (DB) server

*For this project, we will use RedHat OS*

# **LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.**

### Step 1 — Prepare the Web Server

* Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

* Create and Attach all three volumes one by one to your Web Server EC2 instance

* Open SSH client of your choice to connect to the EC2 instances using public IP Address and begin configuration

* Use `lsblk` command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

![Screen Shot 2023-02-20 at 2 22 05 PM](https://user-images.githubusercontent.com/117458922/222132847-48f8062a-1d94-4d94-8103-a791cbcb06e9.png)

* Use `df -h` command to see all mounts and free space on your server

* Use `gdisk` utility to create a single partition on each of the 3 disks with the command below

```
sudo gdisk /dev/xvdf
```

![Screen Shot 2023-02-20 at 2 23 36 PM](https://user-images.githubusercontent.com/117458922/222133407-63ad7602-18b0-48a3-86af-8b1098f0137e.png)

![gdsik](https://user-images.githubusercontent.com/117458922/222133495-5a30e9d9-14d4-47cf-9ba2-a2c3841a08e7.png)

* Use `lsblk` utility to view the newly configured partition on each of the 3 disks.

![lsblk](https://user-images.githubusercontent.com/117458922/222134571-726d4c19-5de9-4fa1-b06d-05e83429acc9.png)

* Install `lvm2` package using `sudo yum install lvm2`. Run `sudo lvmdiskscan` command to check for available partitions.

![lvm2](https://user-images.githubusercontent.com/117458922/222135323-f9b00c9b-86d6-415f-b6ba-e46dbccef45b.png)

*Note that previously, in Ubuntu we used `apt` command to install packages, in RedHat/CentOS a different package manager is used, so we shall use `yum` command instead.*

* Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

![pvcreate](https://user-images.githubusercontent.com/117458922/222135963-c336a443-c09d-4c50-b40d-14fbad95ad16.png)

* Verify that your Physical volume has been created successfully by running `sudo pvs`

![pvs](https://user-images.githubusercontent.com/117458922/222136405-dc616e11-0131-4776-8e80-6429dca0dde7.png)

* Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG vg-webdata, then verify that VG has been created successfully by running `sudo vgs`

```
sudo vgcreate vg-webdata /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```

![Volume Group](https://user-images.githubusercontent.com/117458922/222136824-8be1cb18-fb4a-461a-a5cb-ac23aba06bfa.png)

* Use `lvcreate` utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. 
*NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.*

```
sudo lvcreate -n apps-lv -L 14G vg-webdata
sudo lvcreate -n logs-lv -L 14G vg-webdata
```

![logical volumes](https://user-images.githubusercontent.com/117458922/222139364-8da2d649-7a26-4b3b-9a1c-f9e5d9c03497.png)

* Verify that your Logical Volume has been created successfully by running `sudo lvs`

![sudo lvs](https://user-images.githubusercontent.com/117458922/222139409-b324fe86-4cb6-4d3a-a843-943339a7ae54.png)

* Verify the entire setup

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk 
```

* Use `mkfs.ext4` to format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/vg-webdata/apps-lv
sudo mkfs -t ext4 /dev/vg-webdata/logs-lv
```

![Screen Shot 2023-02-20 at 2 33 56 PM](https://user-images.githubusercontent.com/117458922/222140859-f37488e7-07e7-43f9-81a0-f6169d83681a.png)

* Create `/var/www/html` directory to store website files

```
sudo mkdir -p /var/www/html
```

* Create `/home/recovery/logs` to store backup of log data

```
sudo mkdir -p /var/www/html
```

* Mount `/var/www/html` on `apps-lv` logical volume

```
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
```

* Use rsync utility to backup all the files in the log directory `/var/log` into `/home/recovery/logs` *(This is required before mounting the file system)*

```
sudo rsync -av /var/log/. /home/recovery/logs/
```

* Mount `/var/log` on `logs-lv` logical volume. *(Note that all the existing data on /var/log will be deleted. That is why step 15 above is very important)*

```
sudo mount /dev/webdata-vg/logs-lv /var/log
```
* Restore log files back into `/var/log` directory

```
sudo rsync -av /home/recovery/logs/. /var/log
```

* Update `/etc/fstab` file so that the mount configuration will persist after restart of the server. Click on the next Step To update the `/etc/fstab` file

*The UUID of the device will be used to update the /etc/fstab file;*

```
sudo blkid
```

![blkid](https://user-images.githubusercontent.com/117458922/222146582-04357d9e-0179-4168-8b27-db4285c5e789.png)

```
sudo vi /etc/fstab
```

* Update `/etc/fstab` in this format using your own UUID and rememeber to remove the leading and ending quotes.

![fstab](https://user-images.githubusercontent.com/117458922/222147046-10878aa9-80a5-4e58-8451-0f9554889a71.png)

* Test the configuration and reload the daemon with this commands

```
sudo mount -a
sudo systemctl daemon-reload
```

* Verify your setup by running `df -h`, the output should look like this:

![setup](https://user-images.githubusercontent.com/117458922/222147924-6b287e91-ab2d-48e0-9df7-4d38da8f5c02.png)

### Step 2 — Prepare the Database Server Launch a second RedHat EC2 instance that will have a role – ‘DB Server’ Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

* Launch a second RedHat EC2 instance that will have a role – ‘DB Server’

* Create and Attach all three volumes one by one to your DB Server EC2 instance

* Open MobaXterm and connect Ec2 instances using public IP Address and begin configuration

* We need to create a single partition on each of the 3 disks we added

```
sudo gdisk /dev/xvdf
sudo gdisk /dev/xvdg
sudo gdisk /dev/xvdh
```

![Screen Shot 2023-02-20 at 1 16 44 PM](https://user-images.githubusercontent.com/117458922/222150601-cd668275-9e6a-4a8f-a699-b368fa613179.png)

* We will use the `lsblk` command to view the newly configured partition on each of the 3 disks

* We use `sudo lvmdiskscan` command to check for available storage for Logical Volume Manager (LVM). Of course that is after installing `lvm2`

* Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1
```

![pv db](https://user-images.githubusercontent.com/117458922/222152664-93f3ecd4-1625-412b-9fa5-2de24d01c232.png)

* Verify that your Physical volume has been created successfully by running `sudo pvs`

* Use `vgcreate` utility to add all 3 PVs to a volume group (VG). Name the VG *vg-database*

```
sudo vgcreate vg-database /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```

* Verify that your VG has been created successfully by running `sudo vgs`

![Screen Shot 2023-02-20 at 1 20 42 PM](https://user-images.githubusercontent.com/117458922/222173308-c52b398b-2c61-4413-bcb0-5a9f4449ba16.png)

* Use `lvcreate` utility to create a logical volumes `db-lv`and verify the logical volume with `sudo lvs`

![Screen Shot 2023-02-20 at 1 21 44 PM](https://user-images.githubusercontent.com/117458922/222174436-fe5eda56-de50-4055-a403-c7f4d2662094.png)

* Use `mkfs.ext4` to format the logical volumes with ext4 filesystem

```
sudo mkfs -t ext4 /dev/vg-database/db-lv
```

![Screen Shot 2023-02-20 at 1 23 11 PM](https://user-images.githubusercontent.com/117458922/222175308-9f5b8951-708f-40ce-bdf1-406a1682d618.png)

* We need to create */db* directory with `sudo mkdir /db` to store our database files as shown in the image above

* We need to Mount */db* on *db-lv* logical volume

```
sudo mount /dev/vg-database/db-lv /db
```
* We need to update the */etc/fstab* file so that the mount configuration will persist upon restart of the server.

```
sudo blkid
```

![Screen Shot 2023-02-20 at 1 27 10 PM](https://user-images.githubusercontent.com/117458922/222177467-eead7e02-dc28-4d78-a6b5-a1f8aed1a30f.png)

```
sudo vi /etc/fstab
```

![Screen Shot 2023-02-20 at 1 26 42 PM](https://user-images.githubusercontent.com/117458922/222177583-d425b0d0-92c3-4162-b6a7-9caf96c5f511.png)


* Test the configuration and reload the daemon

```
sudo mount -a
sudo systemctl daemon-reload
```

![Screen Shot 2023-02-20 at 1 28 11 PM](https://user-images.githubusercontent.com/117458922/222178127-00b4d2d6-f610-40a4-ba87-51bb8a3324b1.png)


### Step 3 — Install WordPress on your Web Server EC2 Instance

* Update the repository

```
sudo yum -y update
```

* Install wget, Apache and it’s dependencies

```
sudo yum -y install wget httpd
```

* Start Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```

* To confirm that Apache is successfully installed, navigate to the public IP address of the web server using a web browser and you should get this;

![Redhat](https://user-images.githubusercontent.com/117458922/222354236-9abc28e6-5324-42df-9a17-4fb5b67c20d3.png)

* To install PHP and it’s depemdencies that are compatible with Wordpress

```
sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
sudo dnf module list php
sudo dnf module enable php:remi-7.4
sudo dnf install php php-cli php-common
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

![Screen Shot 2023-02-20 at 3 02 02 PM](https://user-images.githubusercontent.com/117458922/222188720-43eda876-ca21-4aab-b7e1-1db098d74199.png)

![Screen Shot 2023-02-20 at 3 02 33 PM](https://user-images.githubusercontent.com/117458922/222188918-571f10b5-e9f8-4536-aa86-f7849908677e.png)

![php status](https://user-images.githubusercontent.com/117458922/222188997-e33c346d-e3c9-46ed-a612-34a1e21f3d4b.png)

* Restart Apache

```
sudo systemctl restart httpd
```

![Screen Shot 2023-02-20 at 3 02 33 PM](https://user-images.githubusercontent.com/117458922/222189248-4d88763a-1da1-4801-ad09-49c6884a6645.png)


* Download wordpress and copy wordpress to *var/www/html*

```
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress /var/www/html/
```

![Screen Shot 2023-02-20 at 3 15 29 PM](https://user-images.githubusercontent.com/117458922/222188240-13b299e4-9242-4704-8331-06c655660c34.png)

![Screen Shot 2023-02-20 at 3 15 29 PM](https://user-images.githubusercontent.com/117458922/222189571-a34b2201-6d4e-4950-a4dc-97b72abc427a.png)

![Screen Shot 2023-02-20 at 4 20 56 PM](https://user-images.githubusercontent.com/117458922/222189775-81515cbf-2ca9-4890-995e-d6f1597ebcb8.png)

![Screen Shot 2023-02-20 at 4 21 29 PM](https://user-images.githubusercontent.com/117458922/222189868-8ce4c123-de2a-4e6d-8363-f4c6c09497ef.png)


* Configure SELinux Policies

```
sudo chown -R apache:apache /var/www/html/wordpress
sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
sudo setsebool -P httpd_can_network_connect=1
```

### Step 4 — Install MySQL on your DB Server EC2

```
sudo yum update
sudo yum install mysql-server
```
* Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:

```
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```

![Screen Shot 2023-02-20 at 4 24 20 PM](https://user-images.githubusercontent.com/117458922/222185369-65eeaa4b-7b0b-449c-ba33-accb5bfd5ef5.png)


### Step 5 — Configure DB to work with WordPress

```
sudo mysql -u root -p
CREATE DATABASE wordpress;
SHOW DATABASES;
CREATE USER 'wordpress'@'%' IDENTIFIED mysql-native_password BY 'wordpress';
GRANT ALL PRIVILEGES ON *.* TO 'wordpress'@'%' WITH GRANT OPTION';
FLUSH PRIVILEGES;
Sselect user, host from mysql.user;
exit
```

![Screen Shot 2023-02-20 at 4 35 23 PM](https://user-images.githubusercontent.com/117458922/222185591-cd6597ee-a24d-49ec-bf8b-0b06424da090.png)

![database](https://user-images.githubusercontent.com/117458922/222185220-8f516926-559d-4d53-adb6-f5b5ae14efe8.png)


### Step 6 — Configure WordPress to connect to remote database. 

*Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32*

* Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

```
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
```

*We will Get the private IP address of the Database Server Instance and use it to connect to our database from the Web Server*

* Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

![Screen Shot 2023-02-20 at 4 54 10 PM](https://user-images.githubusercontent.com/117458922/222187343-c79a27dd-7839-4038-a244-a98f5c73d177.png)

* Change permissions and configuration so Apache could use WordPress:

* Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)

* Try to access WordPress from your browser through the Web Server's public IP address with extension http://<ip adrress>/wordpress/
  
<img width="885" alt="wordpress" src="https://user-images.githubusercontent.com/117458922/222352936-2446ecc1-6810-4a7d-ab9c-dfe652e2de37.png">

![Screen Shot 2023-02-20 at 5 00 16 PM](https://user-images.githubusercontent.com/117458922/222353083-fe0caaf0-7283-434c-a556-012701b05a18.png)
