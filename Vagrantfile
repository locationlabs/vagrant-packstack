Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.timezone.value = :host
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true

  config.vm.define "packstack" do |ps|
    ps.vm.box = "bento/centos-7.3"
    ps.vm.host_name = "packstack.vagrant"
    ps.vm.network 'private_network', ip: "172.16.0.10", auto_config: false

    ps.vm.provider :virtualbox do |vb, override|
      vb.memory = 8192
      vb.cpus = 2
      vb.customize ['guestproperty', 'set', :id, '/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold', 10000]
      override.vm.provision "ansible" do |ansible|
        ansible.playbook = "provisioning/vagrant/packstack-install.yml"
        ansible.extra_vars = "provisioning/vagrant/vars-virtualbox.yml"
        ansible.sudo = true
        ansible.groups = {
          "vagrant" => ["packstack"]
        }
        ansible.galaxy_role_file = "provisioning/vagrant/requirements.yml"
        ansible.galaxy_roles_path = "provisioning/vagrant/shared-roles"
      end
    end

    ps.vm.provider "vmware_fusion" do |vm, override|
      vm.vmx["memsize"] = 8192
      vm.vmx["numvcpus"] = 2
      vm.vmx["vhv.enable"] = "TRUE"
      override.vm.provision "ansible" do |ansible|
        ansible.playbook = "provisioning/vagrant/packstack-install.yml"
        ansible.extra_vars = "provisioning/vagrant/vars-vmware.yml"
        ansible.sudo = true
        ansible.groups = {
          "vagrant" => ["packstack"]
        }
        ansible.galaxy_role_file = "provisioning/vagrant/requirements.yml"
        ansible.galaxy_roles_path = "provisioning/vagrant/shared-roles"
      end
    end
  end
end
