# -*- mode: ruby -*-
# vi: set ft=ruby :

IMAGENAME = "bento/ubuntu-18.04"
NUM_NODES = 2
ANSIBLE_STR = "ansible"

module OS
    def OS.windows?
        (/cygwin|mswin|mingw|bccwin|wince|emx/ =~ RUBY_PLATFORM) != nil
    end

    def OS.mac?
        (/darwin/ =~ RUBY_PLATFORM) != nil
    end

    def OS.unix?
        !OS.windows?
    end

    def OS.linux?
        OS.unix? and not OS.mac?
    end
end

is_windows_host = "#{OS.windows?}"
puts "is_windows_host: #{OS.windows?} #{RUBY_PLATFORM}"
if OS.windows?
  puts "Vagrant launched from windows."
  # Must use local provisioner on Windows
  ANSIBLE_STR = "ansible_local"
elsif OS.mac?
    puts "Vagrant launched from mac."
elsif OS.unix?
    puts "Vagrant launched from unix."
elsif OS.linux?
    puts "Vagrant launched from linux."
else
    puts "Vagrant launched from unknown platform."
end

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.boot_timeout = 800
  config.vm.provider "virtualbox" do |vm|
    vm.memory = 2048
    vm.cpus = 2
  end
  
  config.vm.define "k8s-control" do |control|
    control.vm.box = IMAGENAME
    control.vm.network "private_network", ip: "192.168.50.10"
    control.vm.hostname = "k8s-control"
    control.vm.provision "#{ANSIBLE_STR}" do |ansible|
      ansible.playbook = "k8s-setup/control-playbook.yml"
      ansible.extra_vars = {
        ansible_python_interpreter:"/usr/bin/python3",
        node_ip: "192.168.50.10",
      }
    end
  end

  (1..NUM_NODES).each do |i|
    config.vm.define "k8s-node-#{i}" do |node|
      node.vm.box = IMAGENAME
      node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
      node.vm.hostname = "k8s-node-#{i}"
      node.vm.provision "#{ANSIBLE_STR}" do |ansible|
        ansible.playbook = "k8s-setup/node-playbook.yml"
        ansible.extra_vars = {
          ansible_python_interpreter:"/usr/bin/python3",
          node_ip: "192.168.50.#{i + 10}",
        }
      end
    end
  end
end
