# (STEP 14) PROJECT 6: Web Solution With WordPress.


*In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).*


Project 6 consists of two parts:

* Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.*

* Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.


### **Three-tier Architecture**

*Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.*

*Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.*

1. **Presentation Layer (PL)**: This is the user interface such as the client server or browser on your laptop.

2. **Business Layer (BL)**: This is the backend program that implements business logic. Application or Webserver

3. **Data Access or Management Layer (DAL)**: This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server


   ![Three Tier Architecture](./Images/Three%20Tier%20Architecture.PNG)


*In this project, we will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.*

*We will be working working with several storage and disk management concepts.*


Your 3-Tier Setup;
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server
Use RedHat OS for this project

**Note**: for Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used ubuntu user, but for RedHat you will need to use ec2-user user. Connection string will look like ec2-user@<Public-IP>

Let us get started!


## LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.


Create an EC2 Linux Server as a web server (This is where we will install Wordpress). We will use Red Hat for the project.

Go to AWS account and click on Launch Instance.


![Launch Instances](./Images/Launch%20Instances.PNG)



Name the Instance – PBL Project_6.


![Name Instance](./Images/Name%20Instance.PNG)




Under Application and OS Images select Red Hat.


![Red Hat](./Images/Red%20Hat.PNG)



You can use an existing Key Pair. In our case we will use PBL Project 5.



![Key Pair](./Images/Key%20Pair.PNG)


You can create a new security group or use an existing security group. In our case we will use an existing security group that has All traffic under type, All under Protocol and Port Range. One of Source should be 0.0.0.0/0 and the other should be ::/0



![Existing Security Group](./Images/Existing%20Security%20Group.PNG)




Change Number of Instances to 2 as we need 2 instances.


![Number of Instances](./Images/Number%20of%20Instances.PNG)



Click Launch Instance.


![Launch Instance](./Images/Launch%20Instance.PNG)



Click on View all Instances.


![View Instances](./Images/View%20Instances.PNG)




The 2 Instances are running.


![Instances Running](./Images/Instances%20Running.PNG)



You can rename them to *PBL project 6 Web Server* and *PBL Project 6 Database Server.*


![Rename Instances](./Images/Rename%20Instances.PNG)




Availability Zone (AZ) of the 2 servers should be the same . It shows us-east-2b for both.


![Availability Zones](./Images/Availability%20Zone.PNG)


### Step 1
After launching an EC2 instance that will serve as "Web Server", Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.



* Go to EC2 and click on Volumes.

  ![Volumes](./Images/Volumes.PNG)


* The screen below will be displayed.


  ![Volumes Display Screen](./Images/Volumes%20Display%20Screen.PNG)


* Click on Create Volume.



  ![Create Volume](./Images/Create%20Volume.PNG)

* The screen below will be displayed.


  ![Create Volume Display Screen](./Images/Create%20Volume%20Display%20Screen.PNG)


* Change Size to 10 GiB and Availability Zone to us-east-2b 


  ![Size and AZ Changes](./Images/Size%20and%20AZ%20Changes.PNG)


* Click on Create Volume.


  ![Create Volume_2](./Images/Create%20Volume_2.PNG)


* Refresh the browser. The New Volume created will show available. It will also show the date and the exact time you created it.


  ![Volume 1 Created](./Images/Volume%201%20Created.PNG)


 * Rename it to PBL Project 6 Web 1.


   ![PBL Project 6 Web 1](./Images/PBL%20project%206%20Web%201.PNG)


* Follow the same steps and create 2 additinal Volumes and rename them PBL Project 6 Web 2 and PBL Project 6b Web 3 respectively. Remember to refresh the browser once you have created the volumes.


  ![Additional Volumes Created](./Images/Additional%20Volumes%20Created.PNG)

* Go back to the instance and connect to PBL Project 6 Web Server.


  ![PBL Project 6 Web Server_Connect](./Images/PBL%20project%206%20Web%20Server_Connect.PNG)


### Step 2

Attach all three volumes one by one to your Web Server EC2 instance.

* Go to Instances
* Select Volumes
* Select PBL Project 6 Web 1 volume.
* Click on Actions
* Attach volume

  ![Attach Volume 1_1](./Images/Attach%20Volume%201_1.PNG)

* Remember to select the instance for PBL Project 6 Web Server.

  ![Attach Volume 1_2](./Images/Attach%20Volume%201_2.PNG)


* Repeat the same process and attach PBL Project 6 Web 2 and PBL Project 6 Web 3. First refresh the browser.

* Go back to the instance and connect through the terminal for the web server (PBL Project 6 Web Server) and run the command `lsblk`. It will show attached volumes.


  ![Attached Volumes_Terminal](./Images/Attached%20Volumes_Terminal.PNG)


### Step 3
Open up the Linux terminal to begin configuration.

Use `lsblk` command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with `ls /dev/` and make sure you see all 3 newly created block devices there – their names will likely be *xvdf, xvdh, xvdg*.


![lsblk](./Images/lsblk.PNG)


Use `df -h` command to see all mounts and free space on your server.


![Mount and Free Space](./Images/Mount%20and%20Free%20Space.PNG)


Use gdisk utility to create a single partition on each of the 3 disks. Do this one disc at a time.

`sudo gdisk /dev/xvdf`

Once you run the command you will be prompted for command (? for help): put n and then enter.

Then under partition number put 1 and enter.

Click enter because we want to use the entire disc. Click enter again to use the entire disc.

We want to change the current type from Linux filesystem to logical Volume Management. Therefore enter 8e00.

Enter command p to check what we have done.

Enter w to write and then Yes.

It will show that that the operation has completed successfully.


![Create Single Partition xvdf_1](./Images/Create%20Single%20partition%20xvdf_1.PNG)


![Create Single Partition xvdf_2](./Images/Create%20Single%20partition%20xvdf_2.PNG)

Repeat the process by running the command below.

`sudo gdisk /dev/xvdg`

Once you run the command you will be prompted for command (? for help): put n and then enter.

Then under partition number put 1 and enter.

Click enter because we want to use the entire disc. Click enter again to use the entire disc.

We want to change the current type from Linux filesystem to logical Volume Management. Therefore enter 8e00.

Enter command p to check what we have done.

Enter w to write and then Yes.

It will show that that the operation has completed successfully.


![Create Single Partition xdvg_1](./Images/Create%20Single%20partition%20xvdg_1.PNG)

![Create Single Partition xdvg_2](./Images/Create%20Single%20partition%20xvdg_2.PNG)


Repeat the process again by running the command below.

`sudo gdisk /dev/xvdh`

Once you run the command you will be prompted for command (? for help): put n and then enter.

Then under partition number put 1 and enter.

Click enter because we want to use the entire disc. Click enter again to use the entire disc.

We want to change the current type from Linux filesystem to logical Volume Management. Therefore enter 8e00.

Enter command p to check what we have done.

Enter w to write and then Yes.

It will show that that the operation has completed successfully.


![Create Single Partition xdvh_1](./Images/Create%20Single%20partition%20xvdh_1.PNG)


![Create Single Partition xdvh_2](./Images/Create%20Single%20partition%20xvdh_2.PNG)


### Step 4

Use `lsblk` utility to view the newly configured partition on each of the 3 disks.


![lsblk_2](./Images/lsblk_2.PNG)


### Step 5

Install lvm2 package using `sudo yum install lvm2`. 

Click Y when prompted.

![Install lvm2_1](./Images/Install%20lvm2_1.PNG)

![Install lvm2_2](./Images/Install%20lvm2_2.PNG)


If you want to confirm that it is installed then run the command `which lvm`.


![Confirm lvm Installation](./Images/Confirm%20lvm%20Installation.PNG)


Run `sudo lvmdiskscan` command to check for available partitions. It will show 4 available partitions.


![Available Partitions](./Images/Available%20Partitions.PNG)


**Note:** Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.


### Step 6

Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.

There are 2 ways of doing this. You can run the commands one at time or run one command for all. 

First run;

`sudo pvcreate /dev/xvdf1`

`sudo pvcreate /dev/xvdg1`

`sudo pvcreate /dev/xvdh1`

or 

`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

We will use the last command that will run the 3 at once.


![Mark as Physical volumes](./Images/Mark%20as%20Physical%20Volumes.PNG)


### Step 7

Verify that your Physical volume has been created successfully by running `sudo pvs`.


![Confirm Physical Volumes](./Images/Confirm%20Physical%20Volumes.PNG)


### Step 8

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

`sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`


![Create volume group webdata](./Images/Create%20volume%20group%20webdata.PNG)



Volume groups adds the logical volumes into one group.


### Step 9


Verify that your VG has been created successfully by running sudo vgs

![Confirm volume group created successfully](./Images/Confirm%20volume%20group%20created%20successfully.PNG)


It will only show one volume instead of 3 .



### Step 10

Use `lvcreate` utility to create 2 logical volumes. **apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE:** apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.


`sudo lvcreate -n apps-lv -L 14G webdata-vg`

![Create logical volume apps-lv](./Images/Create%20logical%20volume%20apps-lv.PNG)


`sudo lvcreate -n logs-lv -L 14G webdata-vg`

![Create logical volume logs-lv](./Images/Create%20logical%20volume%20logs-lv.PNG)


### Step 11


Verify that your Logical Volume has been created successfully by running `sudo lvs`


![Verify logical volumes creation](./Images/Verify%20logical%20volumes%20creation.PNG)


### Step 12

Verify the entire setup

`sudo vgdisplay -v #view complete setup - VG, PV, and LV`


![Display Entire Setup_1](./Images/Display%20Entire%20Setup_1.PNG)

![Display Entire Setup_2](./Images/Display%20Entire%20Setup_2.PNG)


![Display Entire Setup_3](./Images/Display%20Entire%20Setup_3.PNG)


`sudo lsblk`

![lsblk_3](./Images/lsblk_3.PNG)


### STep 13

Use mkfs.ext4 to format the logical volumes with ext4 filesystem

`sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`

![Format logical volumes_apps](./Images/Format%20logical%20volumes_apps.PNG)


`sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`

![Format logical volumes_logs](./Images/Format%20logical%20volumes_logs.PNG)


### Step 14

Create /var/www/html directory to store website files. P means create the current directory if it does not exist.

`sudo mkdir -p /var/www/html`


![Create Directory](./Images/Create%20Directory.PNG)


To confirm, run `sudo ls -l /var`


![Confirm Directory](./Images/Confirm%20Directory.PNG)


### Step 15

Create */home/recovery/logs* to store backup of log data

`sudo mkdir -p /home/recovery/logs`


![Create Home_Recovery_Logs](./Images/Create%20Home_recovery_Logs.PNG)


### Step 16

Mount */var/www/html* on apps-lv logical volume

`sudo mount /dev/webdata-vg/apps-lv /var/www/html/`

![Mount logical volume apps](./Images/Mount%20logical%20volume%20apps.PNG)


*It is a good practice to first check what is in the directory before you mount as it will replace whatever was in the directory after you mount. You can do this by running the command `ls -l /var/www/html`. It should have nothing , but in our case it will have something because I have just mounted the logical volume.*


If you run `df -h` , it will show that it is mounted.


![Confirm Mounted lv](./Images/Confirm%20Mounted%20lv.PNG)



### Step 17

Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs 

(This is required before mounting the file system)

`sudo rsync -av /var/log/. /home/recovery/logs/`


![Backup log directory files](./Images/Backup%20log%20directory%20files.PNG)


![Backup log directory files_2](./Images/Backup%20log%20directory%20files_2.PNG)


To confirm this, run the command below.

`sudo ls -l /home/recovery/logs`

![Confirm backup logs](./Images/Confirm%20backup_logs.PNG)



### Step 18

Mount */var/log* on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)

`sudo mount /dev/webdata-vg/logs-lv /var/log`


![Mount var log on logs -lv](./Images/Mount%20var%20log%20on%20logs-lv.PNG)


If you run `ls -l /var/log`, it will show that it is empty.


![Lost + Found](./Images/Lost%20%2B%20Found.PNG)

### Step 19


You now need to copy back the files by restoring lg files back to /var/log directory.

`sudo rsync -av /home/recovery/logs/. /var/log`

![Restore log files](./Images/Restore%20log%20files.PNG)


To check, run the command below.

`sudo ls -l /var/log`

![Confirm log file restore](./Images/Confirm%20log%20file%20restore.PNG)

### Step 20

Update */etc/fstab* file so that the mount configuration will persist after restart of the server.


To check the block id, run `sudo blkid`


![Block ID](./Images/Block%20ID.PNG)

The two UUID are diaplayed at the bottom of the output.

Copy the 2 types with the UUID in notepad.


![UUID Copy](./Images/UUID%20Copy.PNG)


We know that the first one is for apps. Remove the quotes after the = and clear from the end to where it says BLOCk on both as shown below.


![UUID Copy_2](./Images/UUID%20Copy_2.PNG)


Go back to the terminal and run the command below.

`sudo vi /etc/fstab`


![etc_fstab](./Images/etc_fstab.PNG)


Press i for Insert then comment in the file: # Mount for wordpress webserver.

Go to the notepad and add */var/www/html ext4 defaults 0 0* to the apps ID as shown below.

/dev/mapper/webdata--vg-apps--lv: UUID=ccf3ed93-b726-4d72-9326-e2901a02fb9d /var/www/html ext4 defaults 0 0

 Copy the section below and paste it in the terminal as shown below.

 UUID=ccf3ed93-b726-4d72-9326-e2901a02fb9d /var/www/html ext4 defaults 0 0


 ![etc_fstab_2](./Images/etc_fstab_2.PNG)


 Press Enter.


 Go back to the notepad and copy the UUID for the logs and paste it in the terminal.

 UUID=5343e18c-1fcf-433e-bce7-a4e863a7cfc7 /var/log      ext4 defaults 0 0


 ![etc_fstab_3](./Images/etc_fstab_3.PNG)


 After editing this file, run `systemctl daemon-reload` to update systemd units generated from this file. This can be commented.

 To save and quit run the command below.

 Esc
 :wqa
 Enter


 ![etc_fsatab_4](./Images/etc_fstab_4.PNG)

 The screen below will be displayed.


 ![etc_fstab_5](./Images/etc_fstab_5.PNG)


 Run the command `sudo mount -a` to ensure that what we performed was correct.


 ![Confirm Configuration](./Images/Confirm%20Configuration.PNG)

 No error is returned which means that the configuration is successful.

 Reload the daemon.

 `sudo systemctl daemon-reload`


![Reload Daemon](./Images/Reload%20Daemon.PNG)


Verify your setup by running `df -h`, output must look like this:


![df -h](./Images/df%20-h.PNG)



## Phase 2 — Prepare the Database Server

Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of *apps-lv* create *db-lv* and mount it to */db* directory instead of */var/www/html/.*


### Step 1 - Create Volumes

Once logged in go to and select PBL Project 6 Database Server and go to volumes.

![PBL Project 6 Database Server_Volumes](./Images/PBL%20Project%206%20Database%20Server_Volumes.PNG)


 Click on Create Volume.

 ![PBL Project 6 Create Volumes](./Images/PBL%20Project%206_Create%20Volumes.PNG)



Change Size to 10 GiB and Availability Zone to us-east-2b 


![Size and AZ Changes_DB](./Images/Size%20and%20AZ%20Changes_DB.PNG)


Click on Create Volume.


![Create Volume_DB](./Images/Create%20Volume_DB.PNG)



The screen below will be displayed.


![Create Volume DB Display Screen](./Images/Create%20Volume%20DB%20Display%20Screen.PNG)


Repeat the same process to create the same Volume.

Change Size to 10 GiB and Availability Zone to us-east-2b.


![Size and AZ Changes DB2](./Images/Size%20and%20AZ%20Changes_DB2.PNG)


Click Create Volume.

The screen below will be displayed.

![Create Volume DB Display Screen2](./Images/Create%20Volume%20DB%20Display%20Screen2.PNG)



Repeat the same process to create the third volume.

Click Create Volume.


![Create Volume DB3](./Images/Create%20Volume%20DB%20Display%20Screen2.PNG)


Change Size to 10 GiB and Availability Zone to us-east-2b.


![Size and AZ Changes DB3](./Images/Size%20and%20AZ%20Changes_DB3.PNG)



Click Create Volume.


![Create Volume_3](./Images/Create%20Volume_3.PNG)


The screen below will be displayed.


![Create Volume DB Display Screen3](./Images/Create%20Volume%20DB%20Display%20Screen3.PNG)



Click Refresh. Scroll down and you will see the three volumes created. They will be displayed as available.

![Three DB Volumes Created](./Images/Three%20DB%20Volumes%20Created.PNG)


### Step 2 - Rename Volumes

Select the first Volume and Rename it to PBL Project 6 DB1 as shown below. Save it at the end.


![Rename DB1 Volume](./Images/Rename%20DB1%20Volume.PNG)


Repeat the same for 2 and 3. It should look like the screen below.


![Renamed DB Volumes](./Images/Renamed%20DB%20Volumes.PNG)


### Step 3 - Attach Volumes

Select on PBL Project 6 DB1. Click on Actions and select attach volumes. 


![Attach DB 1 Volume](./Images/Attach%20DB1%20Volume.PNG)


Under Instance , select PBL Project 6 Database Server. Click Attach.


![Attach DB Volume 1_2](./Images/Attach%20DB%20Volume%201_2.PNG)


Click Refresh.


Repeat the same process for PBL Project 6 DB2 and PBL Project 6 DB 3.

PBL Project 6 DB 2


![Attach DB 2 Volume](./Images/Attach%20DB2%20Volume.PNG)


![Attach DB Volume 2_2](./Images/Attach%20DB%20Volume%202_2.PNG)


PBL Project 6 DB 3

![Attach DB 3 Volume](./Images/Attach%20DB%203%20Volume.PNG)


![Attach DB Volume 3_2](./Images/Attach%20DB%20Volume%203_2.PNG)


Click on Refresh.


Click on Instances.

Select PBL Project 6 Databse Server and connect.

![Connect on DB](./Images/Connect%20on%20DB.PNG)


![PBL Project 6 Database Server Connected](./Images/PBL%20Project%206%20Database%20Server%20Connected.PNG)



### Step 4 - Run lsblk

Run `lsblk`


![lsblk 4](./Images/lsblk%204.PNG)


### Step 5 - Create Single  Partition

Use gdisk utility to create a single partition on each of the 3 disks. Do this one disc at a time.

Run `sudo gdisk /dev/xvdf`


Once you run the command you will be prompted for command (? for help): put n and then enter.

Then under partition number put 1 and enter.

Click enter because we want to use the entire disc. Click enter again to use the entire disc.

We want to change the current type from Linux filesystem to logical Volume Management. Therefore enter 8e00.

Enter command p to check what we have done.

Enter w to write and then Yes.

It will show that that the operation has completed successfully.


![Create Single Partition DB xvdf](./Images/Create%20Single%20partition%20DB%20xvdf.PNG)


![Create Single Partition DB xvdf_2](./Images/Create%20Single%20partition%20DB%20xvdf_2.PNG)


Repeat the same process for xvdg.


Run `sudo gdisk /dev/xvdg`


Once you run the command you will be prompted for command (? for help): put n and then enter.

Then under partition number put 1 and enter.

Click enter because we want to use the entire disc. Click enter again to use the entire disc.

We want to change the current type from Linux filesystem to logical Volume Management. Therefore enter 8e00.

Enter command p to check what we have done.

Enter w to write and then Yes.

It will show that that the operation has completed successfully.


![Create Single Partition DB xvdg](./Images/Create%20Single%20partition%20DB%20xvdg.PNG)


![Create Single Partition DB xvdg_2](./Images/Create%20Single%20partition%20DB%20xvdf_2.PNG)


Repeat the same process for xvdh.


Run `sudo gdisk /dev/xvdh`


Once you run the command you will be prompted for command (? for help): put n and then enter.

Then under partition number put 1 and enter.

Click enter because we want to use the entire disc. Click enter again to use the entire disc.

We want to change the current type from Linux filesystem to logical Volume Management. Therefore enter 8e00.

Enter command p to check what we have done.

Enter w to write and then Yes.

It will show that that the operation has completed successfully.


![Create Single Partition DB xvdh](./Images/Create%20Single%20partition%20DB%20xvdh.PNG)


![Create Single Partition DB xvdh_2](./Images/Create%20Single%20partition%20xvdh_2.PNG)



Run `lsblk` again to check and make sure that the partitions were created.


![lsblk_db_1](./Images/lsblk_db_1.PNG)


It will show that it was created successfully per the diagram.


### Step 6 - Intall lvm2

Run `sudo yum install lvm2 -y`


![Install lvm2_db_1](./Images/Install%20lvm2_db_1.PNG)


![Install lvm2_db_2](./Images/Install%20lvm2_db_2.PNG)


### Step 7

Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.

Run;

`sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`


![Created Physical volumes](./Images/Created%20DB%20Physical%20Volumes.PNG)


### Step 8

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the Volume group vg-database.

`sudo vgcreate vg-database /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`


![Created volume group vg-database](./Images/Created%20volume%20group%20vg-database.PNG)



Verify that your VG has been created successfully by running `sudo vgs`.


![Confirm db volume group created successfully](./Images/Confirm%20db%20volume%20group%20created%20successfully.PNG)



### Step 9 - Create logical volume (db-lv)

Use `lvcreate` utility to create db-lv logical volume. Give it 20G.


`sudo lvcreate -n db-lv -L 20G vg-database`

![Create logical volume db-lv](./Images/Create%20logical%20volume%20db-lv.PNG)


To confirm, run `sudo lvs`.


![Confirm db logical volume](./Images/Confirm%20db%20logical%20volume.PNG)





### Step 10 - Create the Mount Point

Create a directory called db.

`sudo mkdir /db`


![Create db directory](./Images/Create%20db%20directory.PNG)


Before you mount it, you need to create a file system first.Use mkfs.ext4 to format the logical volumes with ext4 filesystem.

Run the command below.

`sudo mkfs.ext4 /dev/vg-database/db-lv`


![Format logical volume db-lv](./Images/Format%20logical%20volumes_db-lv.PNG)


The next step is to mount. Before we do that, you can go ahead and confirm that nothing exists in the directory we just created by running the command below.


`ls -l /db`

![db directory check](./Images/DB%20directory%20check.PNG)



Go ahead and run the command below to mount.

`sudo mount /dev/vg-database/db-lv /db`


![Mount db-lv](./Images/Mount%20db-lv.PNG)


Run `df -h` to show that it has been successfully mounted.


![df -h_1](./Images/df%20-h_1.PNG)


### Step 11

Update */etc/fstab* file so that the mount configuration will persist after restart of the server.


To check the block id, run `sudo blkid`


![DB block id](./Images/DB%20block%20id.PNG)

The UUID is diaplayed at the bottom of the output.

Copy the UUID in notepad.


![Copy UUID DB](./Images/Copy%20UUID%20DB.PNG)


Run the command below;

`sudo vi /etc/fstab`


![etc_fstab_db](./Images/etc_fstab_db.PNG)



Press i for Insert then comment in the file: # Mount for database.



Go to the notepad and add */db ext4 defaults 0 0* to the apps ID as shown below.

![etc_fstab_db_1](./Images/etc_fstab_db.PNG)


Copy what you have in the notepad and paste it to the terminal.


![etc_fstab_db_2](./Images/etc_fstab_db_2.PNG)




To save and quit run the command below.

Esc :wqa Enter





![Save and quit_2](./Images/Save%20and%20quit_2.PNG)



Run the command `sudo mount -a` to ensure that what we performed was correct.


 ![Confirm Configuration_2](./Images/Confirm%20Configuration_2.PNG)

 No error is returned which means that the configuration is successful.

 Reload the daemon.

 `sudo systemctl daemon-reload`


![Reload Daemon_2](./Images/Reload%20Daemon_2.PNG)


Verify your setup by running `df -h`, output must look like this:


![df -h_2](./Images/df%20-h_2.PNG)



## Phase 3 - Install Wordpress on your Web Server EC2


1. Update the repository.
   Login to the database server and run the below command. 

   `sudo yum -y update`


   ![Database Server Repository Update](./Images/Database%20Server%20Repository%20Update.PNG)


   Login to the web server and run the same command. The installation might take a while.

   `sudo yum -y update`


   ![Web Server Repository Update](./Images/Web%20Server%20Repository%20Update.PNG)

  2. Check Security group.
     
     Go to EC2 and select database server (PBL Project 6 Database Server).

     Select Security > Security Details > Inbound Rules.

     Click Edit Inbound Rules and ensure that all trafic is allowed.

     ![Edit Inbound Rules Database](./Images/Edit%20Inbound%20Rules%20Database.PNG)

     Click Save

     Repeat the same process for the Web Server.

     ![Edit Inbound Rules Web](./Images/Edit%20Inbound%20Rules%20Web.PNG)

     Note that it is the same security group so no changes are needed.



  3. Install wget, Apache and it’s dependencies

     `sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`

     ![Apache Installation Web Server](./Images/Apache%20Installation%20Web%20Server.PNG)

  4. To install the latest version of PHP.  First install EPEL repository by running the command below.


     `sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

     Click Yes when prompted.


     ![EPEL Repository](./Images/EPEL%20Repository.PNG)

     ![EPEL Repository2](./Images/EPEL%20Repository2.PNG)


     Next, install yum utils and enable remi-repository using the command below.

     `sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`


     Click Y when prompted.

     ![yum utils](./Images/yum%20utils.PNG)

     ![yum utils2](./Images/yum%20utils2.PNG)


     After the successful installation of yum-utils and Remi-packages, search for the PHP modules which are available for download by running the command.

     `sudo dnf module list php`


     ![Latest PHP Install](./Images/Latest%20PHP%20Install.PNG)

     Click Y when prompted.

     ![Latest PHP Install2](./Images/Latest%20PHP%20Install2.PNG)


     Click Y when prompted.

     ![Latest PHP Install3](./Images/Latest%20PHP%20Install3.PNG)


     The output indicates that the currently installed version of PHP is PHP 7.2. To install the newer release, PHP 8.0, reset the PHP modules.

     `sudo dnf module reset php`

     ![Reset PHP Modules](./Images/Reset%20PHP%20Modules.PNG)


     Click Y when prompted.


     Having reset the PHP modules, enable the PHP 8.0 module by running.


     `sudo dnf module enable php:remi-8.0`


     ![Enable PHP 8.0](./Images/Enable%20PHP%208.0.PNG)


     Click Y when prompted.

     Finally, install PHP, PHP-FPM (FastCGI Process Manager) and associated PHP modules using the command.


     `sudo dnf install php php-opcache php-gd php-curl php-mysqlnd`


     You will be prompted 3 times to select Y or . Select Y.


     ![Install PHP PHP-FPM](./Images/Install%20PHP%20PHP-FPM.PNG)


     verify the version installed,run.

      `php -v `

     ![Confirm PHP Version](./Images/Confirm%20PHP%20Version.PNG)


     We now have PHP 8.0 installed. Equally important, we need to start and enable PHP-FPM on boot-up.


     `sudo systemctl start php-fpm`

     ![Start PHP](./Images/Start%20PHP.PNG)


     `sudo systemctl enable php-fpm`


     ![Enable PHP2](./Images/Enable%20PHP2.PNG)


     To check its status execute the command;


     `sudo systemctl status php-fpm`


     ![PHP Status](./Images/PHP%20Status.PNG)


     To instruct SELinux to allow Apache to execute the PHP code via PHP-FPM run;


     `sudo setsebool -P httpd_execmem 1`

     ![Allow Apache to execute PHP Code](./Images/Allow%20Apache%20to%20execute%20PHP%20Code.PNG)


  5. Finally, restart Apache web server for PHP to work with Apache web server.


     `sudo systemctl restart httpd`


     ![Restart Apache Web Server](./Images/Restart%20Apache%20Web%20Server.PNG)


     You can check the status by running the command below.

     `sudo systemctl status httpd`

     ![Status httpd](./Images/Status%20httpd.PNG)

     It shows that it is active and running.


     Get the public IP from the web server.

     18.216.169.86

     Paste the IP on the web and the screen belpow will be displayed showing that the apache is working fine.


     ![Red Hat Enterprise Linux Test Page](./Images/Red%20Hat%20Enterprise%20linux%20Test%20Page.PNG)

  6. Download wordpress and copy wordpress to var/www/html


     Run `mkdir wordpress`

     ![Create wordpress directory](./Images/Create%20wordpress%20directory.PNG)


     Change directory to wordpress.

     `cd wordpress`

     ![Directory Change wordpress](./Images/Directory%20Change%20wordpress.PNG)

     Download wordpress.


     `sudo wget http://wordpress.org/latest.tar.gz`


     ![Download wordpress](./Images/Download%20wordpress.PNG)

     Run `ls -l`. It will show that we have the latest star.

     ![ls -l](./Images/ls%20-l.PNG)

     Extract wordpress by running the command below.

     `sudo tar xzvf latest.tar.gz`

     ![Extract wordpress](./Images/Extract%20wordpress.PNG)

     Run `ls -l`. It will show that an extract called wordpress has been created.


     ![ls -l wordpress](./Images/ls%20-l%20wordpress.PNG)



     Change directory into wordpress.

     `cd wordpress`

     ![Directory Change wordpress2](./Images/Directory%20Change%20wordpress2.PNG)


     Run `ls -l`. It will display everything in the wordpress directory.


     ![ls -l wordpress2](./Images/ls%20-l%20wordpress.PNG)


     Run the command below to create wp-config.php and then copy the content of wp-config-sample.php to wp-config.php.

     `sudo cp -R wp-config-sample.php wp-config.php`


     ![Copy wp-config.php](./Images/Copy%20wp-config.php)


     Run `cat wp-config.php`

     ![cat wp-config.php](./Images/cat%20wp-config.php)


     Run the command below to copy wordpress to var/www/html.

     `sudo cp -R wordpress /var/www/html/`


     ![Copy wordpress](./Images/Copy%20%20wordpress.PNG)


     Change directory to /var/www/html.

     `cd /var/www/html`

     ![Directory Change html](./Images/Directory%20Change%20html.PNG)


     Run `ls -l`

     ![ls -l_2](./Images/ls%20-l_2.PNG)


     This is not supposed to be the case so we will remove the copy we just did by running the command below.


     `sudo rm -rf wordpress/`

     ![Remove wordpress](./Images/Remove%20wordpress.PNG)

      Also remove lost + found

      `sudo rm -rf lost+found/`

      ![Remove lost+found](./Images/Remove%20lost%2Bfound.PNG)

      Run `ls -l`

      ![ls -l_3](./Images/ls%20-l_3.PNG)

      It will show that there is nothing now.

      Change directory by running `cd ../..`

       ![Directory Change var](./Images/Directory%20Change%20var.PNG)

       Run `cd`

       ![cd](./Images/cd.PNG)


       Run `ls`

       ![ls](./Images/ls.PNG)


       Change directory to wordpress.

       `cd wordpress`


       ![Directory Change wordpress](./Images/Directory%20Change%20wordpress3.PNG)


       Run `ls`

       ![ls 4](./Images/ls%204.PNG)


      To see the contents of wordpress, run the command below.

      `ls -l wordpress`

       ![Wordpress contents](./Images/Wordpress%20contents.PNG)


       We do not want to copy the wordpress folder. We would like to open the folder and copy the contents to /var/www/html/.

       `sudo cp -R wordpress/. /var/www/html/`

       ![Copy wordpress contents](./Images/Copy%20wordpress%20contents.PNG)


       To confirm that the contents were copied to /var/www/html/, run the command below.

       `sudo ls -l /var/www/html/`

       ![Confirm Copy](./Images/Confirm%20Copy.PNG)


       Change directory to /var/www/html


       `cd /var/www/html`

       ![Directory Change html_2](./Images/Directory%20Change%20html_1.PNG)


       Run `ls` 

       ![ls_5](./Images/ls_5.PNG)

  6. Install mysql on the web server.
      
      The webserver will be acting as a client , therefore we will need to install my sql.

     Run the command below.

     `sudo yum install mysql-server`

     ![Install mysql server in web server](./Images/Install%20mysql%20server%20in%20web%20server.PNG)

     Click yes when prompted.

     ![Install mysql server to web server2](./Images/Install%20mysql%20server%20in%20web%20server2.PNG)


     We will do the same installation to the database server also to ensure that the web server can talk to the database server.


    
    
7. Login to the database server through the terminal.


   ![Login to Database](./Images/Login%20to%20Database.PNG)


   Run the command below.

     `sudo yum install mysql-server`

     ![Install mysql server in database](./Images/Install%20mysql%20server%20in%20database.PNG)

     Click yes when prompted.

     ![Install mysql server in database_2](./Images/Install%20mysql%20server%20in%20database_2.PNG)


  8. Switch back to the web server and run the command below to start the service for mysql. Make sure you are in the hmtl directory as shown.


     `sudo systemctl start mysqld`


     ![Start service mysql web server](./Images/Start%20service%20mysql%20web%20server.PNG)


     Enable the service by the command below.

     `sudo systemctl enable mysqld`

     ![Enable mysql web server](./Images/Enable%20mysql%20web%20server.PNG)


     Now check the status.

     `sudo systemctl status mysqld`


     ![Check mysql web service status](./Images/cd.PNG)

     It shows that the service is running.

 9. Go back to the database server or log back in if you were logged out.

    ![Login to database server_2](./Images/Login%20to%20Database_2.PNG)


    Start mysql server in  database server.

    `sudo systemctl start mysqld`


    ![Start service mysql database server](./Images/Start%20service%20mysql%20database%20server.PNG)


    Enable mysql server in database server.

    `sudo systemctl enable mysqld`

    ![Enable mysql database server](./Images/Enable%20mysql%20database%20server.PNG)


    Check the status of mysql server.

    `sudo systemctl status mysqld`

    ![Check mysql database service status](./Images/Check%20mysql%20database%20service%20status.PNG)


10. To set the password, run the command below.

     `sudo mysql_secure_installation`

     ![Secure Installation](./Images/Secure%20Installation.PNG)

     Press n when prompted for either y/n

     When prompted  give it a simple password like `password` and enter. You will need to re-enter the password.

     ![Create Password](./Images/Create%20Password.PNG)

     Press y for all the promptings

     ![Secure Installation](./Images/Secure%20Installation_2.PNG)


     ![Secure Installation_3](./Images/Secure%20Installation_3.PNG)


  12. Login to mysql using the password (password) through the command below.


      `sudo mysql -u root -p`

        ![Mysql login](./Images/Mysql%20login.PNG)

      ![Mysql login_2](./Images/Mysql%20login_2.PNG) 


  13. Create database named wordpress.

      `create database wordpress;`

      ![Create database wordpress](./Images/Create%20database%20wordpress.PNG)


      To show the database, run the command below.

      `show databases;`


      ![Show databases](./Images/Show%20databases.PNG)



  13. Create a database user.

       `CREATE USER 'wordpress'@'%' IDENTIFIED WITH mysql_native_password BY 'wordpress';`

      *% means it can connect from any where. Normally you could just put the IP address of the web server only for security , but in our case we want it to connect from anywhere.*

      We have selected the password to be *wordpress*

      ![Created database user](./Images/Created%20database%20user.PNG)

  14. Grant Priviledges to the database user.

       `GRANT ALL PRIVILEGES ON *.* TO 'wordpress'@'%' WITH GRANT OPTION;`


       The first * means database and the second * means tables.

       ![Grant privileges](./Images/Grant%20Privileges.PNG)


       Run `flush privileges`

       ![Flush privileges](./Images/Extract%20wordpress.PNG)


       Confirm that the database user has been created.

       `select user, host from mysql.user;`

       ![Confirm database user created](./Images/Confirm%20database%20user%20created.PNG)


      Run `exit;`

       ![Exit mysql](./Images/Exit%20mysql.PNG)

  15. Set the Bind address.

       `sudo  vi /etc/my.cnf`


       ![Set Bind address](./Images/Set%20Bind%20address.PNG)


       Under !includedir /etc/my.cnf.d add what is shown below.

       Click i for Insert and type the 2 below lines

       [mysqld]
       bind-address=0.0.0.0

       0.0.0.0 means from any IP address

       To save and quit;

       Click ESC

       :wqa!

       Enter

       ![Save and quit_3](./Images/Save%20and%20quit_3.PNG)


       Restart the service.


       `sudo systemctl restart mysqld`

       ![Restart service mysql database](./Images/Restart%20service%20mysql%20database.PNG)


  16. Go back to the web server and open the config.php file.


      `sudo vi wp-config.php`

       ![Open config.php](./Images/Open%20config.php)

  17.  Edit config.php file as shown below.

       Click i for Insert.

       Next to define ( 'DB_NAME', change database_name_here with 'wordpress'). 

       Change the DB password with 'wordpress'

       Get the Private IP address of the database and replace local host with it.You can use it because the web server and the database server reside in the same subnet.

       172.31.31.178

       ![Config.php file](./Images/Config.php%20file.PNG)


       To save and quit;

       Click ESC

       :wqa!

       Enter

       ![Save and quit_4](./Images/Save%20and%20quit_4.PNG)


  18. Restart the web service.

       `sudo systemctl restart httpd`

       ![Restart service web server](./Images/Restart%20service%20web%20server.PNG)


  19. We need to disable the default test page of apache.

      `sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup`

      ![Disable Apache Default Test Page](./Images/Disable%20Apache%20Default%20Test%20Page.PNG)

  20. Test that you can connect from your Web Server to your DB server by using mysql-client.

      `sudo mysql -h 172.31.31.178 -u wordpress -p`

      Enter wordpress for password.

      ![Connect from web server to DB](./Images/Connect%20from%20web%20server%20to%20DB.PNG)


      The screenshot above shows that the connection was successful.


      Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

      ![Show databases](./Images/Show%20databases_2.PNG)


      Exit;

      Change permissions and configuration so Apache could use WordPress:

      `sudo chown -R apache:apache /var/www/html/`

      ![Change permissions](./Images/Change%20permissions.PNG)


      Run `ls -l`

      ![ls -l_4](./Images/ls%204.PNG)


      Another change permissions.

      `sudo chcon -t httpd_sys_rw_content_t /var/www/html/ -R`

      ![Change permissions_2](./Images/Change%20permissions.PNG)


      For connection run these last 2 commands. ;


      `sudo setsebool -P httpd_can_network_connect=1`

      ![Change permissions_3](./Images/Change%20permissions_3.PNG)


      This last one if for DB.

      `sudo setsebool -P httpd_can_network_connect_db 1`

      ![Change permissions_4](./Images/Change%20permissions_4.PNG)

  21. Log to the browser through the link below.

      http://3.135.237.19/wp-admin/install.php

      ![Wordpress browser output](./Images/Wordpress%20browser%20output.PNG)

      Click Continue at the bottom.

      ![Continue](./Images/Continue.PNG)


      Fill your credentials as shown below.

      ![Credentials to install wordpress](./Images/Credentials%20to%20install%20wordpress.PNG)

      Below is the password.

      !WmoTp(Y9nVre7yZY3

      Click Install wordpress.

      ![Output screen for wordpress](./Images/Output%20screen%20for%20wordpress.PNG)

       Click login and use your credentials.

       ![Wordpress login](./Images/Wordpress%20login.PNG)


       ![Wordpress login_2](./Images/Wordpress%20login_2.PNG)


       Complete






































      




       










































    

















     































































     













































































































