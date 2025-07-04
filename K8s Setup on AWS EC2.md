# Setting Up K8s on AWS EC2 Ubuntu

## Installation

### Launching EC2 Instances
- Launch 2 EC2 Ubuntu instance (1 for Master Plane and 1 for Worker Node)
- Connect both the EC2 instance in Terminal in Multi Connect Mode

### Commands to run on both Master Plane and Worker Node

This will update the package to the latest version
```bash
sudo apt update
```
This will set the hostname on the server
```bash
# On controlplane node
sudo hostnamectl set-hostname controlplane
```

```bash
# On controlplane node
sudo hostnamectl set-hostname workerNode
```
### Check MAC and UUID
The MAC and UUID should be different on each machine
```bash
ip link
```
```bash
sudo cat /sys/class/dmi/id/product_uuid
```
### Turn Swap Off

```bash
sudo swapoff -a
```

### commenting the line in /etc/fstab

```bash
sudo sed -i.bak '/ swap /s/^\(.*\)$/#\1/' /etc/fstab
```
### Enable IP Packet Forwarding
```bash
echo "net.ipv4.ip_forward = 1" | sudo tee /etc/sysctl.d/k8s.conf
sudo sysctl --system
sudo sysctl net.ipv4.ip_forward
```

### Install Containerd and Verify installation
```bash
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y containerd
ctr --version
```

### Install CNI Plugins
```bash
# creating directory to install CNI
sudo mkdir -p /opt/cni/bin

# Downloading CNI plugin
sudo wget https://github.com/containernetworking/plugins/releases/download/v1.7.1/cni-plugins-linux-amd64-v1.7.1.tgz

# Unziping the downloaded CNI Plugin
sudo tar -C /opt/cni/bin -xzvf cni-plugins-linux-amd64-v1.7.1.tgz
```
### Configure Containerd

```bash
# Creating Directory for Containerd
sudo mkdir -p /etc/containerd

# Initializing configuration of Containerd
containerd config default | sudo tee /etc/containerd/config.toml

# Checking the configuration status
sudo head /etc/containerd/config.toml
```

### Editing the Containerd TOML file
```bash
sudo nano /etc/containerd/config.toml
```
- update the below line in TOML file
```toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
  SystemdCgroup = true
```
- After making changing save the file

- Restart containerd
```bash
sudo systemctl restart containerd
```

## Add Kubernetes Repos and Install Tools

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# Creating Keyrings folder for k8s
sudo mkdir -p /etc/apt/keyrings

# Downloading the keyrings gpg file for k8s repo
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# Signing the key
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# updating packages
sudo apt-get update

# Installing kubeadm and Kublet
sudo apt-get install -y kubelet kubeadm

# Locking the version and disabling auto update
sudo apt-mark hold kubelet kubeadm
```

# Commands to be used only on Master Plane

### Install kubectl on Master Plane Only
```bash
sudo apt-get install -y kubectl
```
### Initialize Kubernetes Cluster (Only on Masterplane)
```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

### Configure kubectl for Regular User
```bash
# These commands will be displayed after cluster initialization

# Creating Directory for Kubernetes
sudo mkdir -p $HOME/.kube

# Copy config file from admin to current user
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

# Changing the ownership to current user
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Connecting Worker Node to Master Node
```bash

```

### For communicating Worker Node to Master Node we will install CNI
Installing CALICO CNI Plugin
```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.1/manifests/tigera-operator.yaml

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.1/manifests/custom-resources.yaml

watch kubectl get pods -n calico-system
```

### For printing the join command
```bash
kubeadm token create --print-join-command
```
- The join command and token are valid for 24 hours from creation
