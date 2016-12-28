# -*- mode: ruby -*-
# vi: set ft=ruby :

master_hosts = 3
node_hosts = 3

Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  (1..master_hosts).each do |i|
    config.vm.define "kube-master#{i}" do |node|
      node.vm.box = "centos/7"
      node.vm.hostname = "kube-master#{i}"
      node.vm.network "private_network", ip: "192.168.33.1#{i}"

      node.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.cpus = 2
      end
    end
  end

  (1..node_hosts).each do |i|
    config.vm.define "kube-node#{i}" do |node|
      node.vm.box = "centos/7"
      node.vm.hostname = "kube-node#{i}"
      node.vm.network "private_network", ip: "192.168.33.2#{i}"

      node.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        vb.cpus = 2
      end

      if i == node_hosts
        node.vm.provision :ansible do |ansible|
          ansible.playbook = "entrypoint.yml"
          ansible.groups = {
            "master" => ["kube-master[1:#{master_hosts}]"],
            "node" => ["kube-node[1:#{node_hosts}]"],
            "k8s:children" => ["master", "node"],
          }
          ansible.limit = "all"
          ansible.sudo = true
        end
      end
    end
  end
end
