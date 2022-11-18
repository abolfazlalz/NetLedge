I prefer to deploy a Laravel service using Docker. But sometimes it may be necessary to do this without Docker.

#### Prerequisites
[Install nginx](obsidian://open?vault=Linux&file=Nginx%2FNginx%20in%20Docker%20Container)

Lets start

## Step 1 — Installing Package Dependencies

Before we start we need to install some php prerequisites.
For Laravel, we need PHP language, composer package management and some PHP extensions.
The PHP extensions you’ll need are for multi-byte string support and XML support. You can install these extensions, Composer, and `unzip` (which allows Composer to handle zip files) at the same time.

```bash
sudo apt-get install php7.0-mbstring php7.0-xml composer unzip
```

Now that the package dependencies are installed, we’ll create and configure a MySQL database and dedicated user account for the app.

## Step 2 - Configure MySQL Database

Log into the MySQL `root` administrative account.

```bash
mysql -u root -p
```

You will be prompted for the password you set for the MySQL **root** account during installation.

Start by creating a new database called `laravel`, which is what we’ll use for the website. You can choose a different name, but make sure to remember it because you’ll need it later.

```mysql
CREATE DATABASE laravel DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

Next, create a new user that will be allowed to access this database. Here we use `laraveluser` as the username, but you can customize this too. Remember to replace `password` in the following line with a strong and secure password.

```mysql
GRANT ALL ON laravel.* TO 'laraveluser'@'localhost' IDENTIFIED BY 'password';
```

Flush the privileges to notify the MySQL server of the changes.

```mysql
FLUSH PRIVILEGES;
```

and exit mysql.

## Step 3 — Setting Up Application

In Linux, it is better to place website projects inside the /var/www/html folder.

```bash
sudo mkdir -p /var/www/html/project_name
```

Next, change the ownership of the newly created directory to your user, so that you will be able to work with the files inside without using `sudo`.

```bash
sudo chown abolfazl:abolfazl /var/www/html/project_name
```

Move to the new directory and clone the demo application using Git.

```bash
cd /var/www/html/quickstart
git clone https://github.com/laravel/quickstart-basic .
```

## Configuring the Application Environment

Make a copy from `.env.example` file and name it to `.env`.

And open `.env` file, fill required values related to database.

Next you need to migrate your database:

```bash
php artisan migrate
```

## Configuring Nginx

The application directory is owned by our system user, **abolfazl**, and is readable but not writable by the web server. This is correct for the majority of application files, but there are few directories that need special treatment. Specifically, wherever Laravel stores uploaded media and cached data, the web server must be able not only to access them but also to write files to them.

Let’s change the group ownership of the `storage` and `bootstrap/cache` directories to **www-data**.

```bash
sudo chgrp -R www-data storage bootstrap/cache
```

Then recursively grant all permissions, including write and execute, to the group.

```bash
sudo chmod -R ug+rwx storage bootstrap/cache
```

We now have all the demo application files in place with the appropriate permissions. Next, we need to alter the Nginx configuration to make it correctly work with the Laravel installation. First, let’s create a new server block config file for our application by copying over the default file.

```
sudo vim /etc/nginx/sites-available/example.com
```

There are few necessary changes you’ll have to make:

-   Removing the `default_server` designation from `listen` directives,
-   Updating the web root by changing the `root` directive,
-   Updating the the `server_name` directive to correctly point to a domain name for the server,
-   Updating the request URI handling by changing the `try_files` directive.

The modified Nginx configuration file will look like this:

```nginx
server {
	listen 80;
	listen [::]:80;
	
	. . .
	
	root /var/www/html/quickstart/public;
	index index.php index.html index.htm index.nginx-debian.html;
	server_name example.com www.example.com;
	location / { 
	try_files $uri $uri/ /index.php?$query_string;
	}
	
	. . . 
}
```

When you’ve made the above changes, you can save and close the file. We have to enable the new configuration file by creating a symbolic link from this file to the `sites-enabled` directory.

```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

And finally reload Nginx to take the changes into account.

```bash
sudo systemctl reload nginx
```

Now that Nginx is configured to serve the demo Laravel application, all the components are set up.

#nginx 