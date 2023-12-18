# CI/CD Pipeline for 10-Microservice Application

This repository contains the CI/CD pipeline for an application having 10 different microservices. The pipeline is designed to work with Amazon EKS and includes the following steps:

## 1. IAM User Setup

An IAM user was created with specific policies tailored for working with Amazon EKS.

## 2. VM Preparation

A Virtual Machine (EC2 instance) was set up on AWS. The following essential CLIs were installed:

- awscli
- kubectl
- eksctl

Additionally, SonarQube was installed for static code analysis and Docker for containerizing the microservices.

## 3. AWS Connection

The VM was connected to AWS using AWSCLI, utilizing the IAM user security credentials.

## 4. EKS Cluster Creation

An EKS cluster was created using the EKSCTL CLI. The process started with the Master Node and then added Worker Nodes with Autoscaling enabled.

## 5. Service Account and Role Configuration

A Service Account was established within EKS and a ROLE with specific access permissions for CRUD operations in EKS. The role was properly bound to the service account.

## 6. Jenkins Setup

A Jenkins server was configured to streamline the deployment process. SonarQube, Docker, and Kubernetes were configured inside Jenkins.

## 7. Pipeline Creation

A pipeline was developed to perform the following tasks:

- Perform static code analysis using SonarQube.
- Containerize the microservices using Docker.
- Push the images to DockerHub.
- Deploy the application to the EKS cluster.

# Usage

### Creating an IAM user with required permissions



<img width="698" alt="iam-policies" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/5c25891a-fee8-47fd-bd18-41f2adf42ebd">



Add above permissions to the IAM user so that it can interact with the EKS cluster

### Configuring EC2 instance and installing required tools


Create a `t2.large` EC2 instance with 30GB of EBS volume. Use the below set of commands to setup the required tools

- Install AWS CLI
  
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
```

- Install Kubectl for EKS

```
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.28.3/2023-11-14/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
```

- Install EKS

```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
```

- Install JDK 17 and Jenkins

```
sudo apt install fontconfig openjdk-17-jre -y
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
```

-  Install Docker and add jenkins user to docker group

```
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo systemctl restart docker
```

- Install SonarQube using a Docker image

```
docker run -d -p 9000:9000 sonarqube:lts-community
```

<img width="602" alt="installations-1" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/5df4d48f-6c1e-4a59-a63b-61011c32b026">

<img width="917" alt="jenkins-install" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/c492c208-afb0-4872-81d7-f14b98c9cb82">

<img width="916" alt="docker-sonarqube-install" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/e103270c-e766-4563-9566-d44bacf57d16">

> Verifying the installations of the required tools

### Connecting the EC2 instance to AWS

The EC2 instance was connected to the AWS using AWS CLI and then using the command `aws configure` to enter the access key ID and secret access key details

### Creating EKS cluster

Create the EKS cluster using the below command:

```
eksctl create cluster --name=cluster-name \
		      --region=ap-south-1 \
		      --zones=ap-south-1a,ap-south-1b \
		      --without-nodegroup

eksctl utils associate-iam-oidc-provider \
    --region ap-south-1 \
    --cluster cluster-name \
    --approve

eksctl create nodegroup --cluster=cluster-name \
			--region=ap-south-1 \
			--name=node2 \
			--node-type=t3.medium \
			--nodes=2 \
			--nodes-min=2 \
			--nodes-max=3 \
			--node-volume-size=20 \
			--ssh-access \
			--ssh-public-key=pem-file-name \
			--managed \
			--asg-access \
			--external-dns-access \
			--full-ecr-access \
			--appmesh-access \
			--alb-ingress-access
```

It will create the master and the worker nodes seperately with auto scaling and load balancing enabled

### Jenkins Setup
- Login into the Jenkins
- Install the plugins related to SonarQube, Docker and Kubernetes

<img width="959" alt="jenkins-plugins" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/fd9c6451-e054-4810-b83e-d99f4a0ecdf9">
  

- Go to Dashboard > Manage Jenkins > Tools and add SonarQube Scanner and Docker installations

<img width="908" alt="tool-config-1" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/b386df5e-dc6f-417d-b7cf-f4f17571a5d0">
<img width="873" alt="tool-config-2" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/b9d2efb6-87d6-4eb3-ad62-59e9c7798d7d">


- Go to Dashboard > Manage Jenkins > System and setup the SonarQube server with the server URL and the authentication token generated from SonarQube

<img width="912" alt="sonar-server-congif-jenkins" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/3974723b-605d-4de1-a281-ce213369130e">


### Modifying the security group in EKS cluster
Go to the addtional security group inside the EKS cluster and allow `All Traffic` under inbound rules
<img width="750" alt="eks-SG" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/e2aac07e-691e-4426-9b0f-e076d1c9db90">


### Service Account and Role
In order to allow jenkins user to be able to communicate with the EKS cluster and perform deployments, create a service account and a role with specific access permissions and then bind that service account to the role.
The service account, role and bind manifest files are located inside `k8s` folder. Apply those files using the `kubectl apply -f <filename>` command.


### Creating a secret for the service account
Create a secret.yaml file for jenkins so that it can authenticate with the cluster. The file can be accessed from the `k8s` folder. Generate the secret by using the following command:

```
kubectl describe secret mysecretname -n webapps
```

### Setting up the CI/CD pipeline
In order to communicate with the DockerHub repository, store the credentials inside jenkins. Similarily, store the secret token for the service account. The Jenkinsfile for the pipeline is present inside the repository. Once the pipeline is passed successfully use `kubectl get pods` and `kubectl get svc` to get the list of running pods and the load balancer IP. Hit the IP in the browser to view the application deployed.

<img width="821" alt="pipeline" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/a15d701b-7790-474a-9179-4361cfc18bf1">

<img width="551" alt="pods" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/88358e26-ac01-4980-9505-29488748f811">

<img width="919" alt="services" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/fdc0a360-e5c6-42d5-94e9-9cd17d6887a2">

<img width="956" alt="app" src="https://github.com/devops-maestro17/decaFlow-CICD/assets/148553140/fa665f28-760b-4289-a840-068e0e0fff22">





