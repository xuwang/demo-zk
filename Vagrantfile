# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.require_version ">= 1.5.0"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  config.vm.box_url = 'https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_centos-6.4_provisionerless.box'
  #config.vm.box_url = '../opscode_centos-6.4_provisionerless.box'
  config.omnibus.chef_version = :latest
  config.berkshelf.enabled = true
  
  
  #zookeepers
  (1..1).each do |i| 
    config.vm.define "zk#{i}" do |node|
      ip = "192.168.33.2#{i}"
      node.vm.box = "zk#{i}"
      node.vm.network "private_network", ip: "#{ip}"
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", "512"]
      end
      
      node.vm.provision :chef_solo do |chef|
        chef.add_recipe 'apt'
        chef.add_recipe 'java'
        chef.add_recipe 'xu-zookeeper'
        chef.json = {
                    :vagrant => {:ipaddress => "#{ip}", :node_id => "#{i}"},
                    :exhibitor => {
                      :opts => {:hostname => "#{ip}"}, 
                      :defaultconfig => {:servers_spec => "1:192.168.33.21"}}
                    } 
      end
    end
  end  
  
  #kafka brookers
  (1..3).each do |i| 
    config.vm.define "kk#{i}" do |node|
      ip = "192.168.33.3#{i}"
      node.vm.box = "kk#{i}"
      node.vm.network "private_network", ip: "#{ip}"
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", "512"]
      end

      node.vm.provision :chef_solo do |chef|
        chef.add_recipe 'apt'    
        chef.add_recipe 'java'
        chef.add_recipe 'kafka'
        chef.json = {
                    :kafka => {
                      :broker_id => i, 
                      :host_name => "#{ip}",
                      :automatic_restart => true,
                      :zookeeper => {:connect => ["192.168.33.21:2181"]}}
                    } 
      end
    end
  end
end