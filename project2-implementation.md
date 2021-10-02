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

