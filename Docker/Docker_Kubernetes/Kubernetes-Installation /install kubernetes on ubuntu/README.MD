# configure kubernetes in ubuntu 1 master and 2 node's

# Master Node Components
* kube-API server: It is an API server, runs on the master node that exposes Kubernetes API

* etcd: A key store that stores Kubenetes cluster data

* kube-scheduler: It is a scheduler that runs on the master node which monitors newly created pods and runs those pods on select nodes depends on resource availability.

* kube-controller-manager: It is a control manager that runs on the manager who is responsible for monitoring nodes and maintaining pods replication.

* cloud-controller-manager: A cloud control manager that interact with underlying cloud providers (Ex: DigitalOcean, Rancher, etc..)



# Install Kubernetes Cluster using kubeadm
Follow this documentation to set up a Kubernetes cluster on __Ubuntu 18.04__

This documentation guides you in setting up a cluster with one master node and one worker node.

# Worker Nodes Components
* kubelet: It runs on all worker nodes and handles tasks like starting pods and containers.

* kube-proxy: It is a Kubernetes network proxy that forwards TCP/UDP incoming requests to backend container.

* Container Runtime: It is container software (Docker)

## Environment
|Role|FQDN|IP|OS|RAM|CPU|
|----|----|----|----|----|----|
|Master|kmaster.example.com|172.42.42.100|ubuntu 18.04|2G|2|
|Worker|kworker1.example.com|172.42.42.101|ubuntu 18.04|1G|1|
|Worker|kworker2.example.com|172.42.42.102|ubuntu 18.04|1G|1|

## On both Kmaster and Kworker's
Perform all the commands as root user unless otherwise specified
### Pre-requisites
##### Update /etc/hosts
So that we can talk to each of the nodes in the cluster
```
cat >>/etc/hosts<<EOF
172.42.42.100 kmaster.example.com kmaster
172.42.42.101 kworker1.example.com kworker1
172.42.42.102 kworker2.example.com kworker2
EOF
```

#### Set Hostname and update hosts file
sudo hostnamectl set-hostname  "kmaster.example.com"

#### Login to Slave / Worker Nodes and configure their hostname respectively using the hostnamectl command,

```
sudo hostnamectl set-hostname kworker1.example.com
sudo hostnamectl set-hostname kworker1.example.com
```
####  Create a new user and add it to the Sudoers group

##### Then, create a new user:
```
$ adduser michael
$ usermod -aG sudo michael
$ su - michael
```

##### Install, enable and start docker service
Install and Start Docker Service on Master and Slave Nodes

> If you use docker from ubuntu OS repository, the docker version might be old to work with Kubernetes v1.13.0 and above
```
sudo apt-get install docker.io -y

systemctl enable docker
systemctl start docker

sudo systemctl status docker

docker --version
```
##### Disabling swap and installing kubeadm
```
sudo swapoff -a
```
#####  Firewall
UFW:

* On Master Node
```
ufw allow 6443/tcp
ufw allow 2379tcp
ufw allow 2380/tcp
ufw allow 10250/tcp
ufw allow 10251/tcp
ufw allow 10252/tcp
ufw allow 10255/tcp
ufw reload
```

* On Worker Nodes
```
ufw allow 10251/tcp
ufw allow 10255/tcp
ufw reload
```
##### Disable swap
```
sed -i '/swap/d' /etc/fstab
swapoff -a
```

kubeadm - bootstraps a Kubernetes cluster

kubelet - configures containers to run on a host

kubectl - deploys and manages apps on Kubernetes
### Kubernetes Setup
##### Configure Kubernetes Package Repository on Master & Slave Nodes
```
sudo apt-get install apt-transport-https curl -y

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add

sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```

> NOTE:
Kubernetes package repository for Ubuntu 18.04 LTS is not available. Nevertheless, We have used the Xenial Kubernetes package repository.

Kubernetes package repository for Ubuntu 18.04 LTS is not available. Nevertheless, We have used the Xenial Kubernetes package repository.

##### Install Kubernetes
```
apt-get install -y kubeadm kubelet kubectl

kubeadm version
```
##### Enable and Start kubelet service
```
systemctl enable kubelet
systemctl start kubelet
```
## On kmaster
##### Initialize and Start Kubernetes Cluster on Master Node using Kubeadm
```
kubeadm init 

(or)


kubeadm init --pod-network-cidr=10.244.0.0/16

(or)

kubeadm init --apiserver-advertise-address=<MASTER_IP> --pod-network-cidr=172.42.42.0/16

:: cidr=10.244.0.0/16 is specified since we will be using the flannel network

kubeadm init --apiserver-advertise-address=172.42.42.100 --pod-network-cidr=172.42.42.0/16
```

##### Copy kube config
To be able to use kubectl command to connect and interact with the cluster, the user needs kube config file.

In my case, the user account is venkatn
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

```
#### Deploy overlay network for communicate pods. You can use any one below mentions


1. ##### Deploy Calico network
This has to be done as the user in the above step (in my case it is __venkatn__)
```
kubectl create -f https://docs.projectcalico.org/v3.9/manifests/calico.yaml
```

2. #### Deploy weave network

```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')
'
"


kubectl get pod -n kube-system | grep weav

kubectl get pod

kubectl get nodes

ip ad | grep weave
```

3. #### Deploying Flannel as the pod network

```
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

sudo kubectl get nodes

sudo kubectl get pods --all-namespaces
```

##### Verify the status of master node using the following command,

```
kubectl get nodes
```

##### Cluster join command
```
kubeadm token create --print-join-command
```
## On Kworker
##### Join the cluster
Use the output from __kubeadm token create__ command in previous step from the master server and run here.

Adding Slave node to the cluster
```
kubeadm join 172.42.42.100:6443 --token w8kbni.wiyevyov0yxwwtdj --discovery-token-ca-cert-hash sha256:29adc042c538f59f0c7339ebad2126d5836de06ffe0ae22c54ce0aef2eb4cb76
```

## Verifying the cluster
##### Get Nodes status
```
kubectl get nodes
```
##### Get component status
```
kubectl get cs
kubectl cluster-info
```

Have Fun!!


# Deploy Pod, Replication Controller and Service in Kubernetes 

### create Pod `vim pod.yml`
```
apiVersion: v1
kind: Pod
metadata:
  name: web-server-pod
  labels:
   app: webserver-app1
   region: IN
   rack: r1
   version: "1.1"
spec:
  containers:
    - name: web-server-test-container
      image: linuxtechi/web-server-php:testversion
      ports:
      - containerPort: 80
```
In the file we have specify apiversion as v1 , you can cross verify the apiversion from the file “/root/.kube/config”, Specify Kind as ‘Pod’ as we are deploying pod using this yml file. Specify the meta-data and label for pod.

Apart from this we have mentioned the specification for the container like “Image Name” and port exposed from the container etc.

Use below ‘kubectl‘ command to deploy pod.

```
kubectl create -f pod.yml

kubectl get pods
```
#### Creating Service from Command Line using “kubectl expose”
```
kubectl expose pods web-server-pod  --name=webpod-service --target-port=80  --type=NodePort
```
Above command will expose pod or container to outside world. It will generate the random port on the node on which pods are created and we can say above command will do the patting task for us, External users can reach to my web server which is running inside the pod with node ip along with random port.

Verify the Service status using the following commands

```
kubectl get svc

kubectl get svc -o wide

kubectl delete svc webpod-service
```

Creating service using yml file

```
vi service.yml


apiVersion: v1
kind: Service
metadata:
 name: webpod-service
 labels:
   app: webpod-service-label
spec:
 type: NodePort
 ports:
 - port: 80
   nodePort: 30001
   protocol: TCP
 selector:
   version: "1.1"


kubectl create -f service.yml   
```
Let’s verify the Service status by using the beneath command
```   
kubectl get svc -o wide
kubectl get svc
```
curl http://172.42.42.100:30001

We can delete the above created service using the following command
```
kubectl delete svc webpod-service
```

### Creating service using yml file
As of now we have deployed one pod and its service. Let’s assume i want to deploy 5 pods of same kind, then it can be deployed using replication controller. In short it called as ‘rc‘. Whenever we provision pods using rc then pods will be in high availability and fault tolerance, it means if anything goes wrong with pod then pod of same kind will be deployed automatically by the cluster.

```
vi replication-controller.yml

apiVersion: v1
kind: ReplicationController
metadata:
 name: webserver-rc
spec:
 replicas: 5
 selector:
   app:  lamp-app1
 template:
    metadata:
     labels:
       app: lamp-app1
       region: IN
       rack: r1
       version: "1.1"
    spec:
       containers:
       - name: webserver-php-con1
         image: linuxtechi/web-server-php:testversion
         ports:
         - containerPort: 80
```

Run the following command to deploy replication controller
```
kubectl create -f replication-controller.yml
```
Verify the pod status and see where they have been provisioned
```
kubectl get pods
kubectl get pods -o wide
```
Verify the replication controller status using beneath kubectl command
```
kubectl get rc
kubectl get rc -o wide
```
Let’s define the service for above created replication controller.

```
vi rc-service.yml



apiVersion: v1
kind: Service
metadata:
 name: webserver-service
 labels:
   app: webserver-service-label
spec:
 type: NodePort
 ports:
 - port: 80
   nodePort: 30002
   protocol: TCP
 selector:
   version: "1.1"
```
Create the service using kubectl command
```
kubectl create -f rc-service.yml
```
Get the status of Service using below kubectl command
```
kubectl get svc -o wide
```
Now try accessing web server from both the nodes
```
curl http://172.42.42.101:30002
curl http://172.42.42.102:30002
```

As per the above output, we can access our web server using the ip address of both worker nodes. Whenever we access web server using worker nodes ip then the request is load balanced automatically among the pods on nodes.


# Kubernetes Troubleshooting 
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/





https://www.nakivo.com/blog/install-kubernetes-ubuntu/





























  
