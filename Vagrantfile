#!/usr/bin/env ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
servers = YAML.load_file('nodes.yml')

Vagrant.configure("2") do |config|

  config.vm.box = "trusty64nocm"
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"

  servers.each.with_index do |servers, index|
    config.vm.define servers["name"].to_sym do |serv|
      serv.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--memory", servers["memory"]]
        v.customize ["modifyvm", :id, "--name", servers["name"]]
      end
      serv.vm.hostname = servers["name"]
      serv.vm.network "private_network", ip: servers["ip"]
      if index == 3
        serv.vm.provision "ansible" do |ansible|
          ansible.limit= "all"
          ansible.playbook = "ansible/playbook.yml"
          ansible.groups = {
            "mesos-master" => ["mesos00"],
            "mesos-slave" => ["mesos02","mesos01","mesos04"],
            "all_groups:children" => ["mesos-master", "mesos-slave"]
          }
        end
      end
    end 

  end

end