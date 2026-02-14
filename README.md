Kubernates:

It is originated from greek word and its meaning is pilot.
It is also called k8s.
It is given by google


Container are need in production.
Wouldnt it easter to start another container if one stops ?

It will help in scalling and failover

Kubernates:
1) Service discovery and load balancing: It distrubutes loads to the containers
2) Storage Ochestration : If need you can store the data like local storage or any cloud provider
3)Automated rollout and rollbacks
4) Automatic bin packing
5) Self healing
6) Secret and configuration managment
7) batch execution
8) horizontal scalling
9) ip4/ip6 dual stack to pods and services

<img width="1352" height="649" alt="image" src="https://github.com/user-attachments/assets/d5634318-9a46-46af-9d38-62cd53795eee" />

api server : serves the apis 
etcd : it is a key value storage to store the api server processed data.
kube-controller : It makes sure the current state reaches to the desired state.
scheduler: It schedues the pods to run on the node
cloud-kube-controller : it connect external cloud providers

node is a machine
pods is a group of containers (they have same network and storage)

kubectl is to contorl the pod
kube-proxy is a network firewall


