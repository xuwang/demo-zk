# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box_url = "https://opscode-vm.s3.amazonaws.com/vagrant/opscode_ubuntu-12.04_provisionerless.box"
  
  # Installing/Upgrading chef on the target image
  $bootstrap_chef = <<-SCRIPT
    sudo apt-get update -y && 
    sudo apt-get install -q -y rubygems && 
    sudo gem install chef --no-ri --no-rdoc
  SCRIPT

  (1..3).each do |i| 
    config.vm.define "zk#{i}" do |node|
      ip = "192.168.33.2#{i}"
      node.vm.box = "zk#{i}"
      node.vm.network "private_network", ip: "#{ip}"
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", "512"]
      end
      
      node.vm.provision :shell, :inline => $bootstrap_chef

      node.vm.provision :chef_solo do |chef|
        chef.run_list = [
          'recipe[xu-zookeeper::default]'
        ]
        chef.json = {:vagrant => {:ipaddress => "#{ip}", :node_id => "#{i}"},
                    :exhibitor =>{:opts => {:hostname => "#{ip}"}},
                    :exhibitor => {:defaultconfig => {:servers_spec => "1:192.168.33.21,2:192.168.33.22,3:192.168.33.23"}}
                    } 
      end
    end
  end
end
