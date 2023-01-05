---
title: "Introductons to Kubernetes - Part 1"
date: 2023-01-05T16:04:02+05:30
tags: ["Kubernetes", "k3s"]
featured_image: "images/kubernetes/kubernetes.png"
draft: true
---

Kubernetes is an open-source platform for automating the deployment, scaling, and management of containerized applications. It has become the de-facto standard for container orchestration, and is widely used in production environments.

If you're new to Kubernetes, you may find it overwhelming to get started. Fortunately, there are several tools and distributions that make it easier to get up and running with Kubernetes, even if you're just starting out.
One such tool is [k3s](https://k3s.io/), a lightweight Kubernetes distribution designed for easy installation and low resource usage.

In this tutorial, we'll show you how to get started with k3s on a single node. We'll cover the following topics:

1. Installing k3s
2. Deploying a sample application
3. Accessing the application

## 1. Installing k3s

The first step to getting started with k3s is to install it on your machine. The easiest way to do this is to use the installation script provided by the k3s project.

To install k3s, open a terminal window and run the following command:

```sh
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644 --data-dir /data/k3s
```

This will download and run the installation script, which will download and install the k3s and all of its dependencies.
Here I have used two additional parameters.
* `--write-kubeconfig-mode 644` will allow us to run `k3s` CLI without `sudo`
* `--data-dir /data/k3s` will put all k3s data in to a different directory rather than the default location at `/var/lib/rancher/k3s/data/`.

Once the installation is completes you can verify that k3s is running by running the following command:

```sh
sudo systemctl status k3s
```
And you can run following to see k3s if properly working.

```sh
k3s kubectl get nodes
```
This should return a list of nodes in your cluster, which should include the node you just installed k3s on.

```
NAME                       STATUS   ROLES                  AGE    VERSION
chathurika-latitude-3510   Ready    control-plane,master   107d   v1.24.4+k3s1
```

**Congratulations!**, now you have a working Kubernetes cluster to play with...

