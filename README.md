# vagrant-packstack

This vagrant environment runs CentOS 7 and the Packstack installer for an all-in-one OpenStack
environment.

## Prereqs

* VirtualBox
  * Configure a NAT Network

        VBoxManage natnetwork add --netname packstack --network 172.16.1.0/24 --enable
* vagrant-hostmanager
* vagrant-timezone
* vagrant-reload
* ansible

      mkvirtualenv vagrant-packstack -r requirements.txt
* 8GB of ram is needed by the virt

## Start the show

1. Once you have your environment up, do `vagrant up` to start the virt and installation. **Note**
this takes a while, go get some coffee...

_There is a chance of a failure on the last provisioning step (Restart Networking), this is not a fatal error and you can continue_

### OpenStack Orchestration via Ansible

Next up we need to setup OpenStack with a way to let instances get to the outside world, this is
called the provider network. We'll also setup a project and user for doing things not with the admin
user, and then create something.

1. Setup the external provider network and "vagrant" project/user in openstack:
   1. `source provisioning/openstack/keystonerc_admin`
   2. `ansible-playbook provisioning/openstack/admin-setup-tasks.yml`

2. Lets make something:
   1. `source provisioning/openstack/keystonerc_vagrant`
   2. `ansible-playbook provisioning/openstack/vagrant-demo.yml`

After it is online, visit http://packstack.vagrant/dashboard with the creds found in
`provisioning/openstack/keystonerc_[admin|vagrant]` or use the openstack client.

---

## Networking

The vagrant is configured with three interfaces:
1. The default interface used by vagrant to control the box
2. Used by OpenStack for API access from the host
3. Used by OpenStack for external access for instances to reach the internet

Due to the limitations of the NAT Network and Host-Only interfaces, we are not able to reach
instances directly from host, but only locally from within the vagrant. All signs point to
VirtualBox filtering these interfaces before they reach the guest. A thought was to use Bridged
networking, but that is too dependent on someone's networking environment that I could not write a
portable environment to handle all cases.

## Customizing Packstack

Currently the Packstack installer is customized via command line options. If you wish to further
customize the installer, update the command in `provisioning/vagrant/packstack-install.yml`. Running
`packstack --help` will display all the available options. Note, the more services you enable, the
more memory used.

---

## VMWare Fusion

VMWare Fusion doesn't have these network limitations and so we don't need that third NAT Network interface and are able to directly access instances from the host.

### Nested KVM

At this time I cannot enable nested kvm support (presumably) due to these two bugs:

* 3.10 kernel bug - https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1278531
* Apparent VMWare bug - https://bugzilla.redhat.com/show_bug.cgi?id=1456407

---

References:
* https://www.rdoproject.org/networking/neutron-with-existing-external-network/
* https://www.rdoproject.org/networking/networking-in-too-much-detail/
* http://docs.ansible.com/ansible/list_of_cloud_modules.html#openstack
