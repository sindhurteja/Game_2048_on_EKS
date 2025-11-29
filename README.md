# Game_2048: Application deployed on AWS EKS using AWS Fargate  

This is a demo/POC for deploying a 2048 game application on AWS EKS with AWS Fargate and exposing it to the public using Ingress Controller, i.e. ALB-Controller.  

For step-by-step implementation of the game 2048 on AWS EKS with Fargate, follow the [Implementation Guide](https://github.com/sindhurteja/Game_2048_on_EKS/blob/main/step-by-step%20implementation.md) OR check the file **"step-by-step implementation.md"**  

### **Quick overview on key highlights, benefits of this demo:**  

**_What is AWS EKS?_**

AWS Elastic Kubernetes Service (AWS EKS) is a managed Kubernetes service offered by AWS (Amazon Web Services)

AWS EKS manages the Kubernetes control plane, ensuring the availability, scalability, and security of the Kubernetes control plane.

The Kubernetes Control Plane components include the API Server, etcd, Scheduler, Controller Manager, etc.

To know more about AWS EKS, [check AWS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)

_**What is AWS Fargate**_: 

AWS Fargate is a serverless technology that provides on-demand and right-sized compute capacity that allows you to run containers without managing the underlying virtual machines.

To know more about AWS EKS with Fargate, [check AWS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/fargate.html)

**The Setup:**
Deployed the 2048 game on AWS EKS with Fargate, using Helm to install the AWS Load Balancer Controller, and integrated OIDC Connect provider with EKS for secure service account authentication.<br\><br\>


<img width="650" height="350" alt="image" src="https://github.com/user-attachments/assets/07ecb714-f77d-40b7-8e0b-f23b1ce7dd03" /> 


**Result:**
A production-grade, auto-scaling, highly available, secure application that demonstrates enterprise-level architecture patterns. 🚀 

**Key Highlights:**

 ✅ Serverless Containers: EKS Fargate eliminated the overhead of managing worker nodes - no patching, no scaling headaches, just pure application focus \
 ✅ Intelligent Load Balancing: Ingress - AWS LB Controller automatically provisions and configures Application Load Balancers, enabling path-based routing \
 ✅ OIDC Integration: Configured IAM OIDC connect provider to enable secure, native AWS IAM authentication for Kubernetes service accounts \
 ✅ Helm-Powered Deployment: Leveraged Helm charts to deploy the AWS Load Balancer \
 ✅ Networking: VPC with private/public subnet isolation 

**Real Business Benefits:**

💰 Cost Efficiency: Pay only for actual usage—no idle servers burning budget \
📈 Handles Growth Automatically: Scale seamlessly without manual intervention or additional overhead \
🛡️ Security Built-In: Compliance and security policies enforced automatically \
🔄 Always Available: Multi-AZ deployment ensures your business stays online, even during failures (can also be implemented in Multi-Region, by deploying separate EKS clusters in different AWS Regions) \
🚀 Team Productivity: Infrastructure management is automated — teams can focus on building applications, not maintaining servers \
📊 Visibility & Control: Real-time monitoring catches issues before customers are affected

**Total Cost for the demo setup: $0.73 (~2.5 Hrs)**

Deploying this application reinforced critical lessons about EKS, Fargate, and building resilient, scalable cloud-native systems
