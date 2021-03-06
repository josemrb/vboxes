# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANT_API_VERSION = 2

Vagrant.configure(VAGRANT_API_VERSION) do |config|
  config.ssh.forward_agent = true

  config.user.vboxes.each do |vbox, data|
    config.vm.box = data.vm.box
    config.vm.box_check_update = true

    if data.vm.hostmanager
      config.hostmanager.enabled = true
      config.hostmanager.manage_host = true
      config.hostmanager.ignore_private_ip = false
      config.hostmanager.include_offline = true
    end

    config.vm.define vbox do |current|
      current.vm.hostname = data.vm.hostname

      data.vm.synced_folders.each do |item|
        current.vm.synced_folder item[:host], item[:guest]
      end

      data.vm.forwarded_ports.each do |item|
        current.vm.network :forwarded_port,
                           guest: item[:guest],
                           host: item[:host]
      end

      data.vm.networks.private.each do |item|
        current.vm.network :private_network, ip: item[:ip] if item.key?(:ip)
        current.vm.network :private_network, type: 'dhcp' if item.key?(:dhcp)
      end

      current.vm.provider :virtualbox do |vb|
        vb.cpus = data.virtualbox.cpus
        vb.memory = data.virtualbox.memory
        vb.name = data.virtualbox.name if data.virtualbox.key?(:name)
      end

      current.vm.provision :ansible do |ansible|
        ansible.playbook = "./ansible/#{data.ansible.playbook}"
        ansible.extra_vars = data.ansible.extra_vars
      end
    end
  end
end
