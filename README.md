# **Capstone project – ASI Insurance**

**Objective**: To create a micro-service application architecture for an Insurance company through DevOps pipeline and deployment on Docker.

**Tools to use:**

1. Jenkins
2. Github
3. Docker Hub
4. AWS

**Description**

ASI Insurance is facing challenges in improving the SLA to its customers due to its organizational growth and existing monolithic application architecture. It requires transformation of the existing architecture to a microservice application architecture, while also implementing DevOps pipeline and automations. 

The successful completion of the project will enable ASI Insurance to improve its overall application deployment process, enhance system scalability, and deliver better products and services to its customers.

**Task (Activities)**

1. Create the Dockerfile, Jenkinsfile, Ansible playbook, and the source file of the static website 
2. Upload all the created files to GitHub 
3. Go to the terminal and install NodeJS 16 
4. Open the browser and access the Jenkins application 
5. Create Jenkins pipeline to perform CI/CD for a Docker container 
6. Create Docker Hub Credentials and other necessary pre-requisites before running build 
7. Set up Docker remote host on AWS and configure deploy stage in pipeline 
8. Execute Jenkins Build 
9. Access deployed application on Docker container


## **1. Creating Github Repositry and file:**

I have created new git hub repository: <https://github.com/kotianrakshith/CapstoneProject2>

Here I have stored the source file provided by the simplilearn for building the application.

I have created the Dockerfile, Jenkins file and Ansible playbook file also in the git hub:

Jenkins file link: <https://github.com/kotianrakshith/CapstoneProject2/blob/main/Jenkinsfile>

Dockerfile link: <https://github.com/kotianrakshith/CapstoneProject2/blob/main/Dockerfile>

Ansible playbook link: <https://github.com/kotianrakshith/CapstoneProject2/blob/main/ansible-playbook.yml>

As of now these files are empty, we will add update the file as we go step by step testing and building the pipeline

## **2. Install NodeJS 16**

First we will check if the NodeJS is already present in our system or not

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.001.png)

As we can see it is not present, so we will proceed with the installation:

First we will install the repo

`curl -s https://deb.nodesource.com/setup\_16.x | sudo bash`

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.002.png)

Then we can run the install command:

`sudo apt install nodejs -y`

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.003.png)

Now when we check we should see that it is installed:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.004.png)

## **3. Access the Jenkins Application:**

We can access the jenkins through port 8080 if installed.

As I have already installed jenkins in my system I can open localhost:8080 in my browser to access the jenkins:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.005.png)

As I have already setup, it is asking for my passsword and user for login. If not setup it will ask you to go through the initial setup.

## **4. Create Jenkins pipeline:**

First we will create a test pipeline and we will build each step one by one and in this process we will update all our required files in the Github and in the end we will make the necessary changes to run the complete pipeline for the actual app.

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.006.png)

For the first stage we will checkout the git and use mvn clean install to build the jar file

To get the checkout script we are using pipeline syntax to populate the script

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.007.png)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.008.png)

Wiith this we can build the belwo script for this stage:
```
stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/kotianrakshith/CapstoneProject2']])
                sh 'mvn clean install'
            }
        }        
    }
```

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.009.png)

When we build this till here, we can see that this build is complete and successull

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.010.png)

Now we can add build docker image to this scirpt.

For building the docker image Dockerfile is needed. We can use the below code in Dockerfile to create a simple docker image:
<https://github.com/kotianrakshith/CapstoneProject2/blob/main/Dockerfile>

We add this in the github and we add the below script in the pipeline to build the docker image:
```
stage('Build Docker Image'){
            steps{
                script{
                    sh 'docker build -t kotianrakshith/tempapp .'
                }
            }
    }
```
(a test name is used, later it will be changed to correct name when we run the final pipeline)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.011.png)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.012.png)

Now when we build we can see new stage is added and it is also successfull.

Next stage is pushing the docker image to docker hub.

I already have a dockerhub account with id: **kotianrakshith** . I will be using this repository to upload the docker image

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.013.png)

As dockerhub has credentials, I will be using the dockerhub token and using the pipline syntax to change it into secret text:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.014.png)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.015.png)

Using this line we can write the script for pushing the image to dockerhub:
```
stage('Push Docker Image to Dockerhub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpassword')]) {
                    sh 'docker login -u kotianrakshith -p ${dockerhubpassword}'

                    sh 'docker push kotianrakshith/tempapp'
                    }
                }
            }
        }
```

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.016.png)

After you save and build, now you can see another stage added and that it is successfully built.

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.017.png)

That means there should be an image uploaded in the repository

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.018.png) 

As you can see the test image has been uploaded.

Now only the last step is pending which is deploy the container on docker.

## **5. Setup Docker Remote host on AWS.**

For this project I will setup an EC2 instance and install the docker on the virtual machine.

Then I will configure Ansible in my jenkins server with this node so ansible can help in deploying the docker container.

First we will go to launch instance in EC2 instance:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.019.png)

Then chose a os image(im chosing ubutnu image with t2.micro instance type)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.020.png)

Then create a key pair so you can use it to login to your system:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.021.png)

Then we keep all the configuration as it is and create the EC2 instance.

Then we edit inbound rules to add the port 8081, so we can access the application when we deploy it.

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.022.png)

Now we connect connect to the ec2 instance and we install docker.

First we upgrade the system

`sudo apt update && sudo apt upgrade -y`

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.023.png)

Now we install the packages, keys, required repositories:
```
sudo apt install ca-certificates curl gnupg lsb-release

sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.024.png)

Now run the update command again(sudo apt update)

Now install the docker ce:

`sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.025.png)

Now check the docker version:

`docker -v`

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.026.png)

If you check the docker you see it is running:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.027.png)

Then you can add your user for docker group

`sudo usermod -aG docker $USER`

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.028.png)

Then reload the session using command: `newgrp docker`

(As Im using the simplilearn AWS system that resets after few hours, I may be using different vms as I progress)

Now we will setup our Ansible installed on our jenkins system with passwordless access to this node.

First create public key pair using ssh-keygen command:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.029.png)

Then copy the public key and put in the authorized\_keys file in the EC2 instance

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.030.png)

Now if i run the ssh command it should allow you to login 

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.031.png)

Let us add this node as a host in ansible host file

We will go to 

`sudo vi /etc/ansible/hosts`

Here i will add it as a group docker
```
[docker]

ubuntu@3.82.156.152
```

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.032.png)

Now we will ping to see if it works

`ansible -m ping docker`

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.033.png)

Now we can write the ansible playbook for deploying the docker and write the final part of the script for our deployment.

<https://github.com/kotianrakshith/CapstoneProject2/blob/main/ansible-playbook.yml>

And the script for the same
```
stage('Execute Ansible Playbook'){
            steps{
                    sh 'ansible-playbook ansible-playbook.yml'
            }
        }
```

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.034.png)

First it failed :

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.035.png)

And as the error was it was unable to ssh into ec2 instance, we had to repeat adding public key for the jenkins user 

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.036.png)

Then we added that in the authorized\_keys file in ec2 instance

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.037.png)

Then we ssh into the instace to see if we were able to ssh correctly

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.038.png)

Then we run the build again and we can see that it was success:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.039.png)


## **5. Execute the Jenkins build:**

Now that we have tested, we can create the proper jenkins file, ansible file by just changing the naming in our app.

We will use the name _insuranceapp_ for this application.

I have made the changes to the code and uploded all the file in the github

Jenkins file link: <https://github.com/kotianrakshith/CapstoneProject2/blob/main/Jenkinsfile>

Dockerfile link: <https://github.com/kotianrakshith/CapstoneProject2/blob/main/Dockerfile>

Ansible playbook link: <https://github.com/kotianrakshith/CapstoneProject2/blob/main/ansible-playbook.yml>


Now we will create the actual final pipeline using jenkins file:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.040.png)

We will give description and github url:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.041.png)

In the pipeline we are choosing pipeline script from SCM:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.042.png)

Give the correct branch name and jenkinsfile name

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.043.png)

Once saved you can build the pipeline:

As we have tested all the script before it should work as expected and build correctly.

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.044.png)

Now we can check dockerhub if the image has been uploaded:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.045.png)

Now let us check in the ec2 instance if the container is deployed:

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.046.png)

As we can see it is deployed correctly.

## **6. Testing the deployment:**

Now as we have deployed in aws and used port 8081, we should we able to access our deployed applcation through public ip of the ec2 instance.

_publicip:8081_ in our case 3.82.156.152:8081

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.047.png)

As we expected it is loading correctly

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.048.png)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.049.png)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.050.png)

![](/readmeimages/Aspose.Words.407fea5c-18d9-4a32-8259-28127e6336de.051.png)

So that concludes the project, we can improve on this project by making this an automated build by using poll scm or by using webhooks so it will run whenever there is a build made. But you can also click on build now whenever there is a change done and it should deploy the updated application to the docker container in your ec2 instance.
