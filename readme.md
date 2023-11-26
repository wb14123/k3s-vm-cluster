
## Introduction

This is a project to install K3s on a virtual machine cluster. The virtual machine cluster is created by Vagrant.

It will start a cluster with 4 vritual machines: 3 machines as K3s servers so that we have a HA K3s cluster. The 4th machine acts as K3s agent.

Each machine will create a second storage disk which images are stored in `/data/vagrant/disks`. This is for experimenting distributed file systems like CephFS.

## Prerequisite

* Virtualbox is installed.
* Vagrant is installed.
* SSH public key at `~/.ssh/id_rsa.pub`
* Path `/data/vagrant/disks` exists to store second storage disk image for each VM.

## Start Cluster

Run this command to create and start VMs:

```
vagrant up
```

K3s will be installed using the configurations in `k3s-config`.

After it's finished, you can ssh to each machine by `vagrant ssh`, e.g. `vagrant ssh k3s-server1`.

## Install Ceph

An example of installing Ceph with Rook is under the folder `ceph`. It will be copied to `k3s-server1`. Ssh to `k3s-server1` and read `ceph/readme.md` to see the steps to install Ceph.
