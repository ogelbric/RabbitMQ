#Take 1
#Install a cluster

kubectl is aliased to k


## Enter the vSphere namespace (namespace1000)
```
k config use-context namespace1000
```

Auth
========
 k apply -f ./authorize-psp-for-gc-service-accounts.yaml

Operator
=======
k apply -f ./rabbitclusteroperator.yml
#kubectl apply -f "https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml"

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

