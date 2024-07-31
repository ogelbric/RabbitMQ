#Take 1
#Install a cluster

kubectl is aliased to k (alias k='kubectl --kubeconfig ~/.kube/config')


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
Auth
========
 k apply -f ./authorize-psp-for-gc-service-accounts.yaml

Operator
=======
```
#
# Option copy file local and change to local registry from Docker
#
k apply -f ./rabbitclusteroperator.yml
#
# Option use official operator but it will pull from docker and encounter rate limit
#
#kubectl apply -f "https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml"
#
# option use cluster
k apply -f "https://raw.githubusercontent.com/ogelbric/RabbitMQ/main/Vanilla_vSphere_Tanzu/rabbitmqcluster.yaml"

kubectl get namespace rabbitmq-system
kubectl get pods -n rabbitmq-system

Rabbit Cluster
=============
k create ns rabbitmq-dev01

k apply -f ./rabbitmq-3-node.yml -n rabbitmq-dev01

#kubectl apply -f https://raw.githubusercontent.com/Tanzu-Solutions-Engineering/tanzu-rabbitmq-event-streaming-showcase/main/deployment/cloud/k8/data-services/rabbitmq/rabbitmq-3-node.yml
kubectl get pods -w
kubectl get services

kubectl get secret rabbitmq-default-user -o jsonpath="{.data.username}"  -n rabbitmq-dev01
#
export ruser=`kubectl -n rabbitmq-dev01 get secret rabbitmq-default-user -o jsonpath="{.data.username}"| base64 --decode`
export rpwd=`kubectl -n rabbitmq-dev01 get secret rabbitmq-default-user -o jsonpath="{.data.password}"| base64 --decode`
#
echo ""
echo "USER:" $ruser
echo "PASSWORD:" $rpwd


http://192.168.5.100:15672/

