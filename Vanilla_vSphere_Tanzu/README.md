# Take 1
# Install a cluster

`kubectl is aliased to k (alias k='kubectl --kubeconfig ~/.kube/config')`


## Enter the vSphere namespace (namespace1000)
```
k config use-context namespace1000
```
## Create k8 cluster 
```
#
# Option copy file local and execute from directory
#
k apply -f ./rabbitmqcluster.yaml
#
# Option pull from my git repo
#
# k apply -f "https://raw.githubusercontent.com/ogelbric/RabbitMQ/main/Vanilla_vSphere_Tanzu/rabbitmqcluster.yaml"
#
```
## Logonto cluster / context
```
kubectl vsphere login --server 192.168.5.90 --vsphere-username administrator@vsphere.local --tanzu-kubernetes-cluster-namespace namespace1000 --tanzu-kubernetes-cluster-name rabbitmq-cl1 --insecure-skip-tls-verify
k config use-context rabbitmq-cl1
k get nodes
```
## Auth Policy
```
k apply -f ./authorize-psp-for-gc-service-accounts.yaml
#
# Option pull from my git repo
#
# k apply -f "https://raw.githubusercontent.com/ogelbric/RabbitMQ/main/Vanilla_vSphere_Tanzu/authorize-psp-for-gc-service-accounts.yaml"
```
## Operator
```
#
# Option copy file local and change to local registry from Docker
#
k apply -f ./rabbitclusteroperator.yml
#
# Option use official operator but it will pull from docker and encounter rate limit
#
# kubectl apply -f "https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml"
#
# Option use from my git repo / google repo
#
# k apply -f "https://raw.githubusercontent.com/ogelbric/RabbitMQ/main/Vanilla_vSphere_Tanzu/rabbitclusteroperator.yml"
#
kubectl get namespace rabbitmq-system
kubectl get pods -n rabbitmq-system
```

## Rabbit Namespace / Rabbit app in 3 node Rabbit Cluster
```
k create ns rabbitmq-dev01
#
# Option copy file local
#
k apply -f ./rabbitmq-3-node.yml -n rabbitmq-dev01
#
# Option use origional from Rabbit content (again be aware of docker rate limit)
#
#kubectl apply -f https://raw.githubusercontent.com/Tanzu-Solutions-Engineering/tanzu-rabbitmq-event-streaming-showcase/main/deployment/cloud/k8/data-services/rabbitmq/rabbitmq-3-node.yml -n rabbitmq-dev01
#
# Option use from my github repo / google repo
#
# k apply -f "https://raw.githubusercontent.com/ogelbric/RabbitMQ/main/Vanilla_vSphere_Tanzu/rabbitmq-3-node.yml" -n rabbitmq-dev01
#
kubectl get pods -n rabbitmq-dev01
kubectl get services -n rabbitmq-dev01
#
# find the external IP for rabbit for the admin GUI
#
```
## The rabbitMQ deployment set a user and password in a secret 
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
## Go to admin GUI via browser
```
http://192.168.5.100:15672/
```

## Outcome in RabbotMQ Console

![Version](https://github.com/ogelbric/RabbitMQ/tree/main/Vanilla_vSphere_Tanzu/rabbitmq1.png)

## Outcome in vCenter

![Version](https://github.com/ogelbric/POC_Tanzu_App_Engine/blob/main/tanzuplatform1.png)



### Inspiration
```
https://github.com/Tanzu-Solutions-Engineering/tanzu-rabbitmq-event-streaming-showcase/tree/main

https://github.com/Tanzu-Solutions-Engineering/tanzu-rabbitmq-event-streaming-showcase/tree/main/docs/workshops/Podman/Labs

```
