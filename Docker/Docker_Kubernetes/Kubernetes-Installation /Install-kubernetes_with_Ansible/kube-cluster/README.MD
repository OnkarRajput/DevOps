# Server setup
  We need to create 3 virtual machines which we will use to create our cluster.

* Operating System: CentOS7/RHEL7/Ubuntu18.04
* 2048MB RAM + 2 CPU + 20 GB HD (minimum) / node
* Internet access / eth0 interface
* User with root privileges

# Server configuration
  Our 3 servers need to be configured before we will use kubespray to install the Kubernetes cluster. There are some prerequisites:

* Up to date packages
* All servers need to have the same date and time (ntpd)
* Disable firewall
* Disable swap
* Passwordless sudo
* passwordless SSH (recommended)

# Firewall
Configure Firewall for Kubernetes to work correctly.

* On Master Node

- FirwallD:

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

- FirwallD:

```
ufw allow 10251/tcp
ufw allow 10255/tcp
ufw reload

```






#  1. create inventory file for Master and workers node's.

```
vim ~/kube-cluster/hosts

```
* Run Create_a_Non-root_User.yml to create users on  Master and workers node's.

```
ansible-playbook -i hosts ~/kube-cluster/Create_a_Non-root_User

```
# 2. Installing Kubernetetes’ Dependencies

In this section, you will install the operating-system-level packages required by Kubernetes with Ubuntu’s package manager. These packages are:

* Docker - a container runtime. It is the component that runs your containers. Support for other runtimes such as rkt is under active development in Kubernetes.

* kubeadm - a CLI tool that will install and configure the various components of a cluster in a standard way.

* kubelet - a system service/program that runs on all nodes and handles node-level operations.

* kubectl - a CLI tool used for issuing commands to the cluster through its API Server.

Create a file named ~/kube-cluster/kube-dependencies.yml in the workspace

```
ansible-playbook -i hosts ~/kube-cluster/kube-dependencies.yml

```

# 3. Disable Swap and Install Kubeadm on all the nodes

```
vim Remove_swap.yml


ansible-playbook -i hosts ~/kube-cluster/Remove_swap.yml

```

# 4. Setting Up the Master Node

```
vim master.yml

ansible-playbook -i hosts ~/kube-cluster/master.yml

```

* The first task initializes the cluster by running kubeadm init. Passing the argument --pod-network-cidr=10.244.0.0/16 specifies the private subnet that the pod IPs will be assigned from. Flannel uses the above subnet by default; we’re telling kubeadm to use the same subnet.

* The second task creates a .kube directory at /home/ubuntu. This directory will hold configuration information such as the admin key files, which are required to connect to the cluster, and the cluster’s API address.

* The third task copies the /etc/kubernetes/admin.conf file that was generated from kubeadm init to your non-root user’s home directory. This will allow you to use kubectl to access the newly-created cluster.

* The last task runs kubectl apply to install Flannel. kubectl apply -f descriptor.[yml|json] is the syntax for telling kubectl to create the objects described in the descriptor.[yml|json] file. The kube-flannel.yml file contains the descriptions of objects required for setting up Flannel in the cluster.

- ssh ubuntu@master_ip

```
kubectl get nodes

```

# 5. Setting Up the Worker Nodes.

```
nano ~/kube-cluster/workers.yml

```

* The first play gets the join command that needs to be run on the worker nodes. This command will be in the following format:kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>. Once it gets the actual command with the proper token and hash values, the task sets it as a fact so that the next play will be able to access that info.

* The second play has a single task that runs the join command on all worker nodes. On completion of this task, the two worker nodes will be part of the cluster

# 6. Verifying the Cluster.

- ssh ubuntu@master_ip

```
kubectl get nodes

```

# 7. Running An Application on the Cluster.

You can now deploy any containerized application to your cluster. To keep things familiar, let’s deploy Nginx using Deployments and Services to see how this application can be deployed to the cluster. You can use the commands below for other containerized applications as well, provided you change the Docker image name and any relevant flags (such as ports and volumes).

Still within the master node, execute the following command to create a deployment named nginx:

```
kubectl create deployment nginx --image=nginx

```

- Next, run the following command to create a service named nginx that will expose the app publicly. It will do so through a NodePort, a scheme that will make the pod accessible through an arbitrary port opened on each node of the cluster:

```
kubectl expose deploy nginx --port 80 --target-port 80 --type NodePort

```

``
kubectl get services

````
- If you would like to remove the Nginx application, first delete the nginx service from the master node:
```
kubectl delete service nginx

```
- Run the following to ensure that the service has been deleted:
```
kubectl get services

```
- Run the following to confirm that this worked:

```
kubectl get deployments

```




# https://www.itzgeek.com/how-tos/linux/centos-how-tos/how-to-install-kubernetes-on-centos-7-ubuntu-18-04-16-04-debian-9.html
