# coding: utf-8
# copyright Utrecht University
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Configuration variables.
VAGRANTFILE_API_VERSION = "2"

BOX = 'generic/centos7'
GUI = false
CPU = 2
RAM = 4096

DOMAIN  = ".yoda.test"
NETWORK = "192.168.56."
NETMASK = "255.255.255.0"

HOSTS = {
  "combined" => [NETWORK+"10", CPU, RAM, GUI, BOX],
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.insert_key = false

  HOSTS.each do | (name, cfg) |
    ipaddr, cpu, ram, gui, box = cfg

    config.vm.define name do |machine|
      machine.vm.box = box

      machine.vm.provider :virtualbox do |vbox|
        vbox.gui    = gui
        vbox.cpus   = cpu
        vbox.memory = ram
        vbox.name   = name
        vbox.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]
        vbox.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end

      machine.vm.provider :libvirt do |libvirt|
        libvirt.driver = "kvm"
        libvirt.cpus   = cpu
        libvirt.memory = ram
      end

      machine.vm.hostname = name + DOMAIN
      machine.vm.network 'private_network', ip: ipaddr, netmask: NETMASK
      machine.vm.synced_folder ".", "/vagrant", disabled: true
      machine.vm.provision "shell",
        inline: "sudo timedatectl set-timezone Europe/Amsterdam"
      machine.vm.provision "shell",
        inline: "sudo echo \"192.168.56.10 api.eus.yoda.test\" | sudo tee -a /etc/hosts"
    end
  end

  # Provision controller for Ansible on Windows host.
  if Vagrant::Util::Platform.windows? then
    config.vm.define "controller" do |controller|
      controller.vm.provider :virtualbox do |vbox|
        vbox.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000]
      end
      controller.vm.box = 'generic/ubuntu2004'
      controller.vm.hostname = "controller"
      controller.vm.network :private_network, ip: "192.168.56.5", netmask: NETMASK
      controller.vm.provision "shell", privileged: false, path: "vagrant/provision_controller.sh"
      controller.vm.synced_folder ".", "/vagrant", disabled: true
      controller.vm.provision "shell",
        inline: "sudo timedatectl set-timezone Europe/Amsterdam"
    end
  end
end
