# Wordpress
WordPress is a popular, open-source content management system (CMS) that enables users to create and manage websites easily. Known for its flexibility, extensive plugin ecosystem, and user-friendly interface, it supports various types of websites, from blogs to complex e-commerce sites, making it ideal for both beginners and developers.


![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/23/WordPress_6.4_Dashboard.png/1280px-WordPress_6.4_Dashboard.png)


Here's a step-by-step guide to install WordPress with Nginx, MySQL, and an SSL certificate on Ubuntu 22.04:

### 1. **Update Your System**

First, make sure your system is up-to-date:

```bash
sudo apt update
sudo apt upgrade
```

### 2. **Install Nginx**

Install Nginx web server:

```bash
sudo apt install nginx
```

### 3. **Install MySQL**

Install MySQL server:

```bash
sudo apt install mysql-server
```

Secure the MySQL installation:

```bash
sudo mysql_secure_installation
```

### 4. **Create a MySQL Database and User**

Log into the MySQL shell:

```bash
sudo mysql
```

Create a database and user for WordPress:

```sql
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 5. **Install PHP**

Install PHP and the necessary extensions:

```bash
sudo apt install php-fpm php-mysql php-xml php-gd php-mbstring php-curl
```

### 6. **Download and Install WordPress**

Navigate to your web directory and download WordPress:

```bash
cd /var/www/
sudo wget https://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo mv wordpress my_website
sudo chown -R www-data:www-data /var/www/my_website
sudo chmod -R 755 /var/www/my_website
```

### 7. **Configure Nginx for WordPress**

Create a new Nginx server block for WordPress:

```bash
sudo nano /etc/nginx/sites-available/wordpress
```

Add the following configuration:

```nginx
server {
    listen 80;
    server_name your_domain.com;
    root /var/www/my_website;

    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock; # Adjust PHP version if necessary
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Create a symbolic link to enable the site:

```bash
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
```

Test the Nginx configuration:

```bash
sudo nginx -t
```

Reload Nginx:

```bash
sudo systemctl reload nginx
```

### 8. **Install SSL Certificate**

Install Certbot:

```bash
sudo apt install certbot python3-certbot-nginx
```

Obtain an SSL certificate:

```bash
sudo certbot --nginx -d your_domain.com
```

Certbot will automatically configure SSL for your Nginx server block. Verify that the SSL configuration is correct and restart Nginx:

```bash
sudo systemctl restart nginx
```

### 9. **Complete WordPress Installation**

Visit `https://your_domain.com` in your web browser to complete the WordPress installation. You will be prompted to set up WordPress, including connecting it to the database you created earlier.

### 10. **Secure Your WordPress Installation**

- **Update WordPress and plugins regularly.**
- **Use strong passwords for your WordPress admin and database.**
- **Install a security plugin for additional protection.**

You should now have WordPress running on Nginx with MySQL and SSL on Ubuntu 22.04.
