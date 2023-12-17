# CI/CD Pipeline for 10-Tier Microservice Application

This repository contains the CI/CD pipeline for a 10-tier microservice application. The pipeline is designed to work with Amazon EKS and includes the following steps:

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
- Deploy the 10-tier application to the EKS cluster.
