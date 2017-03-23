# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"

  # workaround vagrant bug for private interface
  config.vm.provision "shell", run: "always", inline: <<-SHELL
    ifup eth1
  SHELL

  config.vm.define "target" do |target|
    target.vm.hostname = "iscsi-target"
    target.vm.network "private_network", ip: "192.168.33.10"
	target.vm.provision "ansible" do |ansible|
	  ansible.playbook = "provision/target/playbook.yml"
	end
  end

  config.vm.define "initiator" do |initiator|
    initiator.vm.hostname = "iscsi-initiator"
    initiator.vm.network "private_network", ip: "192.168.33.100"
	initiator.vm.provision "ansible" do |ansible|
	  ansible.playbook = "provision/initiator/playbook.yml"
	end
	# connect the iscsi initiator to target
	initiator.vm.provision "shell", run: "always", inline: <<-SHELL
	  sudo iscsiadm -m discovery -t sendtargets -p 192.168.33.10
	  sudo iscsiadm -m node --login
	SHELL
  end

end
