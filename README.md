# CorfuDB-Ansible

This repository contains a sample Ansible deployment playbook for CorfuDB.
It is used to configure, deploy and orchestrate multi-node CorfuDB deployments.

## How do I use this repository?

To use this repository, you need to have [Ansible](http://docs.ansible.com)
installed. You can run Ansible on Linux, Mac OS X and Windows, although we
currently only support deployment on Debian-flavored Linux machines (pull requests
are welcome, however!).

Ansible is available on most Linux package managers. To install, simply run:

```
# Debian-flavored Linux
$ sudo apt-get install ansible
```
or
```
# Redhat-flavored Linux
$ sudo yum install ansible
```

On Mac OS X, Ansible is available via the [Homebrew](http://brew.sh). Install with
```
$ brew install ansible
```

On Windows platforms, you can install Ansible in cygwin.
[This](https://servercheck.in/blog/running-ansible-within-windows) site provides
a good walkthrough.

## Customizing the playbook

At minimum, you will need to create an Ansible inventory file describing your desired
deployment configuration. The [sample_hosts](sample_hosts) file provides a good starting point, replace
the sample hosts with servers you want to provision.

A CorfuDB deployment requires one sequencer, one configmaster, and one or more logunits.
If your deployment does not contain all of these components, the playbook will fail.

The playbook currently sets up each logunit as a stripe (that is, replica=1). If you
want to increase the replication factor, you will need to modify the configmaster role.

The file `group_vars/all` defines some global variables for the deployment. In particular,
the deployment page size is set here.

## What the playbook does

Currently, for each node, the `setup_deployment.yml` playbook checks out CorfuDB from github,
builds Debian packages using maven, and installs the built packages for each system.
Configuration files are automatically generated based on the configuration given
in the inventory. 

Future versions of this repository will allow either install from local Debian packages
or from a launchpad ppa, when that is available.

## Running the playbook

After you have setup the configuration, use ansible-playbook to run the `setup_deployment.yml`
script. For example, if your inventory file is named `inventory`:

```
$ ansible-playbook setup_deployment.yml -i inventory
```
##Common issues

You might need to install sshpass. On Mac OS X:
```
$ brew install https://raw.github.com/eugeneoden/homebrew/eca9de1/Library/Formula/sshpass.rb
```
or
```
# Debian-flavored Linux
$ sudo apt-get install sshpass
```
You might also have to put the following in the .ansible.cfg file in your home directory:
```
[defaults]
host_key_checking=false
[ssh_connection]
control_path = %(directory)s/%%h-%%r
```