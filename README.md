# Spawning RHEL instance to OpenStack and adding webserver in a single swing with Ansible 

This is a modifed fork of the tutorial created by Michael Solberg 
[Using Ansible to Launch Instances on OpenStack](https://github.com/msolberg/openstack-ansible-demo/tree/master/tutorial)

The original tutorial have been modified with replacing centos with rhel,
modification deploy instance to accomodate multiple floating-ip-pools,
a workaround for an issue with register: nova parameter not gathering
the right info in the first swing and few additions to webserver
installation playbook

It assumes familiarity with the [Ansible
Playbook
Introduction](http://docs.ansible.com/ansible/playbooks_intro.html).
Start there if you haven't written an Ansible playbook before.  The
goal of this tutorial is to launch and configure an instance on an
OpenStack platform.  It assumes that the instance does not exist in
the current inventory of the Ansible host.

## instance-launch.yaml

The first playbook - launch-instance/instance-launch.yaml contains
a simple play with one ansible (1.9) module - nova_compute.
User, tenant credentials need to be modified as well as UUIDs for
private and public(floating IP) networks.

To execute any of the playbooks simple invoke
```
ansible-playbook <playbook.yaml>
```
## install-webserver.yaml

The second playbook install-webserver/install-webserver.yaml is a
combination of few steps:
- copies yum repo file from local directory to spawned instance
- installs http server
- starts http service

Before executing this playbook there is another pre-req that has to
be satisfied and that is adding our webserver host manually to
ansible host file /etc/ansible/hosts
```
Example - 
[webservers]
webserver ansible_ssh_host=X.X.X.X ansible_ssh_user=cloud-user
```

### launch-and-install.yaml

The last playbook launch-and-install/launch-and-install.yaml merges
the other 2 together with addition of few changes. First it takes advantage
of the ansible registers that helps stream output of the task into a
variables - register: nova. Unfortunately, this seems to not be working
in a first swing when there is no instance yet created. As a workaround
we are adding identical task second time.
Then depends on how fast the instance comes up, we need to add a pause.
Rest of the steps is a simple copy and paste from install-webserver piece.
