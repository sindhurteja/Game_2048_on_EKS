![Architecture Overview](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/dgdh20my4var0ejanwfp.png)

## **Step-by-Step: Deploying “Game-2048” on AWS EKS with Fargate**

<u>**Prerequisites:**</u>

Before proceeding, you will need an AWS Account, the AWS CLI, eksctl, kubectl, and Helm to install and configure. 

**AWS Account:** You need AWS Account https://aws.amazon.com/

**AWS CLI:** The AWS Command Line Interface (AWS CLI) is an open-source tool by Amazon Web Services that lets users manage AWS services from their terminal. It offers a consistent interface for services like Amazon EC2, S3, IAM, and more others.

To setup and configure AWS CLI, please refer to [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

**eksctl:** eksctl is the official command-line interface (CLI) for Amazon EKS that automates and simplifies the creation and management of Amazon Elastic Kubernetes Service clusters

To install the eksctl tool, please refer to [check AWS documentation install eksctl](https://docs.aws.amazon.com/eks/latest/eksctl/installation.html)

**Kubectl:** kubectl is the command-line interface (CLI) tool for interacting with and managing Kubernetes clusters. It acts as a client for the Kubernetes API, allowing users to send commands to the cluster's control plane

To install kubectl, please refer to [install kubectl](https://kubernetes.io/docs/tasks/tools/)
To know more about eksctl and kubectl [check AWS documentation](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html#eksctl-install-update)

**Helm:** Helm is a Kubernetes package manager that simplifies application deployment and management. It uses "charts," pre-configured packages with all necessary resources. Helm allows users to define, install, and upgrade applications with one command, ensuring consistency and reducing errors.

To install Helm, refer to [install helm](https://helm.sh/docs/intro/install/)

**<u>Deployment:</u>**

**1. Creating EKS Cluster “game-2048-demo” using eksctl**

**`Run-command:`**

```
eksctl create cluster --name game-2048-demo --region us-east-1 --fargate
```

> **_Note:_** “eksctl” takes care of the resource provisioning using cloudformation templates for AWS EKS. It creates a new VPC with both public and private subnets across multiple Availability Zones, along with a default Fargate profile.

> ⚠️ WARNING!
> Please be informed that the last step of this demo, once you have successfully implemented this demo project. You need to delete all the resources created.
>Deletion of resources is provided as the last or final step

_Review the screenshots below, eksctl creating/deploying the AWS EKS Cluster “game-2048-demo”_

![eksctl output1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/df4g89icu4ivznpxu13t.png)


![eksctl output1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3m4spqnktv8tee24fsuu.png)

_Review and confirm the cluster “game-2048-demo” created from AWS management Console as well_


![awsconsoleoutput1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/uwx3w294e24qiysf9fdp.png)


![awsconsoleoutput2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rkjtltrwzcj51g7elafl.png)

**2. Update the kubeconfig for kubectl:**

**`Run-command:`**

```
aws eks update-kubeconfig --name game-2048-demo --region us-east-1
```


![output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q0rnty20i5t7q6ly2bck.png)

**3. Create a new fargate profile “fgp-game-2048” under a new namespace “game-2048”**

**`Run-command:`**

```
eksctl create fargateprofile --cluster game-2048-demo --region us-east-1 --name fgp-game-2048 --namespace game-2048
```
Creating a new Fargate profile “fgp-game-2048” to deploy the application pods in a new namespace “game-2048.”


![output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qbrju6lxo8ez53rh682d.png)


![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ovjwntnemvmcy2ucuqxd.png)


**4. Deploy the game-2048 application:** 

**`Run-command:`**

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

The file **2048_full.yaml** (from the link) has all the required resources (deployment, service, and ingress) configured.

https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml

The link is provided by official AWS EKS documentation as an example, to get started with AWS EKS [click here](https://docs.aws.amazon.com/eks/latest/userguide/quickstart.html)

_Review the screenshots below, confirming the required resources are created and deployed_

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kdguryu861arsax77sfo.png)


_To review and confirm the application pods, deployments, service, and ingress are up and running under namespace “game-2048”_


**`Run commands below to verify resources (pods, deployments, service, ingress):`**

`kubectl get pods –n game-2048.`

`kubectl get deployments –n game-2048.`

`kubectl get service –n game-2048.`

`kubectl get ingress –n game-2048.`

> _**Note:**_ _For ingress, there is no resource created yet at this point, we will create AmazonLoadBalancerController using helm in the upcoming steps_


![kubectl-output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gnkhsxiwd4dh5iippjra.png)

**5. Configure IAM Open ID Connect provider (OIDC) for cluster “game-2048-demo”**

_What is the need for IAM OIDC Provider?_

IAM OIDC (OpenID Connect) with AWS EKS refers to the method of granting AWS IAM permissions to Kubernetes service accounts within an Amazon EKS cluster. This allows applications running in EKS pods to securely access AWS resources without requiring the direct storage of long-lived AWS credentials in the pod.

**`Run-command:`**

```
eksctl utils associate-iam-oidc-provider --cluster game-2048-demo --approve
```

_The above command creates and associates an OpenID Connect (OIDC) identity provider with your EKS cluster. This is a foundational step for enabling IAM Roles for Service Accounts (IRSA), which allows Kubernetes pods running in your cluster to securely access AWS services without hardcoded credentials._


![eksctl-output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fb5c8xan5oaljzjktsk7.png)

**6. We will create an IAM role with the necessary permissions for the ALBController to access and create an AWS Application Load Balancer for the “game-2048” application**

_Download the ALB Controller IAM-Policy_

**`Run-command:`**

```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jb16h51h9uyw0w1tuku6.png)

**7. Create the IAM Policy for AWSLoadBalancerController using the IAM policy that we downloaded:**

**`Run-command:`**

```
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
```

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ofjeqvjht5kaplqx13up.png)

**8. We will create an IAM service account that binds an IAM role to a Kubernetes service account, enabling the AWS Load Balancer Controller pod to access AWS ALB resources with least-privilege permissions.**

**`Run-command:`**

```
eksctl create iamserviceaccount \
  --cluster=game-2048-demo \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

_What It Does?_

- Creates a Kubernetes service account named aws-load-balancer-controller in the kube-system namespace
- Creates an IAM role named AmazonEKSLoadBalancerControllerRole (if it doesn't exist)
- Attaches the IAM policy specified by the ARN to the role, granting Load Balancer Controller permissions
- Annotates the service account with the IAM role ARN, establishing the trust relationship
- Executes without prompting due to the --approve


![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ym3up42uefkovbvllhrb.png)

**9. Deploy the ALB Controller using Helm Charts**

You need to add the AWS EKS Helm chart repository with local Helm installation/configuration. It's a prerequisite step before you can search or install charts from AWS.

**`Run-command:`**

`helm repo add eks https://aws.github.io/eks-charts`
`helm repo update eks`

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/s7nem2fb0309crjbgtyt.png)

**_Install Ingress ALB-Controller using helm:_**

**`Run-command:`**

```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=game-2048-demo --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller --set region=us-east-1 --set vpcId=vpc-xxxxxxxxxxxxx
```
![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ap4rdhkq50wqy7h8kn53.png)

_**Now, you can verify the ALBController (aws-load-balancer-controller) Pods are up and running under the system namespace “kube-system”**_

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0t5sftmjbxfafumkesrl.png)

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/pix60g3s23bqfzhvceep.png)

_The ALB Controller provisions and configures AWS Application Load Balancers (ALB) in response to Kubernetes resources._

_From the below screenshot, you can review that the AWS ALB is provisioned, and the same is reflected under the ingress resource with the ALB DNS name_

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/khzmmlbht1cfd24w8xhy.png)

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2wd1zoeo2ryivjgji746.png)

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/cbo1ru37i1su5janh4c8.png)

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/i9ownbiu1j8vi82qdgf2.png)

_Now, to test the “game-2048” application. You can copy the AWS Application Load Balancers DNS name (from the AWS managed console or the CLI) as shown above._

_Paste the DNS name in a browser (as shown below)_



# BOOOOOM! Game-2048 is ON!


![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/q1y38xrnh9i68jldjdmp.png)

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/iv7p33zl8ff8g16o2jdl.png)



**_What's your highest score?😉_** **_Let me know in the comments!_**




**_Give a glance at what you have built!_**


_Verify the resources created under the system namespace, i.e. kube-system_


**`Run-commands below:`**

`kubectl get pods –n kube-system`

`kubectl get deployments –n kube-system`

`kubectl get service –n kube-system`


![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/up0l5g4ty0zrm03o13qo.png)


_Verify the resources created under the namespace "game-2048"_


**`Run-commands below:`**

`kubectl get pods –n game-2048`

`kubectl get deployments –n game-2048`

`kubectl get service –n game-2048`

`kubectl get ingress –n game-2048`

![Output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0q84d5n7jh38fgudd1a9.png) <br\>


> 💡Insights!

> Note the difference that the ALBController Pods are created under the system namespace "kube-system", but the Application Load Balancer is created under the namespace "game-2048".

> The ALB Controller pods are created under the "kube-system" system namespace; this is by design and as developed by AWS. <br\>



## **Now, as the final step. You need to delete/clean up the AWS EKS Cluster and other resources created in this demo.**


- Delete the AWS EKS Cluster "game-2048-demo"


**`Run-command:`**
```
eksctl delete cluster --name game-2048-demo --region us-east-1
```


_Review the screenshot below. EKS cluster resources are being deleted or cleaned up_

![eksctl-output](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/46gvqkpw3n9cjh4niyk7.png)

**_Before you finish, review to ensure that no other resources are missed for cleanup._**


**That's all for this project demo! HAPPY LEARNING...!**<br\>


Please share your thoughts and suggestions to improve further.<br\>


Grateful to [@Abhishek Veeramalla](https://youtu.be/RRCrY12VY_s?si=p_7AIhDt_bjZwHG1) for providing the detailed project demonstration on his YouTube Channel.<br\>




### **Thank You!**

