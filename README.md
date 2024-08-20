# AWS-EKS-project
## AWS EKS Project Overview

This project demonstrates the setup and deployment of applications on Amazon Elastic Kubernetes Service (EKS) with a focus on configuring networking, security, and service exposure. 

Note: This project was carried out using the AWS root account for setup and management.
Prerequisites

   1 An AWS account (root account used for this project).
   
   2 AWS CLI and kubectl installed and configured.
   
   3 Basic understanding of Kubernetes and AWS services.

# Project Steps
## 1. Creating an AWS Account and Setting up IAM Users

   1 Create an AWS Account:
   
     + Visit AWS and click on "Create an AWS Account."
     + Follow the instructions to enter your email, password, and payment information.

   2 Access AWS Management Console:
   
      * Verify your email and log in to the AWS Management Console.

   3 Set up Multi-Factor Authentication (MFA):
   
     * Enhance account security by setting up MFA.

   4 Create IAM Users:
   
      * Navigate to IAM in the AWS Management Console.
     * Add users with necessary permissions and store access keys securely.

    Note: This project was completed using the root account. For better security, create and use IAM users and roles with least privilege for managing AWS resources.

## 2. Configuring the AWS CLI and kubectl

   1 .Install AWS CLI:
   
      * Download and install the AWS CLI. Follow instructions specific to your operating system.

   2 .Configure AWS CLI Credentials:
   
      * Run aws configure in your terminal and enter your IAM user's access key ID, secret access key, region, and output format.

    3 .Install kubectl:
    
       * Download and install kubectl. Instructions can be found in the official Kubernetes documentation.

   4 .Configure kubectl for EKS:
   
     * Update your kubeconfig file using:
     
  - aws eks update-kubeconfig --name your-cluster-name
   
     * Verify by running kubectl get nodes.

## 3. Preparing Networking and Security Groups for EKS

 1  . Create an Amazon VPC:
 
       * Set up a VPC with public and private subnets.

  2 . Configure Security Groups:
  
        * Create and configure security groups for your EKS worker nodes, defining inbound and outbound rules.

  3 . Set Up Internet Gateway (IGW):
  
        * Create and attach an IGW to your VPC.
        * Update route tables to allow internet access.

   4 .Configure IAM Policies:
       * Create IAM policies and attach them to roles used by EKS worker nodes.

       Note: I used default VPC



# Project Process

After preparing the networking and security groups for the EKS cluster, the next steps involved setting up the EKS cluster itself and deploying the application. Here’s a detailed overview of the process:

  ## 1 Creating the EKS Cluster:
    
     1 .I created an Amazon EKS cluster and used the default Fargate profile, which is named fargate by default. This Fargate profile enables the cluster to run pods on AWS Fargate, a serverless compute engine that automatically manages the underlying infrastructure for you.

   ## 2 Setting Up the game-2048 Namespace:
   
     1 .Within the EKS cluster, I created a namespace called game-2048. This namespace is used to isolate and manage resources related to the application deployment. Here’s the YAML configuration used to create the namespace:

  
## 3 Deploying the Application:

   1 .In the game-2048 namespace, I deployed the application using a Kubernetes Deployment resource. The deployment specifies the container image to use and the number of replicas. Here’s the YAML configuration for the Deployment:


##  4 Creating a Service:

  1. A Kubernetes Service of type NodePort was created to expose the application internally within the cluster. This service routes traffic to the pods managed by the deployment. The YAML configuration for the Service is as follows:




## 5 Configuring Ingress:

    1 .To manage external access to the application, I configured an Ingress resource within the game-2048 namespace. This Ingress uses the AWS Application Load Balancer (ALB) and routes HTTP traffic to the service. The YAML configuration for the Ingress is:



## 6 Installing OIDC for LoadBalancer Integration:

   1 . I installed and configured OpenID Connect (OIDC) to enable authentication and authorization for the LoadBalancer. OIDC integration allows the LoadBalancer to authenticate and authorize requests securely.


## 7 Deploying the LoadBalancer:

   1 .Finally, I created a LoadBalancer service in the default namespace to manage external access to the application. The LoadBalancer was configured to work with the OIDC setup, providing a secure and scalable way to expose the application
    
    .

  # Service Exposure and Troubleshooting

During the development of this project, I initially exposed services using an AWS LoadBalancer type service. When deploying this service, an external IP address was allocated successfully, but accessing this IP address directly did not yield the expected application output. Instead, the application only responded correctly when the external IP was forwarded to a local host.

This issue occurred because the LoadBalancer service, while providing an external IP, did not automatically handle traffic routing and DNS resolution as needed for the application. The external IP did not map correctly to the service within the Kubernetes cluster, resulting in the application being inaccessible through the LoadBalancer's external IP address.

To address this problem, I switched to using an Ingress resource. The Ingress controller manages external access to the services within the Kubernetes cluster, providing more advanced routing capabilities. By configuring an Ingress, I was able to define rules for routing HTTP(S) traffic to the appropriate services based on domain names or paths. This setup resolved the issue, allowing the application to be accessed reliably through the Ingress without needing to manually forward the IP address to a local host.

In summary, while the LoadBalancer service provided an external IP, it was not sufficient for proper traffic management and routing. The Ingress resource offered a more robust solution for managing external access and routing, ensuring that the application was accessible and functioning as intended.
