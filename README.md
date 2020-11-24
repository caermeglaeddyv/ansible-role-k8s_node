Ansible role: Kubernetes Node
=========

This role is used to prepare worker nodes before joniing to new or existing kubernetes cluster.

For now, it does the following:
- configures kubeadm for join to the cluster
- do necessary taint(s) on nodes
- do some best practice hardening by disabling automatic certificate approval in the cluster and removing cluster-info resources to allow only manual node join in the future


Requirements
------------

This is not strict requirements and it may not work with other versions than tested ones.
Anyway. feel free to test by yourself, suggest addition of new functionality and contribute.

Role is tested with:
- Ansible version >= 2.8.6
- CentOS version >= 7.6 (1803)

Currently supports installation of Kubernetes versions 1.14.1-1.15.10.

"caermeglaeddyv.ansible_role_prepare_k8s_cluster" and "caermeglaeddyv.ansible_role_k8s_control_plane" roles which must be run manually before this one

Kubectl is required on localhost to do management of remote cluster after initialization.

VIP address for api-server must be added to your dns or hosts file with same name as used for cluster initiation ("kubernetes_apiserver_host" variable ), if name used instead of IP.


Role Variables
--------------

Variables and their descriptions copied from defaults/main.yml

```bash

# Variable which is common for most projects, used in
# configuration files or file/directory names.
# By default used as reference for kubernetes_project_dir variable:
kubernetes_project_name: test

# Variable which is common for most projects, used as
# project working directory on the localhost for the role.
# Currently is used for copying admin kubeconfig file or making symlink to it,
# to be able to connect to the cluster and perform taints and/or hardening:
kubernetes_project_dir: files/{{ kubernetes_project_name }}

# DNS name or IP address which will be listening for apiserver requests,
# this must equal to virtual IP to work correct in HA setup:
kubernetes_apiserver_host: ""

# Port which will listen for apiserser requests on apiserver host:
kubernetes_apiserver_port:

# Token which will be used in cluster initialization, so
# other nodes can join the cluster using it:
kubernetes_bootstrap_token: ""          # 123456.abcdefghijklmnop

# List of preflight errors which must be ignored while initializing
# cluster or joining node to existing cluster via kubeadm:
kubernetes_ignore_preflight_errors:
# - NumCPU

# Taints which must be present on nodes:
kubernetes_taints: []
# - node: ""
#   taint: ""

# Disable automatic node join to the cluster:
kubernetes_harden_join: false

```


Dependencies
------------

"caermeglaeddyv.ansible_role_prepare_k8s_cluster" and "caermeglaeddyv.ansible_role_k8s_control_plane" roles which must be run manually before this one

Kubectl is required on localhost to do management of remote cluster after initialization.

VIP address for api-server must be added to your dns or hosts file with same name as used for cluster initiation ("kubernetes_apiserver_host" variable ), if name used instead of IP.


Example Playbook
----------------

```bash
---
- hosts: localhost
  gather_facts: false
  become: no
  tasks:
  - name: Check ansible version >=2.8.6
    assert:
      msg: Ansible must be v2.8.6 or higher
      that:
      - ansible_version.string is version("2.8.6", ">=")
    tags:
    - check
  vars:
    ansible_connection: local

- hosts: all
  become: yes
  tasks:
  - import_role:
      name: caermeglaeddyv.ansible_role_prepare_k8s_cluster
  - import_role:
      name: caermeglaeddyv.ansible_role_k8s_control_plane
  - import_role:
      name: caermeglaeddyv.ansible_role_k8s_node

```

More detailed examples ( inventories, playbooks etc. ) of this and other roles can be found [here](https://github.com/caermeglaeddyv/examples/tree/dev/ansible).

It's highly recommended to start you test deploys from there, especially if you use Google Cloud Platform or VMware vCenter as your infrastructure, for now that [repository](https://github.com/caermeglaeddyv/examples) contains [Packer](https://github.com/caermeglaeddyv/examples/tree/dev/packer) and [Terraform](https://github.com/caermeglaeddyv/examples/tree/dev/terraform) examples to build templates and deploy machines on this platforms.


License
-------

[Apache 2.0](https://github.com/caermeglaeddyv/ansible-role-k8s_node/blob/dev/LICENSE)


Author Information
------------------

Copyright 2020 [caermeglaeddyv](https://github.com/caermeglaeddyv)
