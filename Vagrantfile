# -*- mode: ruby -*-
#[ vi: set ft=ruby :

# Vagrant Vars
VAGRANTFILE_API_VERSION = "2"
VAGRANT_DEFAULT_PROVIDER="libvirt"
VAGRANT_EXPERIMENTAL="disks"

# VM specfifc Vars
EXTRA_DISK_SIZE="5GB"

# OS specific Vars
BOX_NAME='generic/centos9s'
BOX_VERSION='4.2.16'

# Naming
CONTROL_HOSTNAME="master"
MANAGED_HOSTNAME_PREFIX="node"
DOMAIN="ansible.lab"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vagrant.plugins = "vagrant-libvirt"
  config.ssh.username = "root"
  config.ssh.password = "1234QWer"
  config.ssh.insert_key = 'true'
  config.vm.provision "shell", inline: <<-SHELL
    sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config    
    systemctl restart sshd.service
    useradd -m ansible
    echo "1234QWer" | passwd root     
    echo "1234QWer" | passwd ansible
    SHELL
  (1..3).each do |i| # can be changed to any two integers in series
    config.vm.define "node#{i}" do |node|
      node.vm.box = BOX_NAME
      node.vm.box_version = BOX_VERSION
      node.vm.hostname = MANAGED_HOSTNAME_PREFIX << "#{i}"
      node.vm.network :private_network,ip: "10.10.10.10#{i}",
        libvirt__network_name: "rhce-lab",
        libvirt__host_ip: '10.10.10.1'
      if i == 2 
        EXTRA_DISK_SIZE="10GB"
      end
      if i == 1 or i == 2
        node.vm.disk  :disk, size: EXTRA_DISK_SIZE, name: "secondary disk"
      end
      node.vm.provider "libvirt" do |lv|
        lv.memory = 1024
        lv.cpus = 2
      end
    end
  end
  config.vm.define "master" do |master|
    master.vm.box = BOX_NAME
    master.vm.box_version = BOX_VERSION 
    master.vm.hostname = CONTROL_HOSTNAME
    master.vm.network :private_network,ip: '10.10.10.100',
      libvirt__network_name: "rhce-lab",
      libvirt__host_ip: '10.10.10.1'
    master.vm.provider "libvirt" do |lv|
      lv.memory = 2048
      lv.cpus = 4
    end
  end
end
