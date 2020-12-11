# -*- mode: ruby -*-
# vi: set ft=ruby :

IMAGENAME = "bento/ubuntu-18.04"
NUM_NODES = 2
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.boot_timeout = 800
  config.vm.provider "virtualbox" do |vm|
    vm.memory = 1024
    vm.cpus = 1
  end
  
  config.vm.define "k8s-master" do |master|
    master.vm.box = IMAGENAME
    master.vm.network "private_network", ip: "192.168.50.10"
    master.vm.hostname = "k8s-master"
    master.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "k8s-setup/master-playbook.yml"
      ansible.extra_vars = {
        ansible_python_interpreter:"/usr/bin/python3",
        node_ip: "192.168.50.10",
      }
    end
  end

  (1..NUM_NODES).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.box = IMAGENAME
      node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
      node.vm.hostname = "node-#{i}"
      node.vm.provision "ansible_local" do |ansible|
        ansible.playbook = "k8s-setup/node-playbook.yml"
        ansible.extra_vars = {
          ansible_python_interpreter:"/usr/bin/python3",
          node_ip: "192.168.50.#{i + 10}",
        }
      end
    end
  end
end
