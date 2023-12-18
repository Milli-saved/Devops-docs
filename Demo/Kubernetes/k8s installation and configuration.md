# k8s installation and configuration
In this demo we will start by using kind light weight local Kubernetes playground

## Installing kind
This demo is prepared on Ubuntu if you want to get more on other operating systems please go to kind documentation page [here]()
```bash
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind


# Check the installtion by executing the following command
kind --version
```

## Install kubectl
To install kubectl cli  please follow the following instruction
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl
sudo mv kubectl /usr/local/bin

# To check the correct installation run the following command 
kubectl version --client
```

# Configuration
## Create Kubernetes cluster using kind
To create your own small k8s cluster using kind run the following command 
```bash
# create cluster by name kaleab-cluster and wait for 3 minuts until the control plane is ready
kind create cluster --name kaleab-cluster --wait 3m
# when it's done you will see an output similar to the folliwing

```
when it's done you will see an output similar to the followings
```tmp
Creating cluster "kaleab-cluster" ...
 ✓ Ensuring node image (kindest/node:v1.27.3) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Waiting ≤ 3m0s for control-plane = Ready ⏳
 • Ready after 21s 💚
Set kubectl context to "kind-kaleab-cluster"
You can now use your cluster with:

kubectl cluster-info --context kind-kaleab-cluster

Have a nice day! 👋
```
## Interacting with the cluster using kubeclt
To interact with the k8s cluster you should use `kubectl` command line interface. Now let's start interacting with the cluster created above
Get cluster details
```bash
kubectl cluster-info --context kind-kaleab-cluster
```
You will get an output similar to the following
```json
Kubernetes control plane is running at https://127.0.0.1:35453
CoreDNS is running at https://127.0.0.1:35453/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

# Delete the cluster


```bash
kind delete cluster --name kaleab-cluster
```