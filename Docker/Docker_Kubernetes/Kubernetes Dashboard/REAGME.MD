# Kubernetes Dashboard

Kubernetes Dashboard is a general purpose, web-based UI for Kubernetes clusters. It allows users to manage applications running in the cluster and troubleshoot them, as well as manage the cluster itself.


* To deploy Dashboard, execute following command:

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml



(or)


kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta4/aio/deploy/recommended.yaml    


The above link may change from time to time, so if it does not work, please refer to https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/ for the latest file
```

* cluster info

```
kubectl cluster-info

Kubernetes master is running at https://172.31.15.121:6443
KubeDNS is running at https://172.31.15.121:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```


* To access Dashboard from your local workstation you must create a secure channel to your Kubernetes cluster. Run the following command:

```
kubectl proxy

kubectl proxy --address='0.0.0.0' --port=8001 --accept-hosts='.*'  &

```

* Now access Dashboard at:

```

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```


* create ssh tunnel to open kubernetes-dashboard with localhost.

```

ssh -i docker-swarm.pem -L 8001:localhost:8001 ubuntu@35.183.95.188

```

# Create An Authentication Token (RBAC)

NOTE:

- Kubeconfig Authentication method does not support external identity providers or certificate-based authentication.
- Dashboard can only be accessed over HTTPS
- Heapster has to be running in the cluster for the metrics and graphs to be available. Read more about it in Integrations guide.

* Create a Cluster Admin service account

we will find out how to create a new user using Service Account mechanism of Kubernetes, grant this user admin permissions and login to Dashboard using bearer token tied to this user


#### Next we need to create an admin user, and role binding for the user. Create two yaml files called `admin-user.yaml` and `clusterRoleBinding.yaml` on your machine


```

vim dashboard-adminuser.yaml


apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

---
```
vim clusterRoleBinding.yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard 


```
Next issue the following commands :
```
kubectl create -f admin-user.yaml

kubectl create -f clusterRoleBinding.yaml

```

* Next we need to generate the bearer token to be able to login to the dashboard. There are other ways and means to login to the dashboard, but for the sake of simplicity we will generate and use the token as follows :

```
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')


Name:         admin-user-token-ltlfk
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: e05db8e8-95b9-4b66-b0c8-1aca69cdd2ca

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  20 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Ik4wMElWYTI3MjZpeHd1WDJlSzV6cTBRZFZxNFNMWVN0QjV6S2g5SVBCa0kifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWx0bGZrIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJlMDVkYjhlOC05NWI5LTRiNjYtYjBjOC0xYWNhNjljZGQyY2EiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.HS-HVAavrA5mLr1Z4m0NmUe682pTPeq8th5X7Y2XFBeS6Nc8NHYi9N91kRK35qEDJsDclJ6pw6Iqh-JfeLir4afD9FQbktlVf-_HnQrbsDhLsF5AAXOGwRYsir3vAj13LDO19ZliYHdyOTnLYKEAit17fm83hl83Q1bzAdT4dopaRUX7kCo7o2idmEoah9eMwSapsYbsAQ8zxje6xiX5E1U7cw4rf-axczoKKwMgFOfA6f2esbvdv0t50mBcEiJcBhOgx1Vc__Pdb0h_O8wGyG_GQfPE2UqatTdcghKR6h0bZRAhCte7qthhjfbLq-absvpUm2AsswUCk03Bts5c-A

```

* Then, open your browser to http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

* Copy the secret token and paste it in Dashboard Login Page, by selecting a token option

* After Sign In you will land to Kubernetes Homepage.

Home Page
You’ll see the home/welcome page in which you can view which system applications are running by default in the kube-system namespace of your cluster, for example, the Dashboard itself.


```
kubectl -n kube-system get secret

kubectl cluster-info

kubectl get pods --all-namespaces
```
---
# Installing the Heapster Metrics
A very useful set of graphs showing the current cluster resource usage is provided by the heapster metrics. These can be installed by issuing the following command :

```
git clone https://github.com/kubernetes/heapster.git

cd heapster/deploy/kube-config/influxdb

kubectl create -f influxdb.yaml

kubectl create -f heapster.yaml

cd ../../../../

cd heapster/deploy/kube-config/rbac/

kubectl create -f heapster-rbac.yaml
```
Next restart the dashboard pod by killing it. Issue the command `kubectl get pods –all-namespaces` and locate the dashboard pod

```
kubectl  delete pod -n kubernetes-dashboard  kubernetes-dashboard-5c8f9556c4-f5hjt
```
you can see a new pod called kubernetes-dashboard-5c8f9556c4-wtzgd has been spun up to replace the deleted pod. Refresh the dashboard and login again using the token. The metrics should now be available.
```
kubectl get pods –all-namespaces
```




----------------------------------------
# Delete all Dashboard pods (assuming that Dashboard is deployed in kubernetes-dashboard namespace):
kubectl -n kubernetes-dashboard delete $(kubectl -n kubernetes-dashboard get pod -o name | grep dashboard)


# Accessing Dashboard 1.7.x and above

`kubectl proxy` creates proxy server between your machine and Kubernetes API server. By default it is only accessible locally (from the machine that started it).

* Start local proxy server.

`
kubectl proxy
Starting to serve on 127.0.0.1:8001
`

Once the proxy server is started you should be able to access Dashboard from your browser.

To access the HTTPS endpoint of dashboard go to: `http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/`

NOTE: Dashboard should not be exposed publicly using kubectl proxy command as it only allows HTTP connection. For domains other than localhost and 127.0.0.1 it will not be possible to sign in. Nothing will happen after clicking Sign in button on login page.

# kubectl port-forward

Instead of `kubectl proxy`, you can use `kubectl port-forward` and access dashboard with simpler URL than using `kubectl proxy`.

`
kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 10443:443

Forwarding from 127.0.0.1:10443 -> 8443
`

NOTE: If you need to expose dashboard to other hosts besides the host running `kubectl`, you should set the --address [ IP address that your browser's host has ] option. You can set this option to --address 0.0.0.0  , but it means publicly exposing the dashboard.


# NodePort

This way of accessing Dashboard is only recommended for development environments in a single node setup.

Edit `kubernetes-dashboard` service.

```
$ kubectl -n kubernetes-dashboard edit service kubernetes-dashboard


kubectl -n kubernetes-dashboard edit service kubernetes-dashboard

```
Change type: ClusterIP to type: NodePort and save file.

```

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
...
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
  resourceVersion: "343478"
  selfLink: /api/v1/namespaces/kubernetes-dashboard/services/kubernetes-dashboard
  uid: 8e48f478-993d-11e7-87e0-901b0e532516
spec:
  clusterIP: 10.100.124.90
  externalTrafficPolicy: Cluster
  ports:
  - port: 443
    protocol: TCP
    targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}

```

Next we need to check port on which Dashboard was exposed.

```
kubectl -n kubernetes-dashboard get service kubernetes-dashboard


NAME                   TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes-dashboard   NodePort   10.100.124.90   <nodes>       443:31707/TCP   21h

```

Dashboard has been exposed on port 31707 (HTTPS). Now you can access it from your browser at: https://<master-ip>:31707. master-ip can be found by executing kubectl cluster-info. Usually it is either 127.0.0.1 or IP of your machine, assuming that your cluster is running directly on the machine, on which these commands are executed.

In case you are trying to expose Dashboard using NodePort on a multi-node cluster, then you have to find out IP of the node on which Dashboard is running to access it. Instead of accessing https://<master-ip>:<nodePort> you should access https://<node-ip>:<nodePort>.


# API Server

In case Kubernetes API server is exposed and accessible from outside you can directly access dashboard at: https://<master-ip>:<apiserver-port>/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

Note: This way of accessing Dashboard is only possible if you choose to install your user certificates in the browser. In example certificates used by kubeconfig file to contact API Server can be used.

# Ingress

Dashboard can be also exposed using Ingress resource. For more information 

check: https://kubernetes.io/docs/concepts/services-networking/ingress.