# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.ssh.insert_key = false
  
  config.vm.provider :libvirt do |libvirt|
    libvirt.cpus = 4
    libvirt.memory = 4096
    libvirt.nested = true
    libvirt.qemu_use_session = false
  end

  vms = {
    'node1': "192.168.33.10",
    'node2': "192.168.33.20",
    'node3': "192.168.33.30",
    'node4': "192.168.33.40",
    'node5': "192.168.33.50",
    'node6': "192.168.33.60"
  }

  vms.each do |name, ip|
    config.vm.define "#{name}" do |node|
      node.vm.box = "generic/rocky8"
      node.vm.network "private_network", ip: ip
    end
  end

  config.vm.provision "shell" do |s|
    s.inline = <<-SHELL
      yum-config-manager --disable epel
      yum-config-manager --disable epel-modular
    SHELL
  end

  config.vm.provision "shell" do |s|
    ssh_prv_key = ""
    ssh_pub_key = ""
    if File.file?("#{Dir.home}/.ssh/id_rsa")
      ssh_prv_key = File.read("#{Dir.home}/.ssh/id_rsa")
      ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    else
      puts "No SSH key found. You will need to remedy this before pushing to the repository."
    end
    s.inline = <<-SHELL
      if grep -sq "#{ssh_pub_key}" /home/vagrant/.ssh/authorized_keys; then
        echo "SSH keys already provisioned."
        exit 0;
      fi
      echo "SSH key provisioning."
      mkdir -p /home/vagrant/.ssh/
      touch /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
      echo #{ssh_pub_key} > /home/vagrant/.ssh/id_rsa.pub
      chmod 644 /home/vagrant/.ssh/id_rsa.pub
      echo "#{ssh_prv_key}" > /home/vagrant/.ssh/id_rsa
      chmod 600 /home/vagrant/.ssh/id_rsa
      chown -R vagrant:vagrant /home/vagrant
      exit 0
    SHELL
  end
end

# ansible -i inventory/mycluster/hosts.yaml  -b -u vagrant -m systemd -a "name=firewalld.service state=stopped enabled=false" all