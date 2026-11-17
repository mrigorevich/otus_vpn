# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :ansible => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "ansible",
        :net => [
                    ["192.168.50.10", 2, "255.255.255.0", "ansible-mgmt"]
                ]
  },
  :server => {
        :box_name => "ubuntu/jammy64",
        :vm_name => "server",
        :net => [   
                    #ip, adapter, netmask, virtualbox__intnet
                    ["192.168.56.10", 2, "255.255.255.0",  "int_net"],
					["192.168.50.1",  3, "255.255.255.0",  "ansible-mgmt"]

                ]
  },
  :client => {
        :box_name => "ubuntu/jammy64",
		:vm_name => "client",
        :net => [
                   ["192.168.56.20",  2, "255.255.255.0",  "int_net"],
				   ["192.168.50.2",   3, "255.255.255.0",  "ansible-mgmt"]
                   ]
  },
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.hostname = boxconfig[:vm_name]
      box.vm.synced_folder "./shared", "/home/vagrant/shared", # пробрасываем в ВМ каталог с хоста
          owner: "vagrant", 
          group: "vagrant"
      box.vm.provider "virtualbox" do |v|
        v.memory = 768
        v.cpus = 1
		v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
       end
	  if boxconfig[:vm_name] == "ansible"
		box.vm.synced_folder "./ansible_files", "/home/vagrant/ansible_files", # пробрасываем в ВМ каталог с хоста
          owner: "vagrant", 
          group: "vagrant"
		  
        box.vm.provision "shell", inline: <<-SHELL
          apt-get update
          apt-get install -y software-properties-common
          add-apt-repository --yes --update ppa:ansible/ansible
          apt-get install -y ansible openssh-client git
        SHELL
      end
      boxconfig[:net].each do |ipconf|
		box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], virtualbox__intnet: ipconf[3])
      end

      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end
		box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
      SHELL
    end
  end
end