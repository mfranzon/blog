---
title: "Make the right choice: one or more Containers in a Pod ?"
date: 2022-05-19T23:40:55+02:00
authors: ["Marco Franzon"]
categories: ["Resources"]
---
In the design phase you have to make a lot of right choices to avoid bad surprises during the deploy.
One of the most frequent question is: "How can I organise my application in Kubernetes to achieve better performances ?"
Sometimes the correct answer is just make the right decision in splitting and grouping the services in the correct way.

### Pods, the resource you always need to

Pods are the most used resource in a Kubernetes environment. They contains the running processes, containers to be more precise, one or more, and they could be the actuation of another resource.
If you ever asked yourself why we need pods and why we do not use just containers, the answer is the container definition itself.
A container is designed to run only a single process; if you decide to run multiple processes on the same container, you have to be sure that the processes in it are running and correct manage their logs because By default, the processes log to the same stdout.

For this reason, you need another level of abstraction to wrap, different processes/container that are functional related. A pod keeps the advantage of container isolation giving, the illusion to have all the processes running together.

Pods in Kubernetes can communicate with any other pod. When two pods send network packages between them, they see the IP address because no NAT gateways exists.
It does not matter if two pods are in different namespaces or nodes, in both cases they can communicate each other.

<p align="center">
  <figure>
  <img src="/pods.png" width="80%" height="30%">
  <figcaption>Figure 1. Network between pods. A pod can see other pods and it can be reachable, thanks to a routable IP address for each pod. <i>Kubernetes in Action (2022)©</i></figcaption>
  </figure>
</p>

### One or multiple containers in a pod ?

First of all asking yourself the following questions:
- Do my processes represent a single whole or they are independent ?
- Do my processes need to run together (some node, that means same resources) or they could run on different hosts ?
- Do my processes need to scale together or independently ?

Let's try to figure out the problem with a real case scenario. One of the most frequent cases is the web-app application case. The simplest infrastructure could be made from a Front-end (FE), Back-end (BE) and a Database.

We can organise this three pieces in different combinations. All in the same pod, three different pods, or two of them in a pod and the other in another pod.
To find the best choice we have to consider few aspects. A good way to thinking on it, in the design phase, is make all the possible combinations and see pros and cons.

1. **All in the same pod.**\
If all containers are in the same pod, they will always share the same machine. A pod is the unit of a Kubernetes resource, you cannot split the containers into. Another aspect to consider is that, if one of the containers has a failure the pods will send an error and this will hide also the other two services, also they are running correctly.
The time of restart will also increase, typically BE and/or FE are update frequently, mostly during development phase. Instead, database is not subject to frequent updates. So, each time you want to see your changes, you should recreate a new pod, this brings a lot of overhead if also other services have to restart.

2. **FE-BE in the same pod, Database alone.**\
This just exclude the overhead caused by the database in the init phase and also the pod with the database container could be deploy in a different node, with dedicated resources.
Now, the main problem here is that the FE/BE still share the same machine, and, mostly, if they scale, they will scale together.
This is a possible choice if the BE/FE are not supposed to be asymmetric, so none of them could have a workload which requires a lot of replicas of it.
Suppose to have a BE which take time to process data, it is the perfect case in which you want to scale it, but anytime you also scale the FE, which is an unnecessary overhead.


3. **Three different pods.**\
Good choice for the case in which FE/BE could have a different workload. Resources can scale independently and can use all the available resources.

Keep in mind that not always a container per pod is the best choice, you should make a pros/cons list to have the best organisation for your architecture and do not going crazy to debug it after the deployment!
