# M-CORD Profile Installation on Ubuntu 16.04.4 LTS Single Node

This document describes how to install M-CORD profile, on a single node virtual machine in CloudLab.

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


