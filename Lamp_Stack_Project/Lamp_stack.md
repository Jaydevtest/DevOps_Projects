# Lamp Stack Implementation

The Project (LAMP Stack) is a comprehensive program designed for individuals seeking to build and deploy web applications using the LAMP stack. This course offers a hands-on learning experience, guiding participants through the process of creating dynamic websites by combining Linux, Apache, MySQL, and PHP. Throughout the course and project implementation, participants will gain a solid understanding of the LAMP stack components and their roles in web application development. Starting with an introduction to the LAMP stack architecture, learners will explore the benefits and advantages of using this powerful combination of technologies.

The course/project covers essential topics such as setting up a Linux environment, configuring the Apache web server, managing MySQL databases, and writing PHP code for server-side functionality. Participants will learn how to install and configure the necessary software components, ensuring a smooth and optimized development environment. By following the practical examples, and demo shown, and engaging in hands-on exercises, participants will gain proficiency in building dynamic web applications. They will learn how to handle user requests, interact with databases, process forms, and implement security measures. Additionally, the course/project will cover common development frameworks and tools that can enhance productivity and simplify web application development.

The Project (LAMP Stack) goes beyond the basics, delving into advanced topics such as performance optimization, debugging, and deployment strategies. Participants will gain insights into best practices for scaling applications, securing data, and ensuring the robustness of their projects. Upon completion of the course/project, participants will have acquired the skills and knowledge required to develop and deploy web applications using the LAMP stack. Whether pursuing personal projects or working in a professional setting, this project will equip you with the expertise to leverage the power of the LAMP stack and build robust, scalable, and secure web applications.

# Web Stack Implementation (Lamp Stack) in AWS

## What is a Technology stack?

A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating well-functioning software. They are acronyms for individual technologies used together for a specific technology product. some examples are...

-﻿﻿LAMP (Linux, Apache, MySQL, PHP, Python, or Perl)

-﻿﻿LEMP (Linux, Nginx, MySQL, PHP, Python, or Perl) |

-﻿﻿MERN (MongoDB, Express JS, React.JS, NodeJS)|

-﻿﻿MEAN (MongoDB, Express.JS, Angular JS, NodeJS

## Preparing prerequisites

### Step 0 - Preparing prerequisites

To complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

AWS is the biggest Cloud Service Provider and it offers a free tier account that we are going to leverage for our projects.

AWS can provide us with a free virtual server called EC2 (Elastic Compute Cloud) for our needs.

### (Windows) - Connecting to EC2 using Putty.

Remember the private key you downloaded from AWS while provisioning the server? It is a PEM file format. You can open it up to see the content and have a glimpse of what a PEM file looks like.

Now, we are going to use that PEM key to connect to our EC2 instance via ssh.

• Connect to the instance by running

ssh -i ‹private-key-name>.pem ubuntu@<Public-IP-address>

Follow the instructions below to get some work done Installing Apache and Updating the Firewall

### Step 1 - Installing Apache and Updating the Firewall

What exactly is Apache?

Apache HTTP Server is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open-source software available for free. It runs on 67% of all web servers in the world. It is fast, reliable, and secure. It can be highly customized to meet the needs of many different environments by using extensions and modules. Most WordPress hosting
providers use Apache as their web server software. However, websites and other applications can run on other web server software as well. Such as Nginx, Microsoft's IIS, etc.

The Apache web server is among the most popular web servers in the world. It's well documented, has an active community of users, and has been in wide use for much of the history of the web, which
makes it a great default choice for hosting a website.

Install Apache using Ubuntu's package manager 'apt':

#update a list of packages in the package manager

$ sudo apt update

#run apache2 package installation

$ sudo apt install apache2

To verify that apache2 is running as a Service in our OS, use the following command

$ sudo systemctl status apache2

If it is green and running, then you did everything correctly - you have just launched your first Web Server in the Clouds!
Before we can receive any traffic from our Web Server, we need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open an inbound connection through port 80:

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means 'from any IP address').

First, let us try to check how we can access it locally in our Ubuntu shell, run:

$ curl http://localhost: 80

or

$ curl http://127.0.0.1:80|

These 2 commands above actually do pretty much the same - they use the 'curl' command to request our Apache HTTP Server on port 80 (actually you can even try to not specify any port - it will work
anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one - by IP address (in this case IP address 127.0.0.1 corresponds to DNS name 'localhost'
and the process of converting a DNS name to an IP address is called "resolution"). We will touch on DNS in further lectures and projects.

As an output you can see some strangely formatted tests, do not worry, we just made sure that our Apache web service responds to the 'curl' command with some payload.

Now it is time for us to test how our Apache HTTP server can respond to requests from the Internet. Open a web browser of your choice and try to access the following url|

http://<Public-IP-Address>:80

Another way to retrieve your Public IP address, other than to check it in the AWS Web console, is to use the following command:

curl -s http://169.254.169.254/latest/meta-data/public-ipv4|

The URL in the browser shall also work if you do not specify a port number since all web browsers use port 80 by default.

If you see the following page, then your web server is now correctly installed and accessible through your firewall.

It is the same content that you previously got by the 'curl' command but represented in the nice HTML formatting by your web browser.
