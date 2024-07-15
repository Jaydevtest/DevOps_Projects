# Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins

In previous Project 8 we introduced horizontal scalability concept, which allow us to add new Web Servers to our Tooling Website you have successfully deployed a setup with 2 Web Servers and also a Load Balancer to distribute traffic between them. If it is just two or three servers - it is not a big deal to configure them manually. Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

DevOps is about Agility and the speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is the Automation of routine tasks.

In this project we are going to start automating part of our routine tasks with a free and open-source automation server - Jenkins. It is one of the most popular CI/CD tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named "Hudson".

According to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In our project, we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/<yourname>/tooling will be automatically updated to the Tooling Website.

Our 3 tier architecture will look like this

![3-tier-architecture](Project_9_Images/architecture.png)

## Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

### Step 1 - Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

2. Install JDK (since Jenkins is a Java-based application)

```
sudo apt update
sudo apt install openjdk-11-jre
#Confirm Java installation
java -version
```

![Install_Java](Project_9_Images/java_version.png)

3. Install Jenkins

Refer to official Jenkins documentation for more details.

```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

Make sure Jenkins is up and running

`sudo systemctl status jenkins`

[Install_Jenkins](Project_9_Images/jenkins_status.png)


4. By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound Rule in your EC2 Security Group

5. Perform initial Jenkins setup.

From your browser access http://<Jenkins-Server-Public-IP-address-or-Public-DNS-Name>:8080

[Install_Jenkins](Project_9_Images/jenkins_web.png)

You will be prompted to provide a default admin password

Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

Then you will be asked which plugings to install - choose suggested plugins.

[Install_Jenkins](Project_9_Images/jenkins_web1.png)

Once plugins installation is done - create an admin user and you will get your Jenkins server address.

The installation is completed!

[Install_Jenkins](Project_9_Images/jenkins_web2.png)

### Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a 'build' task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable webhooks in your GitHub repository settings

[Configure_Webhook](Project_9_Images/webhook.png)

[Configure_Webhook](Project_9_Images/webhook1.png)

2. Go to Jenkins web console, click "New Item" and create a "Freestyle project"

[Freestyle_project](Project_9_Images/New_item.png)

[Freestyle_project](Project_9_Images/New_item1.png)

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

[Github_link](Project_9_Images/github.png)

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

[Freestyle_project](Project_9_Images/New_item2.png)

Save the configuration and let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1

[Freestyle_project](Project_9_Images/New_item3.png)

[Freestyle_project](Project_9_Images/New_item4.png)

You can open the build and check in "Console Output" if it has run successfully.

[Freestyle_project](Project_9_Images/New_item5.png)

If so - congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

3. Click "Configure" your job/project and add these two configurations

Configure triggering the job from GitHub webhook:

Configure "Post-build Actions" to archive all the files - files resulted from a build are called "artifacts".

[Freestyle_project](Project_9_Images/configure.png)

[Freestyle_project](Project_9_Images/configure1.png)

[Freestyle_project](Project_9_Images/configure2.png)

[Freestyle_project](Project_9_Images/configure3.png)

Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results - artifacts, saved on Jenkins server.

[Freestyle_project](Project_9_Images/build.png)

[Freestyle_project](Project_9_Images/build1.png)

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as 'push' because the changes are being 'pushed' and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`

[Freestyle_project](Project_9_Images/terminal_artifact.png)

### Step 3 - Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

1. Install "Publish Over SSH" plugin.

On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

On "Available" tab search for "Publish Over SSH" plugin and install it

2. Configure the job/project to copy artifacts over to NFS server.

On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

- Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

- Arbitrary name

- Hostname - can be private IP address of your NFS server

- Username - ec2-user (since NFS server is based on EC2 with RHEL 8)

- Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

[Freestyle_project](Project_9_Images/ssh_config.png)

Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"

Configure it to send all files produced by the build into our previously define remote directory. In our case we want to copy all files and directories - so we use **.

[Freestyle_project](Project_9_Images/configure4.png)

If you want to apply some particular pattern to define which files to send - use this syntax.

Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

[Freestyle_project](Project_9_Images/configure5.png)

I got unstable builds: This was solved by changing ownership and permission to the remote directory

```
sudo chmod 777 /mnt/apps
sudo chown nobody:nobody /mnt/apps
sudo -R chmod 777 /mnt
sudo chown -R nobody:nobody /mnt
```

[Freestyle_project](Project_9_Images/success.png)

Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:

```
SSH: Transferred 25 file(s)
Finished: SUCCESS
```

[Freestyle_project](Project_9_Images/success1.png)

To make sure that the files in /mnt/apps have been updated - connect via SSH/Putty to your NFS server and check README.MD file

`cat /mnt/apps/README.md`

[Freestyle_project](Project_9_Images/success2.png)

If you see the changes you had previously made in your GitHub - the job works as expected.



