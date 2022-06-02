---
title: "NetworkPolicy : Enabling network isolation between Pods in the same Namespace"
date: 2022-05-26T14:54:19+02:00
authors: ["Marco Franzon"]
categories: ["Resources", "Network"]
thumbnail: "twitter-shared-link.png"
images: ["twitter-shared-link.png"]
---

Pods are, by default, able to connect each other. No matter if they run in the same or different namespaces, no NAT will be performed between pods. Check [this article](https://kubetips.cloud/posts/containersinpod/) to have more info on pods network.

To change this behaviour it is necessary introduce a custom NetworkPolicy. Just to recall the definition:

> NetworkPolicy is a resource which gives you the possibility to specify how a pod communicates with other pods/services in the network.

Here an example of a very generic NetworkPolicy. It declares, in the namespace you have choose, that no one can connect to any pod into that namespace.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-communication
spec:
  podSelector:
```

If the previous is too strict you can introduce more informations. Assume that in a certain namespace you have a pod for the database, a pod for the web-app and other few pods. Probably, you want that the web-app can communicate with the database, but you do not want to give the same possibility to other pods.
What you need is a NetworkPolicy that sets more specific rules.

Here an example, using 5000 as communication port between web-app and database.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-webapp
spec:
  podSelector:
    matchLabels:
      app: database
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: webapp
ports:
- port: 5000
```

This NetworkPolicy has as subject the `app : database` resource, which means the pod with the database service. It enforces a communication between the two pods on the port 5000, without using services. More in detail, it create an ingress for the database service which accepts request just from web-app service. If there is also a service associated to those pods, this new rule will be add.
