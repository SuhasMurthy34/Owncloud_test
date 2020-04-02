
---
Installing Owncloud Server on Windows 10
---

### Table of Contents

[Introduction](#id1)

[Requirements](#id2)

[Install Ubuntu Linux](#id3)

[Install Apache](#id4)

[Install MySQL](#id5)

[Install PHP](#id6)

[Download and install Owncloud on Windows 10](#id7)

[Configure Apache for Owncloud on Windows 10](#id8)

[Connect to the Owncloud Server](#id9)

>[Add a user account in Owncloud](#id10)
	  
[Connect to the Owncloud server using a desktop client](#id11)


# Introduction <a id="id1"></a>

Owncloud is a suite of client-server software for creating and using file hosting services. 

This guide is intended for users who are proficient with Linux and Windows operating systems and have a basic understanding of Owncloud.
 
This guide enables you to accomplish the following tasks:
* Install and configure the Owncloud 10.0.3 server on the Windows 10 OS.
* Connect to the Owncloud server.
* Add a user account in the Owncloud server.
* Connect to the Owncloud server using a desktop client.

For more detailed installation and configuration steps, refer to [Owncloud Admin guide](https://doc.owncloud.org/server/10.4/admin_manual/).

# Requirements <a id="id2"></a>

* Operating Systems
  + Ubuntu 18.04 LTS
  + Windows 10 Pro 64-bit
* Database: MySQL
* Web Server: Apache 2.0 
* PHP Runtime: PHP 7.1
* Memory: 16 GB RAM
* Web Browser: 
  + Chrome 66 + 
  + Edge (current version on Windows 10)

# Install Ubuntu Linux <a id="id3"></a>

Ubuntu 18.04 on Windows allows you to use the Ubuntu terminal and run Ubuntu command line utilities including bash, ssh, git, apt and so on.

**Note**: To use this feature, navigate to Control Panel, Programs and Features, click **Turn Windows Feature on or off**, and select the **Windows Subsystem for Linux** checkbox.

**Steps**:

1. Download [Ubuntu 18.04 LTS](https://www.microsoft.com/en-in/p/ubuntu-1804-lts/9n9tngvndl3q?rtc=1&activetab=pivot:overviewtab).
2. Click the Ubuntu tile in the Start Menu.
3. Create a username and password.
4. Restart your machine.

# Install Apache <a id="id4"></a>

Apache is available within Ubuntu’s default software repositories and you can install it using conventional package management tools.

**Steps**:

1. Run the following command in the Ubuntu command line:

   ```sudo apt-get install apache2```
   
2. Enter the username and password when prompted and enter Y to continue the installation.

   **Note**: If you get an error message, “E: Failed to fetch http://de.archive.ubuntu.com/ubuntu/pool/main/s/systemd/systemd-sysv_229-4ubuntu11_amd64.deb  404  Not Found [IP: 141.76.1.200 80]”, then run the command:
   
   ```sudo apt update```
   
3.  Enable the following Apache modules and run the following commands to ensure the effective functioning of Owncloud:
    
	a. sudo a2enmod rewrite
	
    b. sudo a2enmod headers
	
    c. sudo a2enmod env
	
    d. sudo a2enmod dir
	
    e. sudo a2enmod mime
    
	**Note**: If you get an error message, “sudo: a2enmod: command not found error”, then run the command: 
   
   ```sudo apt-get install apache2``` 

4. Restart the Apache server using the following command:

   ```sudo service apache2 restart```
   
5. Go to the address bar of the browser and type http://localhost (*your_server_IP_address*) to verify the Apache installation.

   The default Apache landing page is displayed.

# Install MySQL <a id="id5"></a>

The Owncloud server requires a database in which administrative data is stored. The MySQL database software is recommended and lets you create your database and assign a username and password for the database.

**Steps**:

1. Run the following command in the command line to install MySQL:

   ```sudo apt-get install mysql-server mysql-client```
   
2. Use the following command to stop MySQL:

   ```sudo /etc/init.d/mysql stop```
   
3. Use the following command to start MySQL:

   ```sudo /etc/init.d/mysql start```
   
4. Use the following commands to create a MySql database:

   a. sudo mysql
   
   b. CREATE DATABASE <*database_name*>;
   
5. Use the following command to create a username and password for our database.
   
   ```GRANT ALL ON <database_name>.* to '<uname>'@'localhost' IDENTIFIED BY '<password>';```
   
6. Run the Flush privileges command:
   
   ```FLUSH PRIVILEGES;```
   
7. Exit MySQL using the following command:
   
   ```exit```

# Install PHP <a id="id6"></a>

The Owncloud server 10.0.3 is compatible with PHP 7.1. Upgrade to PHP 7.1, see [PHP deprecation section in Owncloud RN](https://doc.owncloud.com/server/10.0/admin_manual/release_notes.html#changes-in-10-0-3).

**Steps**:

1. Run the following commands:

   a. sudo apt-get install software-properties-common 

   b. sudo add-apt-repository ppa:ondrej/php 

   c. sudo apt update 
   
2. Use the following command to install PHP 7.1:
   
   ```sudo apt install php7.1```
   
# Download and install Owncloud on Windows 10 <a id="id7"></a>

Download and unzip the version of Owncloud 10.0.03. The below commands download and extract Owncloud files into its root directory. 

**Note**: If the unzip command is not installed, the system prompts you to install it.

**Steps**:

1. Run the following commands in the command line:

   ```cd /tmp && wget https://download.owncloud.org/community/owncloud-10.0.3.zip```
   
   ```unzip owncloud-10.0.3.zip``` 
   
   ```sudo mv owncloud /var/www/html/owncloud/```

2. Assign the correct permissions for owncloud using the following commands: 
   
   ```sudo chown -R www-data:www-data /var/www/html/owncloud/```
   
   ```sudo chmod -R 755 /var/www/html/owncloud/```
  
  
# Configure Apache for Owncloud on Windows 10 <a id="id8"></a>

Apache installs with a default configuration and therefore you must create a file and replace the directory and other file paths with your file paths.

So, let's create a file, *Owncloud.conf* in Apache that lets you control the Owncloud operations.

**Steps**:

1. Run the following command:

   ```sudo nano /etc/apache2/sites-available/owncloud.conf```
   
2. Add the following lines to the newly created file:

   ```Alias /owncloud "/var/www/owncloud/"```
   
   ```<Directory /var/www/owncloud/>```
   
   ```Options +FollowSymlinks AllowOverride All```
   
   ```<IfModule mod_dav.c> Dav off </IfModule>```
   
   ```SetEnv HOME /var/www/owncloud```
   
   ```SetEnv HTTP_HOME /var/www/owncloud```
   
   ```</Directory>```
   
3. Press Crtl+O to write the files and then CTRL+X to save and exit.
4. Enter Y and press Enter to return to the command line.
5. Create a symbolic link between Apache and Owncloud using the following command:

   ```ln -s /etc/apache2/sites-available/owncloud.conf /etc/apache2/sites-enabled/owncloud.conf```
   
# Connect to the Owncloud Server <a id="id9"></a>

The Owncloud installation wizard lets you install the server and configure the storage and database settings.

**Steps**:

1. Type http://localhost{your_Ubuntu_server_IP_address}/owncloud:8080/ in the address bar of the browser.
2. Enter the username and password for the administrator.
3. Click Storage and Database.
4. Specify the data directory location in the **Data Folder** field.

   **Note**: The ownCloud’s data directory is exclusive to ownCloud and you must configure the data directory location during installation.
5. Select MySQL and enter the database username and password.
6. Click **Finish setup**.

## Add a user account in Owncloud <a id="id10"></a>

The Owncloud administrator can create various users, assign them to groups, and set default quota limits for all users.

**Steps**:

1. Log in to the Owncloud server as an administrator.
2. Select **Users** from the **Admin** drop-down list.
3. Enter the username and password in the **Login Name** and **Password** fields.
4. Click **Create**.
5. Select the **Groups** drop-down list and select the groups to which you want to add the user or click **add group** to add the user to a new group.
6. Set the default storage quota limit from the **Quota** drop-down lists for each user.

# Connect to the Owncloud server using a desktop client <a id="id11"></a>

The Owncloud Desktop Sync Client enables you to connect to your private Owncloud server.

You can create folders on your computer and keep the contents of those folders synced with your Owncloud server.

**Steps**:

1. Log in to the Owncloud server as a user.
2. Click your username and select **Personal**.
3. Click the **Desktop App software** icon.
4. Click **Download for Windows**.
5. Click Download and click Run.
6. Accept the default installation options and click **Install**.
7. Click **Finish**.
   
   The Owncloud Connection wizard displays. 
   
8. Enter the server address in the **Server Address** field and click Next.
9. Enter your username and password and click Next.
   
   Select one of the following options:
   
   * **Sync everything from server**: Syncs all your 
     files and folders with the Owncloud server.
   * **Choose what to sync**: Syncs selected files 
     and folders with the Owncloud server.
	 
10. Click **Connect**.

