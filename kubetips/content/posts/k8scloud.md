---
title: "Kubernetes in Cloud: easy way to deploy an EKS cluster (AWS)"
date: 2022-05-04T20:24:02+02:00
authors: ["Marco Franzon"]
categories: ["How to"]
---

One of the most used service, to have your Kubernetes cluster deploy in cloud, is the Elastic Kubernetes Service provided by AWS.

EKS, defined as containers-as-a-service (CaaS), (more details [here](https://aws.amazon.com/it/eks/) regarding pricing and general overview) is a managed service that helps to run Kubernetes on AWS. Practically speaking, you do not need to manage the installation of Kubernetes on EC2 instances but you can leave all the configuration stuff to the services provided by EKS.

### Configuration AWS CLI

First of all you have to install and configure the AWS client, [here](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) the details for the installation. After that is necessary to add in the <code>.aws</code> folder (which should be created during the aws cli installation) two files:
- config

  ```
  aws_access_key_id=[get-from-account-settings]
  aws_secret_access_key=[get-from-account-settings]
  region=[aws-region]
  output=[format, default json]
  ```

- credentials

  ```
  aws_access_key_id=[get-from-account-settings]
  aws_secret_access_key=[get-from-account-settings]
  ```

### Install kubectl

Second step is to install the instrument you will use to administrate the cluster __kubectl__ .
[Here](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html) the instruction to correctly download the right version of kubectl.
Keep in mind that, after the installation, a <code>.kube</code> will appear on your home. This will contains the config file to connect with the correspondent cluster.

### Add required IAM roles

IAM roles in AWS environment identify the permissions policies that can be associated to an user. To add the correct permissions to your user, follow this [guide](https://docs.amazonaws.cn/en_us/eks/latest/userguide/service_IAM_role.html#create-service-role). At the end your user should have all the permissions to create an EKS cluster from scratch.

### Install eksctl

Finally, you can install eksctl tool. As you have already done for kubectl, choose [here](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html) the correct version, according with your OS.

### Create your first cluster

Now it is time to deploy your first cluster, using eksctl, it is trivial.
The command is:

`eksctl create cluster -f config-cluster.yaml`

```
#config-cluster.yaml

apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: cluster-name
  region: aws-region

nodeGroups:
  - name: ec2-group-name
    desiredCapacity: # of instances
    instanceType: instance-type
```

The deployment requires ~15 mins (could be different based on the number and the types of instances you choose).
At the end, eksctl write in your .kube/config the specs to connect to the cluster when you use the kubectl tool.
When the creation is done, you can interact with your cluster, for example get the system pods with:

`kubectl get pods -A`

That's all! Have fun with your EKS cluster.
