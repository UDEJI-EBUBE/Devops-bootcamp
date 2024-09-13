# Project 4: Setup WordPress Website Using LAMP Stack

|S/N | STEPS                                                      |
|----|---------------------------------------------------------------------|
| 1  |Deploy an Ubuntu Server                                              |
| 2  |Set up your LAMP stack on the server                                 |
| 3  |Configure the wordpress Application                                  |
| 4  |Map the IP address to the DNS A record                               |
| 5  |Validate the WordPress website setup by accessing the web address.   |

## Deploy an ubuntu server
- visit [**Project1**](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) for guidance on spinning up an Ubuntu server.

- You need to set an inbound rule for MYSQL in your security group. Do this by selecting on **security**** and then select **security group**.
![1](img/Screenshot%20(54).png)

- Click on **Edit inbound rule**

- Click on **Add rule**.
![3](img/Screenshot%20(55).png)

- Click on **Custom TCP**① and select **MySQL/Aurora**②.
![4](img/Screenshot%20(56).png)

- You can enter the **ip addresss** you want to allow access but for this specify the source as /30

> [!NOTE]
You can choose 0.0.0.0/0 to permit access from anywhere, but for added security, restrict access to MySQL exclusively to your Web Server’s IP address.

- Open you terminal and access your ubuntu server via ssh

## Install Apache

- To install apache, run the following commands.
`sudo apt update`
`sudo apt install apache2`
![6](img/Screenshot%20(60)~2.png)

-To enable Apache to start on boot, execute **`sudo systemctl enable apache2`①**, and then verify its status with the **`sudo systemctl status apache2`②** command.
![7](img/Screenshot%20(62)~2.png)

- To check if our server is running and accessible both locally and from the Internet by executing the following command: **`curl http://localhost:80`**.
![8](img/Screenshot%20(63)~2.png)

- To test how our Apache server responds to requests from the internet, copy your **public ipv4 address** from your instance.
![9](img/Screenshot%20(64)~2.png)

- Open a web browser of your choice and try accessing the following URL: **`http://<Public-IP-Address>:80`**
if the installtion was complete you should see this page
![10](img/Screenshot%20(66)~2.png)

## Install MYSQL

- Run the command **`sudo apt install mysql-server`**. Confirm the installation when prompted by typing **Y** and pressing enter.
![11](img/Screenshot%20(65)~2.png)

- After the installation run the command **`sudo mysql`** to log in to the MYSQL console

> [!NOTE]
It's recommended that you run a security script included with MySQL to enhance security. Before running the script, set a password for the root user using the default authentication method **mysql_native_password**. For this project, we'll define the password as "**pass**", but you can choose any password you prefer.

- Run the following command to set the password for the root user with the MySQL native password authentication method: **`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'pass';`**. Exit the MySQL shell when you're done by typing **`exit`**.
![12](img/Screenshot%20(67).png)

- Run the command **`sudo mysql_secure_installation`** to start the interactive script. Answer **Y** for yes or press enter to skip any question.
- Set your **password validation policy level**.
![13](img/Screenshot%20(68).png)
![14](img/Screenshot%20(69).png)

> [!NOTE]
I set my password validation policy level to 0 because I don't require much security, as I will be terminating all resources immediately after this project. However, on the job, it's advised to use the strongest level, which is 2.

- Enable MySQL to start on boot by executing **`sudo systemctl enable mysql`①**, and then confirm its status with the **`sudo systemctl status mysql`②** command.

## Install php

- Install PHP along with required extensions by running the following script: **`sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip`**.
![15](img/Screenshot%20(72).png)

- Then run **`sudo apt install php libapache2-mod-php php-mysql`**.

- Confirm the downloaded PHP version by running **`php -v`**.
![16](img/Screenshot%20(73)~2.png)

## Creating A Virtual Host For Your Website Using Apache

- Create the directory for Projectlamp using the 'mkdir' command as follows:
**`sudo mkdir /var/www/projectlamp`①** and assign ownership of the directory to our current system user using:
**`sudo chown -R $USER:$USER /var/www/projectlamp`②**.

- Create and open a new configuration file in Apache's sites-available directory using your preferred command-line editor:
**`sudo vi /etc/apache2/sites-available/projectlamp.conf`**.

- Paste th configiratio text provided below into the file:

```
<VirtualHost *:80>

ServerName projectlamp

ServerAlias www.projectlamp

ServerAdmin webmaster@localhost

DocumentRoot /var/www/projectlamp

ErrorLog ${APACHE_LOG_DIR}/error.log

CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```
![17](img/Screenshot%20(75).png)

- Save your changes by pressing the **`Esc`** key, then type **`:wq`** and press **`Enter`**.

- Run the ls command **`sudo ls /etc/apache2/sites-available`①** to show the **new file②** in the sites-available directory.

- We can now enable the new virtual host using the a2ensite command: **`sudo a2ensite projectlamp`**.
![18](img/Screenshot%20(77)~2.png)

- Run the command `systemctl reload apache2` to activate the new configuration and input your password
![19](img/Screenshot%20(78)~2.png)

- To disable Apache's default website, use the a2dissite command. Type: **`sudo a2dissite 000-default`**.

- To ensure your configuration file doesn’t contain syntax errors, run: **`sudo apache2ctl configtest`**. You should see **"Syntax OK"** in the output if your configuration is correct.

- Finally run: **`sudo systemctl reload apache2`**. This will reload Apache for the changes to take effect.

> [!NOTE]
Our new website is now active, but the web root **`/var/www/projectlamp`** is still empty. Let's create an **`index.html`** file in that location to test that the virtual host works as expected.

- To create the **index.html** file with the content **"Hello LAMP from Dami"** in the /var/www/projectlamp directory, use the following command: **`sudo echo 'Hello LAMP from Dami' > /var/www/projectlamp/index.html`**.

- Access your website through your ip address from your web browser
**`http://<EC2-Public-IP-Address>:80`**

- Remove the index.html file by running the following command: **`sudo rm /var/www/projectlamp/index.html `**

## Enable PHP on the website

In this part we want to make index.php taek precedence over index.html which is the default priority

- Use the command **`sudo nano /etc/apache2/mods-enabled/dir.conf`** to edit the dir.conf file using a text editor.
- Look for the DirectoryIndex directive within this file. It typically looks like this:

```
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```

- Change it to this

```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
![22](img/26%20(1).png)

- save your file and then exit

- Perform the command **`sudo systemctl reload apache2`** for the the changes to take place.

- To create a new file named index.php inside your custom web root folder (/var/www/projectlamp), you can use the following command to open it in the nano text editor: **`nano /var/www/projectlamp/index.php`**.

- This will create a new file. Copy and paste the following PHP code into the new file:

```
<?php

phpinfo();
```

![23](img/Screenshot%20(81).png)

- Once you've saved and closed the file, go back to your web browser and refresh the page. You should see something like this:

![24](img/Screenshot%20(82).png)

> [!NOTE]
This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.

- To remove the file you created run the command **`sudo rm /var/www/projectlamp/index.php`**.

- Navigate to the directory using the cd command **`cd /var/www/html`**, and then download the WordPress installation files using the following command: **`sudo wget -c http://wordpress.org/latest.tar.gz`**
![25](img/Screenshot%20(83).png)

- Extract the files from the downloaded WordPress archive using the command: **`sudo tar -xzvf latest.tar.gz`**
![26](img/Screenshot%20(84).png)

- Run the command **`ls -l`** to confirm the existence of the **wordpress** directory in the current location (`/var/www/html`).
![27](img/Screenshot%20(86).png)

- Check the user running your web server with the command: **`ps aux | grep apache | grep -v grep`**.
![28](img/Screenshot%20(87).png)

- Grant ownership of the WordPress directory and its files to the web server user **(www-data)** by running the command: **`sudo chown -R www-data:www-data /var/www/html/wordpress`**.

## Create a database for wordpress

- Access your MySQL root account with the following command: **`sudo mysql -u root -p`①**. Enter the **password②** you set earlier when prompted.
![30](img/Screenshot%20(89).png)

- To create a separate database named wp_db for WordPress to manage, execute the following command in the MySQL prompt: **`CREATE DATABASE wp_db;`**
![31](img/Screenshot%20(912).png)

> [!NOTE]
This command allows you to create a new database (**wp_db**) within your MySQL environment. Feel free to name it as you prefer.

- To access the new database, you can create a MySQL user account with a strong password using the following command:
**`CREATE USER dami@localhost IDENTIFIED BY 'wp-password';`**
![32](img/Screenshot%20(93).png)
*you can change the password to what you prefer*

- To grant your created user (dami@localhost) all privileges needed to work with the wp_db database in MySQL, use the following commands:

```
GRANT ALL PRIVILEGES ON wp_db.* TO dami@localhost;
FLUSH PRIVILEGES;
```
![33](img/Screenshot%20(93).png)

> [!NOTE]
This grants all privileges **(ALL PRIVILEGES)** on all tables within the wp_db database **(`wp_db.*`)** to the user dami when accessing from localhost. The FLUSH PRIVILEGES command ensures that MySQL implements the changes immediately. Adjust the database name **(wp_db)** and username **(dami)** as per your setup.

- Type **`exit`** to exit the MySQL shell.

- Grant executable permissions recursively (-R) to the wordpress folder using the following command: **`sudo chmod -R 777 wordpress/`**
![35](img/Screenshot%20(930).png)

> [!NOTE]
This command sets read (r), write (w), and execute (x) permissions for the owner, group, and others on all files and directories within the wordpress folder. Using 777 permissions is quite permissive and may not be necessary for all files and folders; consider adjusting permissions based on security requirements.

- Change into the WordPress directory by running the command: **`cd wordpress`**.

## Configure Wordpress
- Rename the sample WordPress configuration file with the command: **`mv wp-config-sample.php wp-config.php`**.
- Edit the **`wp-config.php`** file using the command: **`sudo nano wp-config.php`**.
![45](img/Screenshot%20(94).png)

- Update the database settings in the **`wp-config.php`** file by replacing placeholders like **database_name_here**, **username_here**, and **password_here** with your actual database details.
![46](img/Screenshot%20(95).png)

- Modify the configuration file projectlamp.conf: **`sudo nano /etc/apache2/sites-available/projectlamp.conf`** to update the document root to the directory where your WordPress installation is located.

- After updating the document root to **`/var/www/html`** directory in your editor, save the changes and exit.

![48](img/Screenshot%20(97).png)

- Reload Apache for the changes to take effect: **`sudo systemctl reload apache2`**.

- type **`http://<EC2 IP>/wordpress/`** in you web browser where you will see the installation wizard to and follow the procedure there.
![48](img/Screenshot%20(100).png)

- After successfully installing wordpress, click on log in.

- Fill in the required information to access your wordpress admin dashboard
![49](img/Screenshot%20(101).png)
![50](img/Screenshot%20(102).png)

## Create a record

- Use the steps from [**Project1**](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) to create your a records

-  To update your Apache configuration file in the sites-available directory to point to your domain name, use the command: **`sudo nano /etc/apache2/sites-available/projectlamp.conf`**.

> [!NOTE]
This command opens the **`projectlamp.conf`** file in the nano text editor with superuser privileges **(`sudo`)**. Within the editor, adjust the necessary details to reflect your domain name configuration.

- Ensure that the server settings in your Apache configuration point to your domain name, and that the document root accurately points to your WordPress directory. Once you've made these adjustments, save the changes and exit the editor.

```
<VirtualHost *:80>
    ServerName <Your root domain name>
    ServerAlias <Your sub domain name>
    ServerAdmin webmaster@<Your root domain name>

    DocumentRoot /var/www/html/wordpress

    <Directory /var/www/html/wordpress>
        Options Indexes FollowSymLinks
       # AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```

- To update your **`wp-config.php`** file with DNS settings, use the following command: **`sudo nano wp-config.php`** and add these lines to the file:

```
/** MY DNS SETTINGS */
define('WP_HOME', 'http://<domain name>');

define('WP_SITEURL', 'http://<domain name>');
```
![51](img/Screenshot%20(109)~2.png)

- Reload your server to apply the changes nad visit you domain name to view your wordpress wbsite
![52](img/Screenshot%20(110).png)

- To log in to your WordPress admin portal, visit **`http://<domain name>/wp-admin`**, Enter your **username①** and **password②**, then click on **log In③**.
![53](img/Screenshot%20(111)~2.png)

## Install certbot and Request For an SSL/TLS Certificate
- Use the information from [**Project1**](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) to complete this step.
![54](img/Screenshot%20(114).png)
![55](img/Screenshot%20(115).png)



