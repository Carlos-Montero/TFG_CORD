# M-CORD Profile Installation on Ubuntu 16.04.4 LTS Single Node

This document describes how to install M-CORD profile, on a single node virtual machine in CloudLab.

### PREREQUISITES

  - Ubuntu 16.04.4 LTS server with 64GB of RAM and 32 virtual CPUs
        
  - Lastest versions of released software:

```sh
$ sudo apt update
```

  - user root capabilities

  - Open access to the Internet

  - Public DNS servers are accessible
  
  - Kubernetes 1.10.0

  - Helm v2.10.0

It is possible to deploy all the helm charts that uses M-CORD separetely. 
But, in order to do it simple, exists a script to do it automatically: 

### Installation of M-CORD profile with the convenience script

```sh
$ mkdir ~/cord
$ cd ~/cord
$ git clone https://gerrit.opencord.org/automation-tools
$ automation-tools/mcord/mcord-in-a-box.sh
```


