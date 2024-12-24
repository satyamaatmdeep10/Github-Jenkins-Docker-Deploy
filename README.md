# Github-Jenkins-Docker-Deploy

### what is GitHub webhook integration ?
GitHub webhook integration is a way for GitHub to automatically notify an external service (like Jenkins or Slack) when specific events occur in a repository, such as pushing code, creating pull requests, or merging branches. It triggers actions like starting a build, deploying code, or sending notifications, enabling seamless automation and real-time updates without manual intervention.

## Overview
This project involves setting up a Jenkins CI/CD pipeline integrated with GitHub webhooks to automate the deployment of a Dockerized application on an EC2 instance. First, a Jenkins server is launched on an Ubuntu-based EC2 instance, with Docker installed for container management. Jenkins is configured to pull code from a GitHub repository using SSH keys for secure access. A declarative pipeline is created in Jenkins to build Docker images and run containers whenever code changes are committed. GitHub webhooks are set up to trigger the Jenkins pipeline automatically, ensuring that new commits rebuild and deploy the updated application seamlessly, reflecting the latest changes in the live environment. This setup enables continuous integration and deployment, automating the entire development-to-deployment process.

### What does the project do?
The project automates the deployment of a web application using Jenkins, Docker, and GitHub. It ensures that every time developers push new code to the GitHub repository, Jenkins automatically builds a new Docker image, deploys it as a container on an EC2 instance, and updates the live application. This setup eliminates manual deployment tasks, providing a seamless, efficient, and continuous deployment process where the latest application changes are instantly reflected in the live environment.

### Solution :-
#### Step1:- 
First, I logged in to the AWS portal and created a new instance with the following configuration: I named the instance "jenkins-server," selected Ubuntu as the AMI, and chose the instance type as t2.micro (free tier). For the key pair login, I created a new key pair named docker.pem and downloaded the .pem file. In the security group settings, I allowed both HTTP and HTTPS traffic. Once these settings were configured, I clicked on "Launch Instance."


#### Step2:- 
Next, I connected to the EC2 instance that I had created. I copied the SSH command from the server:

#### Step3:- 
Then, I navigated to the download folder where the .pem file was placed, opened the terminal in that location, and pasted the SSH command.

#### Step4:- 
Next, I ran the command ssh-keygen on the machine. This generated the public and private keys on the machine:
id_rsa – Private Key
id_rsa.pub – Public Key

#### Step5:- 
Then, I installed Jenkins on the machine by following the instructions from the link Jenkins Installation on Linux. This process also automatically installed Java along with Jenkins.
the link :- https://www.jenkins.io/doc/book/installing/linux/

#### Step6:-
I also installed Docker on the machine by running the command: _“Sudo apt-install docker.io”_

#### Step7:-
Next, I checked if Jenkins and Docker were installed correctly by running the following commands: _jenkins --version_ and _ docker --version_

#### Step8:-
Next, I allowed ports 8080 and 8001 for the machine by modifying the security group. To do this, I found the security group in the VM description and added the following inbound rules:
Port 8080 - For Jenkins access
Port 8001 - For the application
This allowed traffic on these ports to reach the instance.

#### Step9:- 
Next, I copied the public IP of the machine and pasted it into the browser to access the Jenkins portal, using the format: “54.193.49.139:8080”

#### Step10:- 
To unlock Jenkins, I needed the Administrator password. I retrieved it by running the following command: _cat /var/lib/jenkins/secrets/initialAdminPassword_

Paste this password in the “Administrator Password” Column.

#### Step11:- 
Next, I clicked on "Install Suggested Plugins" to install the recommended plugins for Jenkins.

#### Step12:- 
This will now install the suggested plugins automatically, setting up Jenkins with the essential tools for building and deploying applications.


#### Step13:- 
Next, Jenkins prompted me to create the first Admin user. I followed the instructions to set up the user.

#### Step14:- 
I filled in the required fields for creating the first Admin user in Jenkins, which typically includes:
Full Name
Username
Password
Confirm Password
Email Address
After filling out the fields, I clicked Save and Continue to complete the setup.

#### Step15:- 
After completing the setup, the Jenkins homepage will appear with the following elements:
A welcome message.
Options to create a new job, configure Jenkins, manage plugins, and access Jenkins settings.
Information about the current system status.
This is the main dashboard where you can start configuring Jenkins and create pipelines for your projects.

#### Step16:- 
Next, I created a CI/CD pipeline in Jenkins that fetches the code from GitHub. 

#### Step17:- 
From the Jenkins dashboard, I clicked on "New Item" to create a new Jenkins job or pipeline.

#### Step18:- 
Next, I added the following details for the new project:
Name: todo-app
Project Type: Freestyle project
Then, I clicked "OK" to proceed.

#### Step19:- 
In the Description field, I filled in relevant information about the project, such as a brief overview of its purpose or functionality. For example:
“This is a CI/CD pipeline for the Todo App, which fetches code from GitHub and deploys it using Jenkins.”
Once the description was filled in, I continued with the next configuration steps.

#### Step20:- 
In the Source Code Management section, I selected Git. Then, I entered the Repository URL for the GitHub repository.If there were no existing credentials added, I clicked on "Add" next to the Credentials field to configure the authentication method. I provided the necessary credentials (username and password/token) or an SSH key, depending on how the GitHub repository is configured for access.
Once the repository URL and credentials were added, I saved the configuration.

#### Step21:- 
In the Build Step section, I selected "Execute Shell" and entered the following command to build a Docker image and create a container from it: _docker build -t todo-app-image .
docker run -d --name todo-app-container -p 8001:8001 todo-app-image_
This command will:
Build the Docker image named todo-app-image from the Dockerfile in the repository.
Create and start a container named todo-app-container using the built image, mapping port 8001 of the container to port 8001 on the host.
After adding this command, I saved the build configuration.

#### Step22:- 
Next, I clicked on "Build Now" in Jenkins. This triggered the build process, and I could see the build progress in the Build History section on the left-hand side of the dashboard.
As the build ran, Jenkins executed the configured steps, including fetching the code from GitHub, building the Docker image, and running the container. Once completed, I reviewed the build status and logs to ensure everything worked as expected.

#### Step23:- 
In the Output Console, I reviewed the logs generated during the build process. These logs provided detailed information about each step.

#### Step24:- 
After the build was successful, I opened a browser and entered the following in the address bar: <public_ip_of_ec2>:8001
This accessed the application running inside the Docker container on port 8001. If everything was configured correctly, the browser displayed the application hosted by the container.


To achieve the goal of reflecting every GitHub commit in the live web app, I configured a GitHub webhook and enabled Git polling in Jenkins. First, I navigated to the GitHub repository, went to Settings > Webhooks, and added a webhook with the URL in the format http://<public_ip_of_jenkins_server>:8080/github-webhook/. I set the content type to application/json and selected "Push events" to trigger the webhook automatically. Next, in Jenkins, I opened the configuration for the todo-app job, enabled the GitHub hook trigger for GITScm polling option under Build Triggers, and saved the configuration.
To automate the CI/CD pipeline, I updated the build steps in Jenkins to rebuild the Docker image and redeploy the container with the latest code. This included cleanup commands to stop and remove any existing containers before running the updated container. Finally, I tested the webhook by pushing a new commit to the GitHub repository, which successfully triggered the Jenkins job, built the updated image, and redeployed the container, ensuring the live application reflected the changes automatically.

#### Step25:- 
I reconfigured the project with the following updates:
Build Trigger:
Enabled the GitHub hook trigger for GITScm polling option in the Jenkins job configuration to ensure that the pipeline is triggered automatically whenever a new commit is pushed to the GitHub repository.
Description:-
Added a description for the Jenkins job: GitHub webhook integration, to clearly document the purpose of the configuration.
These changes allow the pipeline to detect new commits via the GitHub webhook and automatically execute the build and deployment process.

#### Step26:- 
To complete the process, I installed the Git Integration plugin in Jenkins by navigating to Manage Jenkins > Manage Plugins > Available and searching for "Git Integration." After locating the plugin, I selected it, clicked Install without restart, and waited for the installation to complete. This plugin ensures seamless integration between Jenkins and Git repositories, enabling efficient code fetching and triggering pipelines based on repository changes.

#### Step27:- 
To set up SSH access for GitHub, I followed these steps:
Navigated to GitHub > Settings > SSH and GPG Keys > New SSH Key.
Filled in the details as follows:
Title: satyamaatmdeep10@gmail.com
Key Type: Selected the public key (copied from the file id_rsa.pub generated earlier using ssh-keygen).
After saving the SSH key, GitHub granted secure access for Jenkins to interact with the repository, enabling seamless code fetching for the CI/CD pipeline.

#### Step28:- 
To retrieve the public key, I opened the id_rsa.pub file by running the command: _cat ~/.ssh/id_rsa.pub_
I then copied the content of the file (the public key) to use it for configuring SSH access in GitHub.

#### Step29:- 
To enable GitHub webhook integration for the repository, I followed these steps:
Navigated to GitHub > Repo “react-django-demo-app” > Settings > Webhooks.
Clicked on Add Webhook and filled in the following details:
Payload URL: http://<public_ip_of_ec2>:8080/github-webhook/
(Replaced <public_ip_of_ec2> with the actual public IP of the EC2 instance.)
Content Type: application/json
Trigger Event: Selected Just the push event to ensure the webhook activates only for new commits.
Active: Checked to enable the webhook.
Clicked Add Webhook to save the configuration.
This setup ensures that every time a commit is pushed to the repository, GitHub triggers the Jenkins pipeline to rebuild the Docker image and redeplo

#### Step30:- 
After completing the necessary configurations for the project, I saved the settings to ensure the changes were applied successfully. This included the build triggers, GitHub webhook integration, and any other adjustments made to the Jenkins project.

#### Step30:- 
To test the automation, I made some changes to the code in the repository and pushed them to GitHub. This triggered the Jenkins pipeline automatically through the configured GitHub webhook. The pipeline rebuilt the Docker image and redeployed the container, ensuring the updated code was live on the web application.
