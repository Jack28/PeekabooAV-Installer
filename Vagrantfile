# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.synced_folder '.', '/vagrant'

  config.vm.define "peekaboo" do |peekaboo|
    peekaboo.vm.box       = "generic/ubuntu1804"
    peekaboo.vm.hostname  = "peekabooav.int"
    config.ssh.username   = 'vagrant'
    config.ssh.password   = 'vagrant'
    config.ssh.insert_key = 'true'

    peekaboo.vm.network "private_network", ip: "192.168.56.5"
    #peekaboo.vm.network "public_network", type: "dhcp"

    # port forward to cuckoo web
    peekaboo.vm.network "forwarded_port", guest: 8000, host: 8000, host_ip: "127.0.0.1"
    # port forward to cuckoo api
    peekaboo.vm.network "forwarded_port", guest: 8090, host: 8090, host_ip: "127.0.0.1"
    # port forward to amavis
    peekaboo.vm.network "forwarded_port", guest: 10024, host: 10024, host_ip: "127.0.0.1"

    peekaboo.vm.provider "virtualbox" do |vb|
      vb.name   = "PeekabooAV"
      vb.memory = 3072
      vb.cpus   = 2
      # if you need more disk space use
      # vagrant plugin install vagrant-disksize
      if Vagrant.has_plugin?("vagrant-disksize")
        config.disksize.size  = '50GB'
      end
    end
  end

  config.vm.provision "shell" do |install|
    # change directory first (args + env not suitable)
    install.inline = "cd /vagrant && NOANSIBLE=yes ./PeekabooAV-install.sh --quiet"
  end

  config.vm.provision "ansible_local" do |ansible|
    ansible.become         = true
    ansible.playbook       = "PeekabooAV-install.yml"
    ansible.inventory_path = "ansible-inventory"
    ansible.limit          = "all"
  end

  config.vm.provision 'shell', inline: 'passwd --delete vagrant'
end
