# R-CORD Profile Installation on Ubuntu 16.04 Single Node

This document describes how to install R-CORD profile, on a single node virtual machine in CloudLab using the AT&T workflow.

### PREREQUISITES

  - Ubuntu 16.04 LTS server.
        
  - Lastest versions of released software:

```sh
$ apt get update

```

It is possible to deploy all the helm charts that uses R-CORD separetely. 
But, in order to do it simple, exists a script to do it automatically: 

### Installation of R-CORD profile with the convenience script

```sh
$ mkdir -p ~/cord
$ cd ~/cord
$ git clone https://gerrit.opencord.org/automation-toolss
$ cd automation-tools/seba-in-a-box
```

If we want to install a SiaB that uses the released service versions specified in the Helm charts, we proceed:

```sh
$ make 
```

In other case, if we want to use the lastest releases for the charts, we use:

```sh
$ make latest 
```


### Validating the installation

In order to verify if the set up has been done property, is possible to check it with:

```sh
$ helm list
```

And, to tests basic SEBA functionalities, we use:

```sh
$ make run-tests 
```
