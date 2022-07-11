---
layout: post
title:  "How to install Kubernetes"
author: erik
categories: [ Devops ]
image: https://user-images.githubusercontent.com/47476901/165136193-93dbbe6a-ff9e-488e-93f1-1e014f08abf6.jpg
beforetoc: ""
toc: false
tags: [ Devops ]
---
Kubernetes is a portable and extensible open source platform for managing workloads and services. It makes easy to automate and declaratively configure.

# Table of Contents
1. [Environment requirements](#requirements)
2. [Configure SSH logins to machines](#confighostsssh)
3. [Download k0sctl](#downloadK0sctl)
4. [Install kubernetes on machines using k0sctl](#installkuberneteshosts)<br>
5. [Kubernetes clients](#Kubernetesclient)

---

## Environment requirements  <a name="requirements"></a>
- Minimum 2 machines

My virtual machines
I installed Ubuntu Lite on both machines.
![VM](https://user-images.githubusercontent.com/47476901/165136286-5466bfdf-0ae2-4cbf-b358-dedf31eb9406.png)

I use WSL as machine for the installation.
- Minimum "4 GB RAM" preferably 8-16GB for more performance.

![wsl](https://user-images.githubusercontent.com/47476901/165136298-8ff550a2-ee8d-4383-8ab4-62792dfec457.png)

### Configure SSH logins to machines <a name="confighostsssh"></a>
Use  `ssh-copy-id root@192.168.20.134` to avoid having to put passwords in the ssh login and facilitate the installation.

### Download k0sctl <a name="downloadK0sctl"></a>
Download the software.
- You can download the software here: [github.com/K0sctl](https://github.com/k0sproject/k0sctl)

Create a configuration file, send the output init to a yaml file where we will configure it in our own way.
```bash
k0sctl init > k0sctl.yaml
```
In my case it is: **k0sctl.yaml**
```bash
apiVersion: k0sctl.k0sproject.io/v1beta1
kind: Cluster
metadata:
  name: k0s-cluster
spec:
  hosts:
  - ssh:
      address: 192.168.20.134
      user: root
      port: 22
      keyPath: /home/erik/.ssh/id_rsa
    role: controller+worker
    privateInterface: ens33
  - ssh:
      address: 192.168.20.135
      user: root
      port: 22
      keyPath: /home/erik/.ssh/id_rsa
    role: worker
    privateInterface: ens33
  k0s:
    version: 1.23.5+k0s.0
```

### Install kubernetes on machines using k0sctl <a name="installkuberneteshosts"></a>
when everything is ready run the program that configures the installation, automating the process

```bash
k0sctl apply --config k0sctl.yaml
```

![Installing](https://user-images.githubusercontent.com/47476901/165136327-31092a45-200b-4694-a202-f449beabad63.png)


### Install kubernetes client on our machine <a name="installkuberneteshosts"></a>
```bash
sudo apt install kubernetes-client
```
Send the kubernetes configuration to a file called kubeconfig.
```bash
k0sctl kubeconfig > kubeconfig
```

In order to use this configuration, we indicate with the KUBECONFIG variable where it is located.
```bash
export KUBECONFIG=/opt/kubernetes/kubeconfig
```
### Kubernetes client <a name="Kubernetesclient"></a>
**List active nodes:**
```bash
kubectl get nodes
```
Check that they are already active

![list-nodes](https://user-images.githubusercontent.com/47476901/165136351-c17db351-0422-4309-a240-de7f94005476.png)


**List information of the cluster to which we are connected:**

```bash
kubectl cluster-info
```

![cluster-info](https://user-images.githubusercontent.com/47476901/165136364-97c99f98-ae7e-480a-8a42-fe86a51335b9.png)


**List detailed information of all nodes:**
You can see the information such as the type of operating system, the use of cpu, ram, the configuration of the node...

```bash
kubectl describe node
```

**Specify description of a node:**

```bash
kubectl describe node kubernetes-1 
```

![node-info](https://user-images.githubusercontent.com/47476901/165136388-0ca6419e-1d8b-4324-9ea2-28f0e9f6ba17.png)


**Brief summary of the hardware:**
```bash
kubectl top node
```
![hardware-info](https://user-images.githubusercontent.com/47476901/165136407-41c99a3d-127c-477b-8936-871793002979.png)
