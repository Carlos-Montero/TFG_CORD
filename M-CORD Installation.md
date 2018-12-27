# M-CORD Profile Installation on Ubuntu 16.04.4 LTS Single Node

This document describes how to install M-CORD profile, on a single node virtual machine in CloudLab.

### PREREQUISITES

Lastest versions of released software:

```sh
$ sudo apt update
```

Kubernetes 1.10.0:

```sh
$ sudo apt update
$ sudo apt-get install python
$ sudo apt-get install python-pip
$ pip install requests
$ sudo apt install -y docker.io
$ sudo systemctl start docker
$ sudo systemctl enable docker
```

Helm v2.10.0:
```sh
$ docker --version
```

### Minikube and Kubectl

Minikube and Kubectl installation:


