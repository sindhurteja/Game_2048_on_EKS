# Game_2048: Application deployed on AWS EKS
This is a demo/POC to deploy a 2048 game application on AWS EKS with AWS Fargate and expose to public using Ingress and ALB


<img width="650" height="350" alt="image" src="https://github.com/user-attachments/assets/07ecb714-f77d-40b7-8e0b-f23b1ce7dd03" />

The Setup:
Deployed the 2048 game on AWS EKS with Fargate, using Helm to install the AWS Load Balancer Controller, and integrated OIDC Connect provider with EKS for secure service account authentication. 

Result:
A production-grade, auto-scaling, highly available, secure application that demonstrates enterprise-level architecture patterns. 🚀 

🔹 Key Highlights:

 ✅ Serverless Containers: EKS Fargate eliminated the overhead of managing worker nodes - no patching, no scaling headaches, just pure application focus \
 ✅ Intelligent Load Balancing: Ingress - AWS LB Controller automatically provisions and configures Application Load Balancers, enabling path-based routing \
 ✅ OIDC Integration: Configured IAM OIDC connect provider to enable secure, native AWS IAM authentication for Kubernetes service accounts \
 ✅ Helm-Powered Deployment: Leveraged Helm charts to deploy the AWS Load Balancer \
 ✅ Networking: VPC with private/public subnet isolation \

💼 Real Business Benefits:

💰 Cost Efficiency: Pay only for actual usage—no idle servers burning budget
📈 Handles Growth Automatically: Scale seamlessly without manual intervention or additional overhead
🛡️ Security Built-In: Compliance and security policies enforced automatically
🔄 Always Available: Multi-AZ deployment ensures your business stays online, even during failures (can also be implemented in Multi-Region, by deploying separate EKS clusters in different AWS Regions)
🚀 Team Productivity: Infrastructure management is automated — teams can focus on building applications, not maintaining servers
📊 Visibility & Control: Real-time monitoring catches issues before customers are affected

Total Cost for the demo setup: $0.73 (~2.5 Hrs)

Deploying this application reinforced critical lessons about EKS, Fargate, and building resilient, scalable cloud-native systems
