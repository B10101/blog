---
title: KUBERNETES - INTRO AND INSTALLATION
date: 2024-01-24
tags: [Kubernetes]
social_image: '/media/cont.jpg'
description: In the ever-evolving landscape of container orchestration, Kubernetes stands out as a game-changer. Its rise to prominence has been nothing short of meteoric, transforming the way we deploy, manage, and scale containerized applications. Whether you're a seasoned DevOps engineer, a software developer, or just venturing into the world of containerization, understanding Kubernetes is becoming increasingly crucial. This blog is your gateway to demystifying Kubernetes, unraveling its intricacies, and discovering how it revolutionizes the deployment and management of applications at scale. 
---

Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF). Kubernetes allows you to deploy and manage containerized applications in a scalable and efficient way.


![Rocket launch](/media/cont.jpg)

## Features of kubernetes
- Container Orchestration:
    1. Deployment Management: It automates the deployment of containerized applications.
    2. Scaling: It allows horizontal scaling by adding or removing instances of applications based on demand.
    3. Rolling Updates: It enables the deployment of new versions of applications without downtime.
- Automated Load Balancing: It distributes incoming network traffic across multiple pods to ensure optimal resource utilization and high availability.
- Batch Execution: It supports batch and cron job execution for running jobs or tasks on the cluster.

## Installing Kubernetes

In this blog, I am doing the installation on an Ubuntu linux machine. </br>
We have to install a tool called kubectl.
kubectl is the command-line tool for interacting with Kubernetes clusters. It allows users to perform various operations on Kubernetes clusters, such as deploying and managing applications, inspecting and managing cluster resources, and troubleshooting issues. </br>

To install kubectl, </br>

Firstly, update your apt repository then install docker.

> bash
```bash
sudo apt update
sudo apt install docker.io

```
Add your user to the 'docker' group

```bash
sudo usermod -aG docker $USER && newgrp docker

```

 Since the kubectl package is not available in the default Ubuntu repositories for the Jammy release. You might need to use the official Kubernetes repository or download the binary directly.
 Here's how you can install kubectl using the official Kubernetes repository:

 1. Add the Kubernetes APT Repository:
 > bash
```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/kubernetes-archive-keyring.gpg add -
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list > /dev/null
```
2. Install kubectl:
> bash
```bash
sudo apt install -y kubectl

```
This should install kubectl on your Ubuntu system. After installation, you can verify the version by running:
> bash
```bash
kubectl version --client
```

3. Install kubeadm, kubelet, and kubernetes-cni: </br>
These are additional tools required for setting up a Kubernetes cluster.

```bash
sudo apt-get update && sudo apt-get install -y kubelet kubernetes-cni minikube
```

4. Initialize the Kubernetes Master: </br>
On your master node, initialize the cluster using minikube. This command will set up the necessary components.
```bash
minikube start
```


And done! In the next post we will look at setting up pods and start using kubernetes.
Cheers!
