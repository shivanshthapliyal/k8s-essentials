# K8s Essentials

![](https://cdn-media-1.freecodecamp.org/images/1*0ju9JOPACF90yXSi-s2gwQ.jpeg)

# Table of Contents
<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=5 orderedList=false} -->

<!-- code_chunk_output -->
<details>
<summary>Want to jump around the documentation? (Peek at the TOC)</summary>
<br>

- [K8s Essentials](#k8s-essentials)
- [Table of Contents](#table-of-contents)
    - [Certification Related Info](#certification-related-info)
      - [CKAD](#ckad)
        - [Resources](#resources)
        - [Bookmarks](#bookmarks)
    - [Kubernetes Architecture](#kubernetes-architecture)
      - [Control Plane Components](#control-plane-components)
        - [kube-apiserver](#kube-apiserver)
        - [etcd](#etcd)
        - [kube-scheduler](#kube-scheduler)
        - [kube-controller-manager](#kube-controller-manager)
  - [Installation](#installation)
    - [Docker](#docker)
      - [Ubuntu](#ubuntu)
      - [Amazon Linux 2](#amazon-linux-2)
          - [Install Docker](#install-docker)
          - [Start Docker](#start-docker)
          - [Access Docker commands in ec2-user user](#access-docker-commands-in-ec2-user-user)
    - [Installing kubernetes](#installing-kubernetes)
      - [Installing tools (kubectl, kind, minikube, kubeadm)](#installing-tools-kubectl-kind-minikube-kubeadm)
        - [Installing Kubectl on Debian Linux](#installing-kubectl-on-debian-linux)
        - [Installing Kubectl on Amazon Linux 2](#installing-kubectl-on-amazon-linux-2)
        - [Installing Minikube](#installing-minikube)
    - [Interacting with k8's cluster](#interacting-with-k8s-cluster)
      - [MiniKube Addons](#minikube-addons)
        - [Dasboard Addon](#dasboard-addon)
        - [Metrics Server Addon](#metrics-server-addon)
    - [Other tools](#other-tools)
      - [conntrack](#conntrack)
      - [socat](#socat)
    - [Kubernetes Objects](#kubernetes-objects)
      - [Deployments and services](#deployments-and-services)
        - [Running a sample Nginx Stateless Application Using a Deployment](#running-a-sample-nginx-stateless-application-using-a-deployment)
          - [Creating deployment](#creating-deployment)
          - [Updating deployment](#updating-deployment)
          - [Deleting a deployment](#deleting-a-deployment)
        - [Deploying sample servive and deployment](#deploying-sample-servive-and-deployment)
          - [Creating deployment](#creating-deployment-1)
          - [Deleting Deployments](#deleting-deployments)
      - [Networking in K8s](#networking-in-k8s)
  - [Deploying Kubernetes manually](#deploying-kubernetes-manually)
    - [Boostrapping the Cluster](#boostrapping-the-cluster)
    - [Configuring Networking with Flannel](#configuring-networking-with-flannel)
    - [Pods and Containers](#pods-and-containers)
      - [Create a simple pod running an nginx container:](#create-a-simple-pod-running-an-nginx-container)
    - [Networking](#networking)
      - [Create a deployment with two nginx pods:](#create-a-deployment-with-two-nginx-pods)
      - [Create a busybox pod to use for testing:](#create-a-busybox-pod-to-use-for-testing)

<!-- /code_chunk_output -->


</details>


### Certification Related Info

#### CKAD

##### Resources

##### Bookmarks 

Following are the bookmarks of the official documentation. [Download Bookmarks.](content/ckad.html)
They contain most of the basic templates/ docs needed for CKAD. Might save you a few minutes during the certification exam. :)


<details>
<summary>Want to get a short brief about the k8s architecture?</summary>
<br>

### Kubernetes Architecture

- Client-Server architecture
	![](./images/kubernetes/kubernetes-architecture.png)
	
	+ Kubernetes master
		* etcd cluster
		* kube-apiserver
		* kube-controller-manager
		* cloud-controller-manager
		* scheduler

	+ Worker nodes (atleast one) - host pods (components of the application workload)
		* kube-proxy
		* kubelet components
     

#### Control Plane Components

- make global decisions about the cluster
- detecting and responding to cluster events (for example, starting up a new pod when a deployment's replicas field is unsatisfied)
- can be run on any machine in the cluster. (however, for simplicity, set up scripts typically start all control plane components on the same machine, and do not run user containers on this machine)

##### kube-apiserver

- Exposes the Kubernetes API
- API server is the front end for the Kubernetes control plane
- kube-apiserver is designed to scale horizontally (more instances)
- You can run several instances of kube-apiserver and balance traffic between those instances.

##### etcd

- Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data
- Documentation: [etcd](https://etcd.io/docs/)

##### kube-scheduler

- Watches for newly created Pods with no assigned node, and selects a node for them to run on
- Factors taken into account for scheduling decisions include: 
  + individual and collective resource requirements,
  + hardware/software/policy constraints,
  + affinity and anti-affinity specifications,
  + data locality,
  + inter-workload interference,
  + deadlines.


##### kube-controller-manager

- Control Plane component that runs controller processes.
- Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process.
- Some types of these controllers are:
  + Node controller: Responsible for noticing and responding when nodes go down.
  + Job controller: Watches for Job objects that represent one-off tasks, then creates Pods to run those tasks to completion.
  + Endpoints controller: Populates the Endpoints object (that is, joins Services & Pods).
  + Service Account & Token controllers: Create default accounts and API access tokens for new namespaces.

</details>

## Installation

### Docker
You can also refer my docker-essentials gist here for other docker related commands: [gist/docker-essentials](https://gist.github.com/shivanshthapliyal/1abf664fbd39d36cd2c6115ea3f44f4c)


#### Ubuntu

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

    sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"

    sudo apt-get update

    sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu

    sudo apt-mark hold docker-ce

**You can verify that docker is working by running this command:**

    sudo docker version

#### Amazon Linux 2

###### Install Docker

    sudo yum update -y
    sudo yum -y install docker

###### Start Docker

    sudo service docker start

###### Access Docker commands in ec2-user user

    sudo usermod -a -G docker ec2-user
    sudo chmod 666 /var/run/docker.sock
    docker version

----

### Installing kubernetes

#### Installing tools (kubectl, kind, minikube, kubeadm)
Follow [official documentation.](https://kubernetes.io/docs/tasks/tools/#install-kubectl-on-linux)

##### Installing Kubectl on Debian Linux
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl
```
##### Installing Kubectl on Amazon Linux 2
```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

yum install -y kubectl
```

##### Installing Minikube

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube

sudo cp minikube /usr/local/bin   
```
or follow [this link](https://minikube.sigs.k8s.io/docs/start/#linux)

----

### Interacting with k8's cluster

    minikube start

    kubectl get po -A
    minikube kubectl -- get po -A

#### MiniKube Addons

minikube has a built-in list of applications and services that may be easily deployed, such as Istio or Ingress. To list the available addons for your version of minikube:

      minikube addons list

To enable an add-on, see:

      minikube addons enable <name>

To enable an addon at start-up, where –addons option can be specified multiple times:

      minikube start --addons <name1> --addons <name2>

For addons that expose a browser endpoint, you can quickly open them with:

      minikube addons open <name>

To disable an addon:

      minikube addons disable <name>

##### Dasboard Addon 

      minikube addons enable dashboard

      minikube addons list
      kubectl get pod -A

to expose dasboard to internet:
      kubectl proxy --address 0.0.0.0 --accept-hosts '.*'

Get the IP address of your EC2 instance and the Port on which it is serving dashboard:

      kubectl get services --all-namespaces
     

And in your browswer:

http://<IP>:<PORT>/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login

> Note that this is a possible security vulnerability as you are accepting all traffic (AWS firewall rules) and also all connections for your kubectl proxy (--address 0.0.0.0 --accept-hosts '.*') so please narrow it down or use different approach.

##### Metrics Server Addon

Example:

      minikube addons enable metrics-server
      kubectl top nodes

### Other tools


#### conntrack
```
sudo apt-get install -y conntrack socat
```

#### socat
```
sudo apt-get install -y socat
```


---

### Kubernetes Objects
#### Deployments and services

##### Running a sample Nginx Stateless Application Using a Deployment

###### Creating deployment

Create a Deployment based on the YAML file:
```
kubectl apply -f ./sample-deployments/nginx-deployment.yaml
```

Display information about the Deployment:
```
kubectl describe deployment nginx-deployment
```

List the Pods created by the deployment:
```
kubectl get pods -l app=nginx
```

Display information about a Pod:
```
kubectl describe pod <pod-name>
```

###### Updating deployment

Make changes to deployment file, then
```
kubectl apply -f ./sample-deployments/nginx-deployment.yaml
```

Watch the deployment create pods with new names and delete the old pods:
```
kubectl get pods -l app=nginx
```

###### Deleting a deployment
```
kubectl delete deployment nginx-deployment
```


##### Deploying sample servive and deployment

###### Creating deployment

Create a Deployment, Service based on the YAML file

    kubectl apply -f ./sample-deployments/nginx-deployment-svc.yaml

verify the resources

    kubectl get deployments
    kubectl get services

    kubectl get deployment
    kubectl get service

    kubectl describe deployment nginx-deployment



When I want to hit a service, I hit the service using <external-ip:NodePort> right? Then what's the use of Port?:

A Service in k8s is a combination of virtual IP and virtual Port.

Just like we assign an IP to an (ethernet) interface to communicate through it, this Virtual IP:Port combination is a way to communicate through the service.

The targetPort is the target container's port. (if not specified, k8s defaults it to the port (virtual port) of the Service)

The NodePort is the port that is exposed on an ethernet interface (on which k8s cluster is configured) of the machine. (if not specified, k8s chooses a random available port between 30000-32767 for service types NodePort and LoadBalancer).

---

Nodes are an essential part of the Kubernetes cluster. They are the machines where your cluster's container workloads are executed. In this lesson, we will discuss what nodes are in Kubernetes, and we will explore some ways in which you can find information about nodes in your cluster.

Get a list of nodes:
```kubectl get nodes```
Get more information about a specific node:
```kubectl describe node $node_name```


---
###### Deleting Deployments

Delete all the pods in a single namespace with this command:
```
kubectl delete --all pods --namespace=foo
```
Delete all deployments in namespace which will delete all pods attached with the deployments corresponding to the namespace
```
kubectl delete --all deployments --namespace=foo
```
Delete all namespaces and every object in every namespace (but not un-namespaced objects, like nodes and some events) with this command:
```
kubectl delete --all namespaces
```

Or:
You can simply run
```
kubectl delete all --all --all-namespaces
```

The first all means the common resource kinds (pods, replicasets, deployments, ...)
```
kubectl get all == kubectl get pods,rs,deployments, ...
```
The second --all means to select all resources of the selected kinds
Note that all does not include:

non namespaced resourced (e.g., clusterrolebindings, clusterroles, ...)
configmaps
rolebindings
roles
secrets

In order to clean up perfectly,

you could use other tools (e.g., Helm, Kustomize, ...)
you could use a namespace.
you could use labels when you create resources.

---


#### Networking in K8s

High-level overview of what a Kubernetes virtual cluster network looks like. We will also demonstrate how the network functions by contacting one pod from another pod over the virtual network.

Create a deployment with two nginx pods:
```
cat << EOF | kubectl create -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.4
        ports:
        - containerPort: 80
EOF
```

Create a busybox pod to use for testing:
```
cat << EOF | kubectl create -f -
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: busybox
    image: radial/busyboxplus:curl
    args:
    - sleep
    - "1000"
EOF
```
Get the IP addresses of your pods:

```kubectl get pods -o wide```

Get the IP address of one of the nginx pods, then contact that nginx pod from the busybox pod using the nginx pod's IP address:

```kubectl exec busybox -- curl $nginx_pod_ip```

Get a list of Pods in the kube-system namespace:

```kubectl get pods -n kube-system```

Get the same list of Pods using the raw Kubernetes API:

```kubectl get --raw /api/v1/namespaces/kube-system/pods```

Get information on a single Pod using the raw API:

```kubectl get --raw /api/v1/namespaces/kube-system/pods/etcd-k8s-control```

```
kubectl get pods -n kube-system
kubectl get --raw /api/v1/namespaces/kube-system/pods | jq

kubectl get --raw /api/v1/namespaces/kube-system/pods/storage-provisioner | jq
```

---

## Deploying Kubernetes manually 

### Boostrapping the Cluster

On the Kube master node, initialize the cluster:

    sudo kubeadm init --pod-network-cidr=10.244.0.0/16
    
That command may take a few minutes to complete.

When it is done, set up the local kubeconfig:

    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config

Verify that the cluster is responsive and that Kubectl is working:
 
    kubectl version

You should get Server Version as well as Client Version. It should look something like this:

The kubeadm init command should output a kubeadm join command containing a **token** and **hash**. Copy that command and run it with sudo on both worker nodes. It should look something like this:

    sudo kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash

Verify that all nodes have successfully joined the cluster:
    
    kubectl get nodes

You should see all three of your nodes listed.
**Note: The nodes are expected to have a STATUS of NotReady at this point.**

---

### Configuring Networking with Flannel

On master and nodes, run the following:

    echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
    sudo sysctl -p

Install Flannel in the cluster by running this only on the Master node:
    
    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml

Verify that all the nodes now have a STATUS of Ready:

    kubectl get nodes

You should see all three of your servers listed, and all should have a STATUS of Ready.

**Note: It may take a few moments for all nodes to enter the Ready status, so if they are not all Ready, wait a few moments and try again.**

It is also a good idea to verify that the Flannel pods are up and running. Run this command to get a list of system pods:

    kubectl get pods -n kube-system
    
You should have three pods with flannel in the name, and all three should have a status of Running.

---

### Pods and Containers

#### Create a simple pod running an nginx container:
```
  cat << EOF | kubectl create -f -
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
  spec:
    containers:
    - name: nginx
      image: nginx
  EOF
```
Get a list of pods and verify that your new nginx pod is in the Running state:

    kubectl get pods
  
Get more information about your nginx pod:
    
    kubectl describe pod nginx

Delete the pod:

    kubectl delete pod nginx
    
Get a list of nodes:
  
    kubectl get nodes

Get more information about a specific node:
  
    kubectl describe node shivansh_node
    
    
### Networking

Networking is an important part of understanding the basics of Kubernetes. 

#### Create a deployment with two nginx pods:
    cat << EOF | kubectl create -f -
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.15.4
            ports:
            - containerPort: 80
    EOF

#### Create a busybox pod to use for testing:
    cat << EOF | kubectl create -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: busybox
    spec:
      containers:
      - name: busybox
        image: radial/busyboxplus:curl
        args:
        - sleep
        - "1000"
    EOF

**Get the IP addresses of your pods:**

    kubectl get pods -o wide

**Get the IP address of one of the nginx pods, then contact that nginx pod from the busybox pod using the nginx pod's IP address:**

    kubectl exec busybox -- curl $nginx_pod_ip
