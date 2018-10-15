# CORD Installation with SEBA

This document describes how to install CORD with SEBA, plus a SimpleExampleService, on a single machine.

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


### Deploy SEBA Helm Charts

Deploy SEBA:

```sh
$ helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
$ helm repo add cord https://charts.opencord.org/master
$ helm repo add cord6 https://charts.opencord.org/cord-6.0
$ helm dep update nem-core
$ helm dep update profile/bng-in-fabric
$ helm dep update seba-substrate
$ helm install nem-core -n nem-core
$ helm install profile/bng-in-fabric -n nem-svc
$ helm install seba-substrate -n seba-substrate
$ helm upgrade seba-substrate --set voltha.etcd-operator.customResources.createEtcdClusterCRD=true seba-substrate
```

After installing the SEBA charts above, the following commands can be used to wait until all pods have started successfully:

```sh
../tools/wait-for-pods.sh
../tools/wait-for-pods.sh voltha
```


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
