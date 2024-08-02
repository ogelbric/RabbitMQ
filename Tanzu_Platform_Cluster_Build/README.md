# Take 2: Build cluster with Tanzu Platform (Tanzu Apllication Engine Disabled) 

## There are two ways to build this k8 cluster (I will explain both options here) 
* via the Tanzu Hub GUI
* via the Tanzu CLI and yaml file

## (1) Tanzu Hub GUI

### Create a cluster group (turn Tanzu application engin off) 

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/clg1.png)

### Create a cluster

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/cl1.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/cl2.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/cl3.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/cl4.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/cl5.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/cl6.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/cl7.png)

## (2) CLI

### Tanzu Login
```
source ./tanzucli.src
tanzu login
tanzu project use AMER-East
#using the cluster group I created via the GUI (tanzu operations clustergroup create...)
tanzu operations cluster create -f ./tkgsclusteryaml-rabbit.yaml

```

## Create a mutating policy in the GUI to avoid having to set "permission" for the namespace 

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/pol1.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/pol2.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/pol3.png)

Result is on the namespaces created it will have pod-security.kubernetes.io/enforce=privileged


### Labels (Before and after the addition of the new label):

```
k get ns rabbitmq-dev01  --show-labels

NAME             STATUS   AGE    LABELS
rabbitmq-dev01   Active   4m7s   kubernetes.io/metadata.name=rabbitmq-dev01

kubectl label --overwrite ns rabbitmq-dev01 pod-security.kubernetes.io/enforce=privileged

k get ns rabbitmq-dev01  --show-labels

NAME             STATUS   AGE     LABELS
rabbitmq-dev01   Active   4m23s   kubernetes.io/metadata.name=rabbitmq-dev01,pod-security.kubernetes.io/enforce=privileged

k get ns rabbitmq-system  --show-labels
NAME              STATUS   AGE   LABELS
rabbitmq-system   Active   38s   app.kubernetes.io/component=rabbitmq-operator,app.kubernetes.io/name=rabbitmq-system,app.kubernetes.io/part-of=rabbitmq,kubernetes.io/metadata.name=rabbitmq-system,pod-security.kubernetes.io/enforce=privileged
```
## Deploy RabbitMQ Operator
```
k apply -f "https://raw.githubusercontent.com/ogelbric/RabbitMQ/main/Vanilla_vSphere_Tanzu/rabbitclusteroperator.yml"
k get pods -n rabbitmq-system
```
## Deploy RabbitMQ POD's 
```
k create ns rabbitmq-dev01
k get ns rabbitmq-dev01 --show-labels
k apply -f "https://raw.githubusercontent.com/ogelbric/RabbitMQ/main/Vanilla_vSphere_Tanzu/rabbitmq-3-node.yml" -n rabbitmq-dev01

kubectl get pods -n rabbitmq-dev01

#NAME                READY   STATUS     RESTARTS   AGE
#rabbitmq-server-0   0/1     Init:0/1   0          104s
#rabbitmq-server-1   0/1     Running    0          104s
#rabbitmq-server-2   0/1     Running    0          104s

kubectl get services -n rabbitmq-dev01

#NAME             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                        AGE
#rabbitmq         LoadBalancer   10.96.35.132   192.168.5.114   5552:30257/TCP,15692:32538/TCP,5672:32452/TCP,15672:31527/TCP,1883:30958/TCP   108s
#rabbitmq-nodes   ClusterIP      None           <none>          4369/TCP,25672/TCP                                                             108s

```
## Obtain user ID and Password
```
kubectl get secret rabbitmq-default-user -o jsonpath="{.data.username}"  -n rabbitmq-dev01
#
export ruser=`kubectl -n rabbitmq-dev01 get secret rabbitmq-default-user -o jsonpath="{.data.username}"| base64 --decode`
export rpwd=`kubectl -n rabbitmq-dev01 get secret rabbitmq-default-user -o jsonpath="{.data.password}"| base64 --decode`
#
echo ""
echo "USER:" $ruser
echo "PASSWORD:" $rpwd
```
## Result

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/rb1.png)

![Version](https://github.com/ogelbric/RabbitMQ/blob/main/Tanzu_Platform_Cluster_Build/rb2.png)



## Extra Info

### Origional Tanzu cluster create yaml
```
https://github.com/Tanzu-Solutions-Engineering/tanzu-platform-workshop/blob/main/lab-platform-engineer/templates/tkgs-cluster.yaml
```

