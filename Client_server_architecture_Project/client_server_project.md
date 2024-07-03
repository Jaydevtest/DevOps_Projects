# Implememting a Client-Server Architecture using MySQL Database Management System (DBMS)

To demonstrate a basic client-server using MySQL RDBMS, follow the instructions below.

### 1. Create and configure two Linux-based virtual servers (EC2 instances in AWS).

![AWS_Instances](Client-Server_Images/AWS_instances.png)

### 2. 
On mysql-server install MySQL Server software

![Mysql_server_install](Client-Server_Images/install_mysql_server.png)

### 3. 
On mysql-client install MySQL Client software

![Mysql_client_install](Client-Server_Images/install_mysql_client.png)

### 4. 
By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate with each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in Inbound rules in mysql server's security groups. For extra security, do not allow all IP addresses to reach your mysql server - allow access only to the specific IP address of your mysql client.

![inbound-rule](Client-Server_Images/inbound_rule.png)

### 5.
You might need to configure MySQl server to allow connections from remote hosts.

`sudo vi /ect/mysql/mysql.conf.d/mysqld.cnf

Change bind address from '127.0.0.1 to '0.0.0.0'

![bind-address](Client-Server_Images/bind_address.png)

### 6.
From mysql client Linux server connect remotely to mysql server Database Engine without using SSH. You must use the mysql utility to perform this action.

![remote-login](Client-Server_Images/remote_login.png)

### 7.
Check that you have successfully connected to a remote MYSQL server and can perform SQL queries:

`show databases;`

![show-databases](Client-Server_Images/show_databases.png)



