# -*- mode: ruby -*-
# vi: set ft=ruby :
# vi: set shiftwidth=2 :
# vi: set tabstop=2 :
# vi: set expandtab :

def is_plugin_installed(name)
  if Vagrant.has_plugin?(name)
    puts "The plugin '#{name}' is installed."
  else
    puts "The plugin '#{name}' is not installed."
    puts "It can be installed executing:"
    puts "vagrant plugin install #{name}"
    exit(1)
  end
end

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  is_plugin_installed("vagrant-berkshelf")
  is_plugin_installed("vagrant-omnibus")

  config.berkshelf.enabled = true
  config.berkshelf.berksfile_path = "./Berksfile"
  config.omnibus.chef_version = "12.6.0"

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.enable :apt
    config.cache.enable :chef
  end

  config.vm.provider "virtualbox" do |vbox, override|
    vbox.customize ["modifyvm", :id, "--memory", "#{1024*4}"]
    vbox.customize ["modifyvm", :id, "--cpus", "4"]

#    override.vm.network :private_network, ip: "192.168.33.10"

    override.vm.network :forwarded_port, guest: 3000, host: 3000
  end

  config.vm.provision :chef_solo do |chef|
    chef.add_recipe "sensu::default"
    chef.add_recipe "sensu::rabbitmq"
    chef.add_recipe "sensu::redis"
    chef.add_recipe "sensu::server_service"
    chef.add_recipe "sensu::api_service"
    chef.add_recipe "uchiwa::default"
    chef.json = {
      :sensu => {
        :version => '0.23.2-1',
        :use_ssl => false
      },
      :uchiwa => {
        :version => '0.14.2-1'
      }
    }
  end

end
