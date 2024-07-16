# AUTOMATING PROJECTS 7-10 USING ANSIBLE CONFIGURATION MANAGEMENT

In Projects 7-10, we had to perform lots of manual operations to set up virtual servers, install and configure required software, and deploy our web application.

We will automate the routine tasks with Ansible Configuration Management using declarative language such as **YAML**.

## ANSIBLE CLIENT AS A JUMP SERVER (BASTION HOST)

A Jump Server(Bastion Host) is an intermediary server through which access to the internal network can be provided. In the current architecture we have been working on, the webservers are inside a secured network that cannot be reached directly from the Internet. To access the webservers using SSH, we need to go through a **jump server** which provides better security and reduced attack.

In the diagram below, the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

We will be performing the following tasks:

- Install and configure Ansible client to act as a jump server/Bastion Host.

- Create a simple Ansible Playbook to automate server configuration.

### Install and configure Ansible on EC2 Instance

We will update the **Name tag** on our **Jenkins EC2 Instance** to **Jenkins-Ansible**. We will use this server to run our playbooks.

In the GitHub account, we create a new repository and name it ansible-config-mgt.



