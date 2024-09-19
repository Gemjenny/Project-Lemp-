#Step 1: Install NGINX Web Server

To begin the setup, I updated my package index and installed NGINX:

```bash
sudo apt update
sudo apt install nginx
```
After installation, I verified NGINX was running by checking its status:
sudo systemctl status nginx

To ensure the server was working, I opened a browser and visited http://localhost:80 using
```bash
curl http://localhost:80
```
#Step 2: Install MySQL Database Server
Next, I installed the MySQL server with the following command:
```bash
sudo apt install mysql-server
sudo mysql
```
I then set up the root user’s password for added security:
```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```
After that, I exited the MySQL prompt:
```bash
mysql> exit
```

#Step 3: Install PHP
PHP is required to process dynamic content, so I installed PHP and the necessary PHP modules for MySQL:
```bash
sudo apt install php-fpm php-mysql
```

#Step 4: Set Up Project Directory for the Website
I created a directory for my LEMP project where I would store the web files:
```bash
sudo mkdir /var/www/projectLEMP
sudo chown -R $USER:$USER /var/www/projectLEMP
```

#Step 5: Configure NGINX to Serve the Website
To configure NGINX, I created a site configuration file for the project:
```bash
sudo nano /etc/nginx/sites-available/projectLEMP
```
I added the following content to the file:
```bash
server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Then, I enabled the site by creating a symbolic link to the configuration file:
```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
Next, I tested the configuration and reloaded NGINX to apply the changes:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

#Step 6: Add Dynamic Content to the Website
To make the site more dynamic, I added a command to display the public hostname and IP of my cloud instance on the webpage
```bash
sudo echo 'Hello LEMP from hostname! ' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP ' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
#Step 7: Create PHP Info Page
To verify that PHP was working correctly, I created a PHP info file
```bash
<?php
phpinfo();
?>
```
I then accessed http://localhost/info.php to check if the PHP information was displayed correctly


#Step 8: Create MySQL Database and User
To manage my databases, I logged into MySQL:
```bash
sudo mysql
```
I created a new database and user
```bash
CREATE DATABASE 'example_database';
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PasswWord.1';
GRANT ALL ON example_database.* TO 'example_user'@'%';
```
After creating the user, I exited the MySQL prompt
```bash
mysql> exit
```
Finally, I logged in as the new user to verify the connection:
```bash
mysql -u example_user -p
```
