Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.timezone.value = :host
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true

  cached_addresses = {}
  config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
    if cached_addresses[vm.name].nil?
      if hostname = (vm.ssh_info && vm.ssh_info[:host])
        vm.communicate.execute("ip -f inet -o addr show ens32 | awk '{print $4}' | cut -d '/' -f 1") do |type, contents|
          cached_addresses[vm.name] = contents.chomp
        end
      end
    end
    cached_addresses[vm.name]
  end

  config.vm.define "packstack" do |ps|
    ps.vm.box = "bento/centos-7.3"
    ps.vm.host_name = "packstack.vagrant"
    ps.vm.network 'private_network', ip: "172.16.0.10", auto_config: false
    ps.vm.provider :virtualbox do |vb|
      vb.memory = 8192
      vb.cpus = 4
      vb.customize ['guestproperty', 'set', :id, '/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold', 10000]
    end
    ps.vm.provider "vmware_fusion" do |vm|
      vm.vmx["memsize"] = 8192
      vm.vmx["numvcpus"] = 4
      vm.vmx["vhv.enable"] = "TRUE"
    end

    ps.vm.provision "ansible" do |ansible|
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
