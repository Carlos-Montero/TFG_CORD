# R-CORD Profile Installation on Ubuntu 16.04 Single Node

This document describes how to install the R-CORD profile, plus a SimpleExampleService, on a single machine.

### PREREQUISITES: Docker and Python installation

Docker and Python installation:

```sh
$ sudo apt update
$ sudo apt-get install python
$ sudo apt-get install python-pip
$ pip install requests
$ sudo apt install -y docker.io
$ sudo systemctl start docker
$ sudo systemctl enable docker
```

To verify the version used in Docker, we use:
```sh
$ docker --version
```

### Minikube and Kubectl

Minikube and Kubectl installation:

```sh
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
$ chmod +x minikube
$ sudo mv minikube /usr/local/bin/
$ curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x kubectl
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
$ chmod +x ./kubectl
$ sudo mv ./kubectl /usr/local/bin/kubectl
```

Issue the following commands:

```sh
$ export MINIKUBE_WANTUPDATENOTIFICATION=false
$ export MINIKUBE_WANTREPORTERRORPROMPT=false
$ export MINIKUBE_HOME=$HOME
$ export CHANGE_MINIKUBE_NONE_USER=true
$ mkdir -p $HOME/.kube
$ touch $HOME/.kube/config
$ export KUBECONFIG=$HOME/.kube/config
```

Navigate to /usr/local/bin/

```sh
$ cd /usr/local/bin/
```

Check if there is any error:

```sh
$ sudo -E ./minikube start --vm-driver=none
```

To verify that Minikube cluster is up and running, we can issue:

```sh
$ kubectl cluster-info
```


### Export the KUBECONFIG File

Locate the KUBECONFIG file:

```sh
$ sudo updatedb
$ locate kubeconfig
```

Export a KUBECONFIG variable containing the path to the configuration file found above:

```sh
$ export KUBECONFIG=/var/lib/localkube/kubeconfig
```

Change permissions of the next folders and files:

```sh
$ cd /var/lib
$ sudo chmod 777 localkube
$ cd localkube
$ ls
```

Change permissions on "kubeconfig" file and "certs" folder:

```sh
$ sudo chmod 777 kubeconfig
$ sudo chmod 777 certs
```

Navigate to /certs and change permissions on the next files:

```sh
$ cd certs
$ ls
$ sudo chmod 777 apiserver.crt
$ sudo chmod 777 apiserver.key
$ sudo chmod 777 ca.crt
```

### Download CORD from git repository

Git clone:

```sh
$ mkdir ~/cord
$ cd ~/cord
$ git clone https://gerrit.opencord.org/helm-charts
$ cd helm-charts
```

### Run the Helm installer script

Doing this, it will automatically grab the lastest version of the Helm client and install it locally:

```sh
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

### Tiller

Issue the following:

```sh
$ sudo helm init
$ sudo kubectl create serviceaccount --namespace kube-system tiller
$ sudo kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
$ sudo kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'      
$ sudo helm init --service-account tiller --upgrade
```

Install socat to fix a port-forwarding error:

```sh
$ sudo apt-get install socat
```

Issue the following and make sure no errors come up (theorically resolved with the permission commands from above):

```sh
$ helm ls
```

### Deploy CORD Helm Charts

Deploy the service profiles corresponding to the "xos-core", "base-kubernetes", and "demo-simpleexampleservice" helm-charts:

```sh
$ cd ~/cord/helm-charts
$ helm init
$ sudo helm dep update xos-core
$ sudo helm install xos-core -n xos-core
$ sudo helm dep update xos-profiles/base-kubernetes
$ sudo helm install xos-profiles/base-kubernetes -n base-kubernetes
$ sudo helm dep update xos-profiles/demo-simpleexampleservice
$ sudo helm install xos-profiles/demo-simpleexampleservice -n demo-simpleexampleservice

```

### Install R-CORD Profile

```sh
sudo helm dep update xos-profiles/rcord-lite
sudo helm install -n rcord-lite xos-profiles/rcord-lite
```

### Install Kafka Helm Chart (Optional)

```sh
sudo helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
sudo helm install -f examples/kafka-single.yaml --version 0.8.8 -n cord-kafka incubator/kafka
sudo helm install -f examples/kafka-single.yaml --version 0.8.8 -n voltha-kafka incubator/kafka
```

### Install Hippie OSS Helm Chart (Optional)
```sh
sudo helm install -n hippie-oss xos-services/hippie-oss
```

### Verification

Use "kubectl get pods" to verify that all containers in the profile are successful and none are in the error state:

```sh
$ kubectl get pods
```

And, to see in deep the state of the pods:

```sh
$ kubectl describe pod 
```

In the next link there is documentation related with debugging Kubernetes CrashLoopBacckoff pods:

https://sysdig.com/blog/debug-kubernetes-crashloopbackoff/

> Note: It will take some time for the various
> helm charts to deploy and the containers to 
> come online. The "tosca-loader" container 
> may error and retry several times as they 
> wait for services to be dynamically loaded. 
> This is normal, and eventually the "tosca-
> loader" containers will enter the completed 
> state.

These information are extract from:

https://guide.opencord.org/linux.html
