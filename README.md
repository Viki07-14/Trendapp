EKS CI/CD Pipeline with Monitoring using
Prometheus & Grafana
Project Overview
This project demonstrates a complete DevOps CI/CD workflow using AWS EKS, Jenkins, Docker, GitHub, 
Prometheus, and Grafana.
The objective of this project is to:
• 
• 
• 
• 
Provision a Kubernetes cluster using eksctl
Deploy an application to EKS
Automate build and deployment using Jenkins Pipeline
Monitor the Kubernetes cluster using Prometheus and Grafana
Repository URL:
https://github.com/Vennilavan12/Trend.git
Architecture
Workflow:
1. 
2. 
3. 
4. 
5. 
6. 
7. 
Developer pushes code changes to GitHub
Jenkins pipeline is triggered automatically
Jenkins builds Docker image
Docker image is pushed to Docker Hub
Jenkins deploys the application to EKS
Prometheus collects cluster and application metrics
Grafana visualizes metrics using dashboards
Tools & Technologies Used
Tool
Purpose
AWS EKS
Managed Kubernetes cluster
eksctl
Create and manage EKS cluster
Jenkins
CI/CD automation
GitHub
Source code management
Docker
Containerization
1
Tool
Purpose
Helm
Kubernetes package manager
Prometheus Metrics collection
Grafana
Metrics visualization
EKS Cluster Creation
The Kubernetes cluster was created using eksctl.
Example command:
eksctl create cluster--name trend-cluster--region ap-south-1--nodegroup-name worker-nodes--node-type t3.medium--nodes 2
After creation, cluster access was verified using:
kubectl get nodes
Jenkins Setup
• 
• 
• 
• 
• 
• 
• 
• 
Jenkins installed on an EC2 instance
Required tools installed:
Docker
kubectl
Helm
AWS CLI
Jenkins configured with:
GitHub credentials
2
• 
• 
Docker Hub credentials
Kubernetes kubeconfig
Jenkins CI/CD Pipeline
The Jenkins pipeline performs the following steps:
1. 
2. 
3. 
4. 
Checkout source code from GitHub
Build Docker image
Push Docker image to Docker Hub
Deploy updated application to EKS
Pipeline is triggered automatically when changes are pushed to the GitHub repository.
Application Deployment to EKS
Kubernetes manifests are used to deploy the application:
• 
• 
Deployment
Service (LoadBalancer)
Example:
kubectl apply-f k8s/
The application is exposed externally using an AWS LoadBalancer.
Monitoring Setup (Prometheus & Grafana)
Monitoring is implemented using kube-prometheus-stack via Helm.
Installation
helm repo add prometheus-community https://prometheus-community.github.io/helm
charts
helm repo update
helm install monitoring prometheus-community/kube-prometheus-stack--namespace monitoring--create-namespace
3
Accessing Monitoring Tools
Grafana
• 
• 
• 
• 
Exposed via LoadBalancer
Default credentials:
Username: 
admin
Password: 
prom-operator
Prometheus
• 
• 
Exposed via LoadBalancer
Used to query cluster metrics
Node Exporter
• 
• 
Exposed via LoadBalancer
Provides node-level metrics
Dashboards
Grafana dashboards include:
• 
• 
• 
• 
Kubernetes Cluster Overview
Node Metrics
Pod CPU & Memory usage
Application performance metrics
Security Considerations
• 
• 
• 
IAM roles used for EKS access
Jenkins uses least-privilege credentials
Prometheus & Grafana access can be restricted via security groups
Conclusion
This project successfully demonstrates:
• 
• 
• 
Automated CI/CD using Jenkins
Containerized application deployment to AWS EKS
Real-time monitoring using Prometheus and Grafana
The setup follows DevOps best practices and provides a scalable and observable Kubernetes deployment.
4
Future Enhancements
• 
• 
• 
• 
Add Ingress with HTTPS
Enable alerting with Alertmanager
Add Helm-based application deployment
Integrate Slack or Email alerts
