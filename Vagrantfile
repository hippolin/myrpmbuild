# -*- mode: ruby -*-
# vi: set ft=ruby :

def is_plugin(name)
  if Vagrant.has_plugin?(name)
    puts "using #{name}"
  else
    puts "please run vagrant plugin install #{name}"
    exit(1)
  end
end

Vagrant.configure(2) do |config|

  config.vm.box = 'bento/centos-7.1'

  is_plugin("vagrant-berkshelf")
  is_plugin("vagrant-omnibus")
  is_plugin("vagrant-hosts")

  config.vm.provider :virtualbox do |vb, override|

    vb.customize ["modifyvm", :id, "--memory", "#{1024*2}"]
    vb.customize ["modifyvm", :id,  "--cpus",  "2"]

    if Vagrant.has_plugin?("vagrant-cachier")
      override.cache.enable :yum
      override.cache.scope = :box
    end
  end

  config.berkshelf.enabled = true
  config.berkshelf.berksfile_path = "./Berksfile"

  config.vm.provision :chef_solo do |chef|
    chef.environments_path = 'environments'
    chef.environment = 'rpmbuild'
    chef.add_recipe "yum"
    chef.add_recipe "yum-repoforge"
    chef.add_recipe "yum-epel"
    chef.add_recipe "yum-remi"
  end

  config.vm.provision "shell", inline: <<-SHELL
  sudo yum -y install nano rpm-build rpmdevtools createrepo
  SHELL
end
