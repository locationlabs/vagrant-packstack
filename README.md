# vagrant-packstack

This vagrant environment runs CentOS 7 and the Packstack installer for an all-in-one OpenStack
environment.

## Prereqs

* VirtualBox
* vagrant-hostmanager
* vagrant-timezone
* ansible
 * `mkvirtualenv vagrant-packstack -r requirements.txt`
* GB of ram is needed by the virt

## Start the show

1. Once you have your environment up, do `vagrant up` to start the virt and installation. **Note** this
takes a while

2. Provision the "vagrant" project and user in openstack:
 1. `source provisioning/openstack/keystonerc_admin`
 2. `ansible-playbook provisioning/openstack/vagrant-demo-env.yml`

After it is online, visit http://packstack.vagrant/dashboard with the creds found in
`provisioning/openstack/keystonerc_[admin|vagrant]` or use the openstack client.
