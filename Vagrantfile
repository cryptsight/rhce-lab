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

# Naming
CONTROL_HOSTNAME="master"
MANAGED_HOSTNAME_PREFIX="node"
DOMAIN="ansible.lab"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vagrant.plugins = "vagrant-libvirt"
  config.ssh.insert_key = 'true'
  config.vm.provision "shell", inline: <<-SHELL
    sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config    
    echo -e '10.10.10.100 master.ansible.lab master\n10.10.10.201 node1.ansible.lab node1\n10.10.10.202 node2.ansible.lab node2\n10.10.10.203 node3.ansible.lab node3' >> /etc/hosts 
    echo 'ansible ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers.d/ansible
    systemctl restart sshd.service
    useradd -m ansible
    mkdir -p /home/ansible/rhce/ /home/ansible/.ssh
    chown -R ansible:ansible /home/ansible
  SHELL
  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "kvm"
    libvirt.host = 'localhost'
    libvirt.uri = 'qemu:///system'
  end
  (1..3).each do |i| # can be changed to any two integers in series
    config.vm.define "node#{i}" do |node|
      node.vm.box = BOX_NAME
      node.vm.hostname = "#{MANAGED_HOSTNAME_PREFIX}#{i}.#{DOMAIN}"
      node.vm.network :private_network, 
                      :ip => "10.10.10.20#{i}", 
                      :libvirt__netmask => '255.255.255.0',
                      :libvirt__network_name => "rhce-lab", 
                      :libvirt__forward_mode => 'none'
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
    master.vm.hostname = "#{CONTROL_HOSTNAME}.#{DOMAIN}"
    master.vm.network :private_network, 
                      :ip => "10.10.10.100", 
                      :libvirt__network_name =>  "rhce-lab",
                      :libvirt__forward_mode => 'none'
    master.vm.provider "libvirt" do |lv|
      lv.memory = 2048
      lv.cpus = 4
    end
  end
end
