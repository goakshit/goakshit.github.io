---
title: "Get Started With Minikube on MacOS"
date: 2021-07-28T18:05:10+05:30
slug: "minikube on macos"
tags: ["minikube", "macos", "kubernetes", "k8s-primer"]
draft: false
---
![](/img/minikube.jpeg)
# Get Started With Minikube on MacOS
## What is Minikube?
> Minikube is a tool that makes it easy to run Kubernetes locally. It is a one node cluster, where the master and worker processes are on the same machine.


## Setup Minikube On MacOS
To get started wiht minikube on macOS, we need to install binary for minikube which can be downloaded using the following command:
```
<!-- For x64-86 -->
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube

<!-- For x64-86 using homebrew  -->
brew install minikube

<!-- For arm64 -->
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-arm64
sudo install minikube-darwin-arm64 /usr/local/bin/minikube
```

After installing minikube, we can start the minikube cluster using `minikube start`. It will boot up a node in a cluster.

To verify the installation, we can run `minikube status`. 
It should return somthing like this:
```
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

Kudos. We have successfully installed minikube on macOS.
You have a cluster up and running. Now you can run `minikube dashboard` to open a dashboard.