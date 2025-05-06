# Microservices-setup-on-EKS

## ☁️ Install AWS CLI v2 on Linux

Follow the instructions below based on your system architecture:

---

### 🔹 For Intel/AMD (x86_64) Systems

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt update
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
```
### 🔹 For ARM (aarch64) Systems

```bash
sudo apt update
sudo apt install unzip -y
sudo curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip"
sudo unzip awscliv2.zip
sudo ./aws/install
```

### ✅ Verify Installation
```bash
aws --version
```

## 🧰 Install `kubectl`

The `kubectl` command-line tool lets you interact with your Kubernetes cluster.

### ✅ Steps for Intel/AMD (x86_64)
```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

### ✅ Install on ARM (aarch64)

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/arm64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
```

## 🧰 Install `eksctl`

The `eksctl` tool simplifies EKS cluster creation and management just like `Kubeadm` in Kubernetes cluster

### ✅ Install on Intel/AMD (x86_64)
```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

### ✅ Install on ARM (aarch64)
```bash
ARCH=arm64
OS=$(uname -s | tr '[:upper:]' '[:lower:]')
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_${OS}_${ARCH}.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
```

### 🚀 Setup Amazon EKS Cluster
Create an EKS cluster using `eksctl` of same nodes types with the following command:

```bash
eksctl create cluster \
  --name three-tier-cluster \
  --region us-east-1 \
  --node-type t4g.medium \
  --nodes-min 2 \
  --nodes-max 2
```
### 📝 Command Explanation

Flag | Description 
--- | --- 
`--name`	| Name of your EKS cluster
`--region` |	AWS region where the cluster will be created
`--node-type`	| EC2 instance type for worker nodes (e.g., t2.medium)
`--nodes-min / --nodes-max`	 | Minimum and maximum number of nodes (scaling settings)

🛡️ This command creates:

- A VPC with subnets and security groups

- A control plane managed by EKS

- Worker nodes using EC2 in an Auto Scaling Group

- IAM roles and Kubernetes configuration

The `eksctl` create cluster command by default uses a `single node` group—which means all EC2 instances will be of the same type. To create `multiple node` groups with different instance types, you need to use a `YAML configuration` file instead of inline flags:

####  🚀cluster-config.yaml
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: three-tier-cluster
  region: us-east-1

nodeGroups:
  - name: frontend-nodes
    instanceType: t3.small
    desiredCapacity: 2
    minSize: 2
    maxSize: 3

  - name: backend-nodes
    instanceType: m5.large
    desiredCapacity: 2
    minSize: 2
    maxSize: 4

  - name: db-nodes
    instanceType: r5.large
    desiredCapacity: 1
    minSize: 1
    maxSize: 2
```

