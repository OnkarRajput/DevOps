# Kubernetes:
According to the Kubernetes website: “Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications”.

Kubernetes comes from the Greek word called κυβερνήτης, it means helmsman or ship pilot, so that we can imagine that kubernetes acts as a manager for shipping containers.

Kubernetes is also referred to as k8s as there are 8 characters between k and s, similar to i18n referred to as Internationalization.

You have one or more master servers.

# Install Kubernetes on Ubuntu:
We can install kubernetes in different modes based on our requirement. Following are the typical installation modes:

* All-in-One Single-Node Installation

* Single-Node etcd, Single-Master, and Multi-Worker Installation

* Single-Node etcd, Multi-Master, and 
 Multi-Worker Installation

* Multi-Node etcd, Multi-Master, and Multi-Worker Installation

# Kubernetes Components

* Master Components
* Node Components


# Master

# Master Components
1. kube-apiserver 
- API server is the front end for the Kubernetes control plane. you have to work everything with 'apiserver' and store data in 'etcd'.

2. etcd
- store for all cluster meta data. If you will loose 'etcd' data you will loose all cluster data. you can run 'etcd' cluster server seprate. it's key value store.

3. kube-scheduler
- When you deploy new application in kubernetes cluster. Scheduler will decide where to start this application on which nodeto start your application. Scheduler has a policy and you can extend this policy as well.  

4. kube-controller-manager
- controller-manager will probably check the staus of your application. We called it kubernetes cluster as work load. So everthing in application is workload.   

5. CNI network : Weave Net, Calico, etc.
- CNI : container network interface. This is comman network interface for container run time like docker container etc... use can use diffrent run container run time bcz witch are supported bu kubernetes. Using CNI network you can access you application over the cluster.

- Weave Net : uses VXlan
-  Calico : using VGP Routing 

So when you have to setup kubernetes cluster server using along with  API server, Controller manager and etcd along with CNI network for your application.

# Node
In kubernetes we call a worker nodes. worker node hosted the Components.

# Node Components
1. kubelet
- kubelet is main components for kubernets node. kubelet will talk to the API-Server so when you orchestration your applications and when you want to require the status  and communicate to the worker nodes to get the status of the application, network node. you talk with kubelet and kubelet talk to the container run time and eta reports of run time status of the node, cpu, memory and network uages etc..       
2. kube-proxy
-  kube-proxy will communicate with API Server.It will do the filtering and trafic redirection using IPTables bydault. 

So when you as a devops engg. deploy a application or issues comman to the kubernetes cluster. You talk with API server with "Kubectl" cli tool ,or talking the reset API with  Ajax code on your own application etc." you talk with api sevrer and "API server" will get information form "etcd" or getting information form node with the help of "Kubelet". But everyting is connected with API Server. 
So example when you deployed a application. you issued a comman to the API server to deploy your application it was stored your request in etcd. then it will talk to Scheduler it will choose one of the nodes, API server will connect to the kubelet. The Kubelet will talk to the container run time like running docker etc..
So it will report back to API server wether application deployed succed or not, how's it start or not and so on and status stored in etcd as well as.  Also Kube proxy  will get the notify about the new application. and through the kube proxy with know what is the local ip address of application what's ports are allowed to used. kube proxy will get require configurtion through the API server as well whats ports are opened and soo on and it will setup the iptables rules and it will report back where is the status of rules and soo on.
Later the Controller manager will probaly talk to the kubelet to get the status of your applications. 

Once user want to connect your applications, it deployed in one of the worker node. it will root redirected or allowed by the roles what the kube proxy applied. you can also use user space proxy  iptables rules example u can setup reverse porxy on application in this case kube proxy hand your request and forward to  the application. you have one or more master nodes. you have serval worker nodes and you can communicate the api server and this orgnize every things and kubelet in main node.


# Use full links:

https://github.com/ramitsurana/awesome-kubernetes#database

# Master Node
* kube-API server: It is an API server, runs on the master node that exposes Kubernetes API

* etcd: A key store that stores Kubenetes cluster data

* kube-scheduler: It is a scheduler that runs on the master node which monitors newly created pods and runs those pods on select nodes depends on resource availability.

* kube-controller-manager: It is a control manager that runs on the manager who is responsible for monitoring nodes and maintaining pods replication.

* cloud-controller-manager: A cloud control manager that interact with underlying cloud providers (Ex: DigitalOcean, Rancher, etc..).

# Worker Nodes
* kubelet: It runs on all worker nodes and handles tasks like starting pods and containers.

* kube-proxy: It is a Kubernetes network proxy that forwards TCP/UDP incoming requests to backend container.

* Container Runtime: It is container software (Docker)

