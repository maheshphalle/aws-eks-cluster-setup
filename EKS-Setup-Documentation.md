AWS EKS Cluster Setup Documentation

Overview

This document provides a step-by-step breakdown of the commands executed to create, configure, and manage an AWS EKS cluster using aws cli and eksctl. It is designed for beginners who have little or no experience with AWS EKS.

1. What is AWS EKS?

Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes service that allows you to run Kubernetes on AWS without the need to install and operate your own control plane or nodes.

Why Use EKS?

Fully managed Kubernetes service

Scales automatically based on workload demands

Secure with IAM roles and networking integration

Reduces operational overhead

2. Prerequisites

Before you start, make sure you have the following installed on your local machine:

Install AWS CLI

AWS CLI is required to interact with AWS services from the command line.

aws --version

If not installed, follow the official AWS documentation: Install AWS CLI

Install eksctl

eksctl is a CLI tool for creating and managing Amazon EKS clusters.

eksctl version

If not installed, follow the guide: Install eksctl

Install kubectl

kubectl is the CLI tool for interacting with Kubernetes clusters.

kubectl version --client

If not installed, follow: Install kubectl

Configure AWS CLI

Run the following command to configure your AWS credentials:

aws configure

It will prompt you to enter:

AWS Access Key ID

AWS Secret Access Key

Default region name (e.g., us-west-1)

Default output format (leave default as json)

3. Creating an EKS Cluster

Command to Create AWS Cluster

eksctl create cluster \
  --name my-eks-cluster \
  --region us-west-1 \
  --version 1.31 \
  --nodegroup-name standard-workers \
  --node-type t3.micro \
  --nodes 2 \
  --nodes-min 1  \
  --nodes-max 3 \
  --with-oidc \
  --ssh-access \
  --ssh-public-key my-key.pem \
  --managed 

Modified Command with Custom VPC & Subnets

eksctl create cluster \
  --name my-eks-cluster \
  --region us-west-1 \
  --version 1.31 \
  --nodegroup-name standard-workers \
  --node-type t3.micro \
  --nodes 2 \
  --nodes-min 1  \
  --nodes-max 3 \
  --vpc-id vpc-0efa1f6df5f264384 \
  --subnets subnet-0b5fa99833c1c6e20,subnet-09bafaef5b90e79b7,subnet-0cc13b344447978a6,subnet-0e5134cbd501c5218 \
  --with-oidc \
  --ssh-access \
  --ssh-public-key my-key.pem \
  --managed 

Note: t3.micro is not supported for EKS clusters. Instead, we create a t3.medium node group.

4. Managing Node Groups

Create a New Node Group with t3.medium

eksctl create nodegroup \
  --cluster mahesh-eks-cluster \
  --name t3-medium-group \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 3 \
  --region us-west-1 \
  --managed

Delete Older Node Group

eksctl delete nodegroup --cluster mahesh-eks-cluster --name standard-workers --region us-west-1

5. Verifying Cluster Creation

Update kubeconfig for Cluster Access

aws eks --region us-west-1 update-kubeconfig --name mahesh-eks-cluster

Check Cluster Resources

kubectl get pods
kubectl get pods -n tmdatabase
kubectl get svc -n tmdatabase

6. Deploying Kubernetes Resources

Apply Kubernetes Configurations

kubectl apply -f namespace-be.yml
kubectl apply -f be-secret.yml
kubectl apply -f be-deployment.yml
kubectl apply -f db-service.yml
kubectl apply -f be-service.yml

Check Namespaces and Services

kubectl get namespaces
kubectl get pods -n tmbackend
kubectl get svc -n tmbackend

7. Testing with Postman

After the services are running, we can test API endpoints using Postman.

POST Request:

http://a284490c0df0f4b249da4879211ad7ae-1032939870.us-west-1.elb.amazonaws.com:3001/trip

8. Deleting the EKS Cluster

If needed, the cluster can be deleted to free resources.

eksctl delete cluster --name my-eks-cluster --region us-west-1

