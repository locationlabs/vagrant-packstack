# vagrant-packstack

This vagrant environment runs CentOS 7 and the Packstack installer for an all-in-one OpenStack
environment.

## Prereqs

* VirtualBox
* vagrant-hostmanager
* vagrant-timezone
* ansible
 * `mkvirtualenv vagrant-packstack -r requirements.txt`
* 6GB of ram is needed by the virt

## Start the show

Once you have your environment up, do `vagrant up` to start the virt and installation. **Note** this
takes a while

After it is online, visit http://packstack.vagrant/dashboard with the creds found in
`/root/keystone_admin`
