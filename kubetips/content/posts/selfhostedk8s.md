---
title: "Kubernetes: solutions to deploy it locally"
date: 2022-04-17T11:11:21+02:00
authors: ["Marco Franzon"]
tags: ["on-prem", "sandbox"]
categories: ["How to"]
---

In this article I want to summarize the most used softwares to deploy a
local Kubernetes cluster on your local machine.
Having a local deployment of your infrastructure is always a good idea for testing and
debugging.
There are several platforms you can choose to quickly have your self-hosted
Kubernetes.


### Minikube

<p align="center">
  <img src="https://miro.medium.com/max/800/0*KzqL3xqmXzV5PPjX.png" width="20%" height="30%">
</p>

[Minikube](https://minikube.sigs.k8s.io/docs/start/) is probably the most used choice. It can runs in all the major OS and
offers an easy installation guide.
It is perfect for people that are using multiple OS machines and have some basic familiarity with Kubernetes and Docker.
It is a good choice if the infrastructure you want to deploy is not too much complex. Otherwise, you will probably spend too much time looking for the correct minikube configuration, instead of working on your deployment.

Main features that caracterized Minikube are :

- Supports for all the Kubernetes (good for very retro-compatibility)
- Cross-platform (Windows, MacOS, Linux)
- Different containerization systems (Docker, containerd, CRI-O)
- Add-ons to easily install Kubernetes features and properly configure the deployment
- Minikube itself could be install in a container as well as on bare metal.


### Kind

<p align="center">
  <img src="https://d33wubrfki0l68.cloudfront.net/d0c94836ab5b896f29728f3c4798054539303799/9f948/logo/logo.png" width="20%" height="30%">
</p>

[Kind](https://kind.sigs.k8s.io/docs/user/quick-start/) (Kubernetes in Docker) is a CNCF (Cloud Native Computing Foundation) certified conformant Kubernetes installer. It is more devoted to test Kubernetes itself or to have a deeper look on how Kubernetes works under the hood.
Kind gives you the possibility to easily install a local cluster using Docker containers as nodes. This makes easy to install, remove, upgrade it unlike Minikube.

Main aspects are :

- Multi platform (Linux, MacOS, Windows)
- Multi node cluster (in Minikube you have just one node)
- No need to configure anything outside of Docker


### Microk8s

<p align="center">
  <img src="https://dashboard.snapcraft.io/site_media/appmedia/2018/11/b8a85a31-MicroK8s_SnapStore_icon.png" width="20%" height="30%">
</p>

[Microk8s](https://ubuntu.com/tutorials/install-a-local-kubernetes-with-microk8s?gclid=Cj0KCQjw37iTBhCWARIsACBt1IyB4QUogBpRlNU76BTm80LLFmBl1aK6Qn3NwQdwBOy6Lfvy4mZpYRwaAuSsEALw_wcB#1-overview) is also a CNCF certifided installer for Kubernetes. It is a fully contained platform, all the pieces needed are wrapped in a single package. For this reason it is isolated from other tools of your machine. As for kind, also Microk8s is very easy to install and remove.
It is relatively new, so it could be unstable, but seems promising.

Main points of Microk8s :

- Multi platform BUT on Linux via snap, on MacOs via brew and using an installer for Windows
- Multi node cluster, each Linux instances connected to the master could be a node of the cluster.
- Add-ons, as for Minikube there are a set of customizable features.
