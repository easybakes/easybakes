# Easy Bakes
I have created a baking blog to feature my personal recipes which incorporate a mix of healthy treats and snacks, focusing on nutritious alternatives to popular baked goods and meals. I have chosen to host my server on Amazon EC2 as this offers flexibility, control and customisation of the server environment, allowing for advanced features and scalability. The website is run on an Apache2 webserver, using MySQL for database management, and WordPress to create, edit and manage the site.

### Installing the server
Amazon EC2 Setup
- Sign up to AmazonEC2 (free tier eligible)
- Launch instance
- Choose Ubuntu Server 24.04 LTS (64bit (x86))
- Create new key pair (.pem) - this gives us command line access to the VM
- Allow SSH traffic from anywhere, and HTTP - this enables our server to receive web traffic
- Configure storage - 30gb, gp3 root volume - this allows enough space for the content and images

### Connecting to instance
1. Go to instances on AWS EC2, choose instance and click connect
2. Open an SSH client
3. Locate private key file (key.pem)
4. Ensure key is not publicly viewable
   ```
   chmod 400 "keyname.pem"
   ```
   This limits read permissions to only the files owner or user

5. Connect to instance using its Public DNS
   ```
   ssh -i "keyname.pem" ubuntu@ec2-54-206-105-26.ap-southeast-2.compute.amazonaws.com
   ```
   You should now have remote control of your virtual machine

### Install Apache2 Webserver
Installing Apache2 enables the computer to deliver content through the internet
```
sudo apt update
```
This ensures the apt repositories are updates

```
sudo apt install apache2
```

To test if it works, enter your public IP address into a web browser. It should come up with the Apache2 Ubuntu Default Page.

### Linking Domain Name - Crazy Domains
1. Obtain a domain name from Crazy Domains
2. Log in to Crazy Domains and go to Domain Registration, followed by DNS Settings
3. Select A records, and click edit button to add your servers IP address

### Install Database
```
sudo apt install mysql-server
```
```
sudo mysql_secure_installation
```

### Installing Wordpress
Download and uncip the WordPress installation package
```
wget https://wordpress.org/latest.tar.gz
```
```
unzip -tar -xzf latest.tar.gz
```

Move wordpress to the document root of Apache2
```
sudo mv wordpress/ /var/www/html
```

### Create a MySQL database and username
```
sudo mysql -u root -p
```

This will ask you to enter your root password
You will now be on the mysql command line. Next we will set up a database and user to store and manage data.

``` 
CREATE DATABASE dbname;
```
Replace dbname with your chosen database name

```
CREATE USER username@'localhost' IDENTIFIED BY 'chosenpassword';
```
Replace username with your chosen username and chosenpassword with your password of choice

``` 
GRANT ALL PRIVILEGES ON dbname.* TO 'username'@'localhost';
```
``` 
FLUSH PRIVILEGES;
```
This updates the database with your changes
```
EXIT
```

### Create and edit the wp-config.php file
1. Create a wp-config.php file by copying the wp-config-sample.php file. This provides a new configuration while keeping hte original as a backup
   ```
   cp wordpress/wp-config-sample.php wordpress/wp-config.php
   ```
2. We will now edit the wp.config-php file, replacing each line with our updated information
   ```
   nano wordpress/wp-config.php
   ```
   Find the lines that say define('DB_NAME', 'dbname'); and replace this with your db name
   e.g., ('DB_NAME', 'easybakes');

   Do this for the user and password lines as well

3. Find the Authentication Unique Keys and Salts and replace the text with your generated key values
   You can generate this by visiting https://api.wordpress.org/secret-key/1.1/salt/

4. Remove index.html file (this will be located in var/www/html)
   ```
   sudo rm index.html
   ```

   ```
   sudo systemctl restart apache2
   ```

   When you now visit your domain name, it should direct you to the wordpress page. From here, pick your chosen language, and enter the database and username details you created above. Once this has been done, you should now be able to access your website to edit.
   

### Obtain and Configure SSL/TLS Certificate
This will enable HTTPS encryption on your website.
   ```
   sudo apt install certbot python3-certbot-apache
   ```
   ```
   sudo certbot --apache
   ```
   This will ask you to enter an email address, and agree to the terms of service. It will then ask if you are willing to share your email (this is optional).

   Following this it will ask you to enter the domain name(s) you want on the certificate
   e.g.,
   ```
   easybakes.shop www.easybakes.shop
   ```
Now we will update the WordPress general settings. 
Do this by logging into WordPress> dashboard> security> general. Ensure both WordPress and Site address are https://yourdomain.com
Save changes.

This will likely log you out, and you will need to log back in. 

Mixed Content Errors
Sometimes you may come accross mixed content errors, and some content might not be accessible. To fix this, go to your WordPress dashboard> plug ins. Search 'Better Search Replace', install and activate. 

Once this has been completed, go to Tools> Better Search Replace. Place http://yourdomain.com in the search for, and https://yourdomain.com in the replacement section. Select all database tables, and run as a dry run first. Click 'Run Search/Replace', this will tell you if anything needs updating. You can also run the search for https://youripaddress and replace with https://yourdomain.com to fix any remaining IP addresses. 

If there are any errors, uncheck dry run first and click 'Run Search/Replace'.

### Copyright Licenses
It is important to copyright your work to protect your original ideas and creations. This ensure my work cannot be stolen and passed off.

Including a Creative Commons license provides you with choices on how others can use your work, leaving it up to you how your work may be interpreted and distributed.

Visit https://creativecommons.org/chooser/ to select a Creative Commons license that fits your website. 
This will ask you which license, and the details of your project, providing you with a Creative Commons copyright statement to apply to your project.

This will be placed in the footer of your website.




   


