# vagrant-packstack

This vagrant environment runs CentOS 7 and the Packstack installer for an all-in-one OpenStack
environment.

## Prereqs

* VirtualBox
* vagrant-hostmanager
* vagrant-timezone
* ansible
 * `mkvirtualenv vagrant-packstack -r requirements.txt`
* 8GB of ram is needed by the virt

## Start the show

1. Once you have your environment up, do `vagrant up` to start the virt and installation. **Note**
this takes a while, go get some coffee...

### OpenStack Orchestration via Ansible

Next up we need to setup OpenStack with a way to let instances get to the outside world, this is
called the provider network. We'll also setup a project and user for doing things not with the admin
user, and then create something.

2. Setup the external provider network and "vagrant" project/user in openstack:
 1. `source provisioning/openstack/keystonerc_admin`
 2. `ansible-playbook provisioning/openstack/admin-setup-tasks.yml`

3. Lets make something:
 1. `source provisioning/openstack/keystonerc_vagrant`
 2. `ansible-playbook provisioning/openstack/vagrant-demo.yml`

After it is online, visit http://packstack.vagrant/dashboard with the creds found in
`provisioning/openstack/keystonerc_[admin|vagrant]` or use the openstack client.

---

## Networking

The vagrant is configured with two interfaces:
1. The default interface used by vagrant to control the box
2. Used by OpenStack as the external provider network, as well as for API access from the host

Here is what this looks like after `admin-setup-tasks.yml` and `vagrant-demo.yml` have been run.

```
+--------+     +----------+
|        |     |          |
|  host  +---> |  enp0s3  |
|        |     |          |
+---+----+     +----------+
    |
    |          +----------+  +---------+
    |          |          |  |         |
    +--------> |  enp0s8  +--+  br-ex  |
               |          |  |         |
               +----------+  +----+----+
                                  |
                                  |
                             +----+-----+
                             |          |
                             |  router  |
                             |          |
                             +----+-----+
                                  |
+------------+   +-------+   +----+-----+
|            |   |       |   |          |
|  instance  +---+  qbr  +---+  br-int  |
|            |   |       |   |          |
+------------+   +-------+   +----------+
```

## Customizing Packstack

Currently the Packstack installer is customized via command line options. If you wish to further
customize the installer, update the command in `provisioning/vagrant/packstack-install.yml`. Running
`packstack --help` will display all the available options. Note, the more services you enable, the
more memory used.

References:
* https://www.rdoproject.org/networking/neutron-with-existing-external-network/
* https://www.rdoproject.org/networking/networking-in-too-much-detail/
* http://docs.ansible.com/ansible/list_of_cloud_modules.html#openstack
