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

Update the repository and Install Ansible

`sudo apt update -y && sudo apt install ansible`

Update the repository and Install Ansible

`sudo apt update -y && sudo apt install ansible`

Configure Jenkins build job to save your repository content every time there is an edit. Go to "configure" and edit the following:

Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

Configure the "ansible-config-mgt" to connect to Jenkins through webhook to trigger a build automatically. In the "ansible-config-mgt" repository, we click on settings. Click on "webhook"-Then click on "add webhook".

In the "project URL", put in the Jenkins IP address and select "application/json" in the "content-type".

`<jenkins-IP-address>:8080/github-webhook/`

Then click on "add webhook".

Save the configuration and run "build now" to connect jenkins to our repository

Click "Configure" your job and add these two configurations

Configure triggering the job from GitHub webhook and Configure "Post-build Actions" to archive all the files.

click on add post-build actions and click "Archive the Artifact" then save

Now, go ahead and make some changes in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

### Step 3: Prepare your development environment using Visual Studio Code

1. The first part of 'DevOps' is 'Dev', which means you will be required to write some codes and you shall have proper tools that will make your coding and debugging comfortable - you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and source-code Editors for different languages with their advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs - Visual Studio Code (VSC), you can get it here.

2. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

3. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance. See Project 1 Step V to learn more about cloning git repositories.

### Step 4: - Begin Ansible Development

1. In your ansible-config-mgt GitHub repository, create a new branch "feature" locally that will be used for the development of a new feature.

2. Checkout the newly created feature branch to your local machine and start building your code and directory structure

3. Create a directory and name it Playbooks - it will be used to store all your playbook files.

4. Create a directory and name it inventory - it will keep your hosts organized.

5. Within the playbooks folder, create your first playbook, and name it common.yml

6. Within the inventory folder, create an inventory file () for each environment (Development, Staging Testing, and Production) dev, staging, uat, and prod respectively. These inventory files use .ini languages style to configure Ansible hosts. Your repository should look like this

### Step 5: Setup Ansible Inventory

In Ansible, an inventory file is a simple text file that defines the hosts and groups of hosts that Ansible can manage. It serves as a source of truth for Ansible to know which hosts it should target when running tasks and playbooks. The inventory file is a fundamental concept in Ansible and is used to organize and manage your infrastructure.

We need setup our ssh keys for ansible so it can have access to our host (remote servers) when running playbooks for our hosts.

This can be done using an SSH agent. An SSH agent allows you to securely store and manage your SSH private keys and provides a convenient way for ansible to authenticate with remote hosts without repeatedly entering your SSH key.

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

Confirm the key has been added with the command below, you should see the name of your key

`ssh-add -l `

Since we have our SSH key for our host stored with SSH agent so ansible can use this, ansible also need to know the host to manage. We can do this with an inventory file

i. Open the inventory/dev.yml file we created earlier

`sudo nano /inventory/dev.yml`

ii. Paste the below information

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user=ec2-user 

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user=ec2-user
<Web-Server2-Private-IP-Address> ansible_ssh_user=ec2-user

[db]
<Database-Private-IP-Address> ansible_ssh_user=ec2-user 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user=ubuntu
images
```

### Step 6: - Create a Common Playbook

It is time to start giving Ansible the instructions on what you need to be performed on all servers listed in inventory/dev.

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code:

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  become: yes
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest


- name: update LB server
  hosts: lb
  become: yes
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```

Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

Create a directory and a file inside it Change timezone on all servers Run some shell script

### Step 7: - Update GIT with the latest code
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes - it is also called "Four eyes principle".

Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

Use git commands to add, commit and push your branch to GitHub.

```
git status
git add <selected files>
git commit -m "commit message"
```
push changes to a remote repositories

`git push`

Wear the hat of another developer for a second, and act as a reviewer.

If the reviewer is happy with your new feature development, merge the code to the master branch on your local computer, commit and push changes to your remote repository.

Once your code changes appear in master branch - Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server as we configured it to in Step 2

jenkins

### Step 8: - Run first Ansible test
Now, it is time to execute ansible-playbook command and verify if your playbook works:

Setup your VSCode to connect to your instance as demonstrated by the video above. Now run your playbook using the command:

`cd ansible-config-mgt`

ansible-playbook -i inventory/dev.yml playbooks/common.yml
ansible

Note: Make sure you're in your ansible-config-mgt directory before you run the above command.

You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

wireshark

Our architecture should look like this

final architecture

Side Study
Learners should look into Ansible Modules to deepen knowledge of ansible playbooks. Ansible Modules are the building blocks of Ansible playbooks, and understanding them is fundamental to working effectively with Ansible.

Real Life Scenarios Using Ansible

In a DevOps scenario, you want to implement a CI/CD pipeline for your project. Ansible can play a crucial role in this process. You can use Ansible to automate the provisioning and configuration of testing and production environments. This ensures that your CI/CD pipeline is reliable and reproducible, allowing you to deliver software updates to your users with confidence.

Conclusion

Ansible Automation is a powerful tool that can simplify and streamline various aspects of IT and infrastructure management. This project also covered jenkin for integration. By learning Ansible and Jenkins, you can automate tasks, reduce manual errors, and ensure consistency in your operations. Key takeaways from this project include: Understanding the fundamentals of Ansible and its use cases. Proficiency in writing Ansible playbooks to automate tasks. The ability to apply Ansible in scenarios such as server configuration, application deployment, and CI/CD pipelines.

