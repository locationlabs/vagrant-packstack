Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.timezone.value = :host
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true

  config.vm.define "packstack" do |p|
    p.vm.box = "bento/centos-7.3"
    p.vm.host_name = "packstack.vagrant"
    p.vm.provider :virtualbox do |vb|
      vb.memory = 6144
      vb.cpus = 4
      vb.customize ['guestproperty', 'set', :id, '/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold', 10000]
    end
    p.vm.provider "vmware_fusion" do |vm|
      vm.vmx["memsize"] = 6144
      vm.vmx["numvcpus"] = 4
      vm.vmx["vhv.enable"] = "TRUE"
    end

    p.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/packstack.yml"
      ansible.sudo = true
      ansible.groups = {
        "vagrant" => ["packstack"]
      }
      ansible.galaxy_role_file = "provisioning/requirements.yml"
      ansible.galaxy_roles_path = "provisioning/shared-roles"
    end
  end
end
