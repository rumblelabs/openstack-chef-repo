# to use this vagrantfile, do either one of the following (not both):
# 1) export VAGRANT_VAGRANTFILE=Vagrantfile-aio-neutron
# 2) mv Vagrantfile-aio-neutron Vagrantfile
#
# and then use as normal:
# vagrant up centos65
# AND/OR
# vagrant up ubuntu1204

Vagrant.require_version ">= 1.1"

Vagrant.require_plugin "vagrant-chef-zero"
Vagrant.require_plugin "vagrant-omnibus"

Vagrant.configure("2") do |config|
  # Berkshelf plugin configuration
  # config.berkshelf.enabled = true

  # Chef-Zero plugin configuration
  config.chef_zero.enabled = true
  config.chef_zero.chef_repo_path = "."

  # pre-installed with berks
  config.chef_zero.cookbooks = ".cookbooks"

  # Omnibus plugin configuration
  config.omnibus.chef_version = :latest

  # get local ip so that we can force chef zero onto a different port per
  # machine, allowing for multiple simultaneous vagrant up runs
  local_ip = Socket.ip_address_list.detect{|intf| intf.ipv4_private?}.ip_address

  # OpenStack-related settings
  config.vm.network "private_network", ip: "192.168.100.60"
  chef_environment = "vagrant-aio-neutron"
  chef_run_list = [ "role[allinone-compute]","role[os-image-upload]" ]

  # virtualbox provider settings
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--cpus", 2]
    vb.customize ["modifyvm", :id, "--memory", 2048]
    vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
  end

  #######################
  # Ubuntu 12.04        #
  #######################

  config.vm.define :ubuntu1204 do |ubuntu1204|

    ubuntu1204.vm.hostname = "ubuntu1204"

    ubuntu1204.vm.box = "opscode-ubuntu-12.04"

    ubuntu1204.vm.box_url = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-12.04_chef-provisionerless.box"

    ubuntu1204.vm.network "forwarded_port", guest: 443, host: 8443     # dashboard-ssl
    ubuntu1204.vm.network "forwarded_port", guest: 5000, host: 5000    # identity-api
    ubuntu1204.vm.network "forwarded_port", guest: 8773, host: 8773    # compute-ec2-api
    ubuntu1204.vm.network "forwarded_port", guest: 8774, host: 8774    # compute-api
    ubuntu1204.vm.network "forwarded_port", guest: 4002, host: 4002    # chef-zero
    ubuntu1204.vm.network "forwarded_port", guest: 35357, host: 35357  # identity-admin-api

    ubuntu1204.vm.provision :chef_client do |chef|
      chef.environment = chef_environment
      chef.run_list = chef_run_list.unshift("recipe[apt::cacher-client]")
      chef.chef_server_url        = "http://#{local_ip}:4002"
    end
  end

  #######################
  # Centos 6.5          #
  #######################

  config.vm.define :centos65 do |centos65|

    centos65.vm.hostname = "centos65"

    centos65.vm.box = "opscode-centos-6.5"

    centos65.vm.box_url = "http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.5_chef-provisionerless.box"

    centos65.vm.network "forwarded_port", guest: 443, host: 9443     # dashboard-ssl
    centos65.vm.network "forwarded_port", guest: 8773, host: 9773    # compute-ec2-api
    centos65.vm.network "forwarded_port", guest: 8774, host: 9774    # compute-api
    centos65.vm.network "forwarded_port", guest: 4001, host: 4001    # chef-zero

    centos65.vm.provision :chef_client do |chef|
      chef.environment = chef_environment
      chef.run_list = chef_run_list
      chef.chef_server_url        = "http://#{local_ip}:4001"
    end
  end
end
