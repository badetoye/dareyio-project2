## WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS

This is closely similar in concept to the first project (Project1) @ https://github.com/badetoye/dareio-project1/blob/main/project1-implementation.md

In this project we will implement a similar stack, but with an alternative Web Server – **NGINX**, which is also very popular and widely used by many websites in the Internet.

Step 1 – Installing the Nginx Web Server

>We’ll use the 'apt' package manager to install this package.

Since this is our first time using `apt` for this session on the instance, we will start off by updating the server’s package index and then install the NGINX server.

`sudo apt update`

`sudo apt install nginx`

>To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

`sudo systemctl status nginx`

The output should be similar to the image below.

![nginx-status](https://user-images.githubusercontent.com/40571508/135733370-0f34ea1f-deb0-4564-a0b3-c034c48c1894.PNG)

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is default port that web brousers use to access web pages in the Internet. Sample image below.

![opened-ports](https://user-images.githubusercontent.com/40571508/135733569-71eea377-4fea-43e4-8eaf-4a0fe8db2558.PNG)

Now our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

>First, let us try to check how we can access it locally in our Ubuntu shell, run:

`curl http://localhost:80`

or

`curl http://127.0.0.1:80`

Sample output from the local machine - 

![sample-output-from-local-machine](https://user-images.githubusercontent.com/40571508/135733724-765c4f58-cd59-4c26-b64b-e15da6ae7e70.PNG)

Now it is time for us to test how our Nginx server can respond to requests from the Internet.
Open a web browser of your choice and try to access following url

`http://<Public-IP-Address>:80`

sample output -

![nginx-server-browser-output](https://user-images.githubusercontent.com/40571508/135733983-baa328ec-9647-4f2d-828c-3ebe6b6432cf.PNG)

Another way to retrieve a Public IP address, other than to check it in AWS Web console, is to use following command:

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

Step 2 — Installing MySQL

Now that we have a web server up and running, we need to install a Database Management System (DBMS) to be able to store and manage data for our site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in this project.

>To install mysql server using apt package manager, run:

`sudo apt install mysql-server`

When the installation is finished, it’s recommended that we run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to our database system. Start the interactive script by running:

`sudo mysql_secure_installation`

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

>To test if we are able to log in to the MySQL console, run:

`sudo mysql`

This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. We should see output like this:

![mysql](https://user-images.githubusercontent.com/40571508/135734311-5c457ff3-326e-4b7d-b4c6-ca1833d40b48.PNG)

Step 3 – Installing PHP

We have Nginx installed to serve our content and MySQL installed to store and manage our data. Now we can install PHP to process code and generate dynamic content for the web server.

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. We’ll need to install **php-fpm**, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, we’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

>To install these 2 packages at once, run:

`sudo apt install php-fpm php-mysql`

We have now installed PHP components.

Step 4 — Configuring Nginx to Use PHP Processor

Here we will use **projectLEMP** as our chosen domain name. We will keep the default NGINX server block serving document at this path (/var/www/html) as is and create a new directory structure at this directory (/var/www/) as /var/www/projectLEMP.

>To create the root web directory for our domain, run:

`sudo mkdir /var/www/projectLEMP`

>Next, assign ownership of the directory with the $USER environment variable, which will reference our current system user:

`sudo chown -R $USER:$USER /var/www/projectLEMP`

Then, open a new configuration file in Nginx’s sites-available directory, run:

`sudo nano /etc/nginx/sites-available/projectLEMP`

(*nano editor was chosen to perform this, you may as well use Vim editor if that is what you prefer*)

>Paste in the following bare-bones configuration into the blank editor page:

`#/etc/nginx/sites-available/projectLEMP

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
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}`

When we’re done editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

>To activate our configuration by linking to the config file from Nginx’s sites-enabled directory:

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

This will tell Nginx to use the configuration next time it is reloaded. We can test our configuration for syntax errors by typing:

`sudo nginx -t`

See the sample output below -

![nginx-config-test](https://user-images.githubusercontent.com/40571508/135754968-933ddbbe-d120-4263-b13d-f7b994edbfdb.PNG)

>To disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

>To reload Nginx to apply the changes, run:

`sudo systemctl reload nginx`

Our new website is now active, but the web root /var/www/projectLEMP is still empty. We need to create an index.html file in that location so that we can test that our new server block works as expected. Run the below commands to send some data to the index.html file.

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

>To open our website URL using IP address, run:

`http://<Public-IP-Address>:80`

See the sample output below -

![Capture](https://user-images.githubusercontent.com/40571508/135756107-4de4efff-9fd4-4b6a-927b-e2be4b39e874.PNG)

Our LEMP stack is now fully configured.

Step 5 – Testing PHP with Nginx

We can do this by creating a test PHP file in our document root. Open a new file called **info.php** within our document root in our text editor:

`sudo nano /var/www/projectLEMP/info.ph`

Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:

We can now access this page in our web browser by visiting the domain name or public IP address we’ve set up in our Nginx configuration file, followed by /info.php:

`http://server_domain_or_IP/info.php`

Sample output is shown below -

![php-info-page](https://user-images.githubusercontent.com/40571508/135756339-b4a66f2f-8485-44f9-b1ec-6fd684ee6965.PNG)

It is always a good practice to remove this file as it contains sensitive information about our PHP environment and our Ubuntu web server. You may run the commands below to remove the file.

`sudo rm /var/www/your_domain/info.php`

Step 6 — Retrieving data from MySQL database with PHP

In this step we will create a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.

We will create a database named `example_database` and a user named `example_user`, but you can replace these names with different values.

First, connect to the MySQL console using the root account:

`sudo mysql`

>To create a new database, run the following command from MySQL console:

mysql> CREATE DATABASE ``example_database`;

Now you can create a new user and grant him full privileges on the database you have just created.

We’re defining this user’s password as "**password**", but you should replace this value with a secure password of your own choosing.

`mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

Now we need to give this user permission over the example_database database:

`mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';`

This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on the server.

Now exit the MySQL shell with:

`mysql> exit`

You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

`mysql -u example_user -p`

Notice the -p flag in this command, which will prompt you for the password used when creating the example_user user as shown below.

![enter-password](https://user-images.githubusercontent.com/40571508/135768212-5c5c7186-d35c-4964-9c93-2c2a1a01bcda.PNG)

After logging in to the MySQL console, we can confirm that we have access to the example_database database:

`mysql> SHOW DATABASES;`

See the sample output below.

![show-database](https://user-images.githubusercontent.com/40571508/135768390-44ce48d9-1367-4dac-b43f-0649dd819b02.PNG)

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

`CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id) );`

Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

`mysql> INSERT INTO example_database.todo_list (content) VALUES ("My fourth important item");`

>To confirm that the data was successfully saved to your table, run:

`mysql>  SELECT * FROM example_database.todo_list;`

You’ll see the following output:

![db-output](https://user-images.githubusercontent.com/40571508/135768709-9790c709-7b67-44c4-b3a6-ae4aa5a6f40d.PNG)

After confirming that you have valid data in your test table, you can exit the MySQL console:

`mysql> exit`

Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use nano for that:

`nano /var/www/projectLEMP/todo_list.php`

The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.

>Copy this content into your todo_list.php script:

`<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}`

Save and close the file when you are done editing.

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

`http://<Public_domain_or_IP>/todo_list.php`

You should see a page like this, showing the content you’ve inserted in your test table:

![todo-list](https://user-images.githubusercontent.com/40571508/135768955-881a19fa-a927-4fdc-8f61-52154df33ff4.PNG)

That means your PHP environment is ready to connect and interact with your MySQL server.

In this guide, we have built a flexible foundation for serving PHP websites and applications to our visitors, using Nginx as web server and MySQL as database management system.

*Note that this guide is just for learning purposes*

 ☕ credits [Darey.io Bootcamp](https://www.darey.io/) & [Digital Ocean - How to install linux-Nginx-Mysql-php LEMP stack on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-20-04)
 
 






