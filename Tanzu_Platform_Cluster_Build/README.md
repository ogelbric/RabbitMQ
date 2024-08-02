# Take 2: Build cluster with Tanzu Platform (Tanzu Apllication Engine Disabled) 

## There are two ways to build this k8 cluster (I will explain both options here) 
## 1) via the Tanzu Hub GUI
## 2) via the Tanzu CLI and yaml file

## 1) Tanzu Hub GUI

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/clg1.png)


## 2 CLI

### Tanzu Login
```
source ./tanzucli.src
tanzu login
tanzu project use AMER-East
```


### Origional Tanzu cluster create yaml
```
https://github.com/Tanzu-Solutions-Engineering/tanzu-platform-workshop/blob/main/lab-platform-engineer/templates/tkgs-cluster.yaml
```

### Labels (Before and after the addition of the new label):

```
k get ns rabbitmq-dev01  --show-labels

NAME             STATUS   AGE    LABELS
rabbitmq-dev01   Active   4m7s   kubernetes.io/metadata.name=rabbitmq-dev01

[root@orfdns ~]# kubectl label --overwrite ns rabbitmq-dev01 pod-security.kubernetes.io/enforce=privileged

k get ns rabbitmq-dev01  --show-labels

NAME             STATUS   AGE     LABELS
rabbitmq-dev01   Active   4m23s   kubernetes.io/metadata.name=rabbitmq-dev01,pod-security.kubernetes.io/enforce=privileged

k get ns rabbitmq-system  --show-labels
NAME              STATUS   AGE   LABELS
rabbitmq-system   Active   38s   app.kubernetes.io/component=rabbitmq-operator,app.kubernetes.io/name=rabbitmq-system,app.kubernetes.io/part-of=rabbitmq,kubernetes.io/metadata.name=rabbitmq-system,pod-security.kubernetes.io/enforce=privileged

```

