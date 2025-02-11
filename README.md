# Deployment of Three-Tier MERN stack Web Application on EKS

## Project Overview

This project demonstrates the deployment of a Three-Tier Web Application using ReactJS, NodeJS, and MongoDB, on AWS EKS.

## Architecture

The application  consists of:

- A frontend microservice written using ReactJS
- A backend microservice written using NodeJS
- Data is stored in MongoDB

## Prerequisites

- AWS CLI configured with appropriate credentials
- AWS eksctl
- Helm
- Kubectl 

## Project Structure

```
.
├── docker-compose/
├── backend-app/
├── frontend-app/
├── manifests/
│ ├── backend/
│ ├── frontend/
│ ├── mongodb/
└── README.md
```

## Usage

1. Clone this repository
2. Navigate to the project directory
3. Setup EKS cluster

```
eksctl create cluster --name <cluster_name> --region <region> --node-type t2.medium --nodes-min 2 --nodes-max 5
aws eks update-kubeconfig --region <region> --name <cluster_name>
kubectl get nodes
```

4. Configure IAM OIDC provider

   ```
   eksctl utils associate-iam-oidc-provider --cluster <cluster_name> --approve
   ```

5. Download IAM policy

   ```
   curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
   ```

6. Create IAM Policy

   ```
   aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
   ```

7. Create IAM Role and service account. Also, attach IAM Role to service account.

   ```
    eksctl create iamserviceaccount \
    --cluster=<cluster-name> \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::<aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve
   ```

8. Add helm repo to deploy ALB controller

   ```
    helm repo add eks https://aws.github.io/eks-charts
   ```
9. Update the repo

   ```
    helm repo update eks
   ```
10. Install the repo

   ```
    helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
    -n kube-system \
    --set clusterName=<cluster-name> \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set region=<region> \
    --set vpcId=<vpc-id>
   ```



## Cleanup

To delete the EKS cluster:

```
   eksctl delete cluster --name <cluster-name> --region <region>
```
