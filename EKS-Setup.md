# Step - 1 : For EKS Management in AWS #

Open terminal in your Windows/Mac/Ubuntu

## Installing Kubectl to interact with K8s Cluster
```
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/darwin/arm64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /opt/homebrew/bin
kubectl version --short --client
```
## Install AWS CLI latest version using below commands 
```
sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

## Install eksctl using below commands
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```
## Configure AWS CLI with IAM User Access Keys
```
aws configure
```
enter details like access key and secret access key with Region

# Step - 2 : Create IAM role & attach to EKS Management Host # - Optional if using AWS CLI with Admin Access Key

1) Create New Role using IAM service ( Select Usecase - ec2 ) 	
2) Add below permissions for the role <br/>
	- Administrator - acces <br/>
		
3) Enter Role Name (eksroleec2) 
4) Attach created role to EKS Management Host (Select EC2 => Click on Security => Modify IAM Role => attach IAM role we have created) 

# Step - 3 : Create EKS Cluster using eksctl # 
**Syntax:** 

eksctl create cluster --name cluster-name  \
--region region-name \
--node-type instance-type \
--nodes-min 2 \
--nodes-max 2 \ 
--zones <AZ-1>,<AZ-2>

## Mumbai: <br/>
`
eksctl create cluster --name demo-cluster-1 --region ap-south-1 --node-type t2.medium  --zones ap-south-1a,ap-south-1b,ap-south-1c
`

## Note: Cluster creation will take 5 to 10 mins of time (we have to wait). After cluster created we can check nodes using below command.

`
 kubectl get nodes  
`

# Note: We should be able to see EKS cluster nodes here.**

# We are done with our Setup #
	
# Step - 4 : After your practise, delete Cluster and other resources we have used in AWS Cloud to avoid billing #

```
eksctl delete cluster --name demo-cluster-1 --region ap-south-1
```
