# -*- mode: ruby -*-
# vi: set ft=ruby :
ENV["VAGRANT_DETECTED_OS"] = ENV["VAGRANT_DETECTED_OS"].to_s + " cygwin"
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  config.vm.host_name = "test.server"
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
  config.ssh.forward_agent = true
  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  if config.vm.provider == 'aws'
    config.vm.box = "dummy"
  else
    config.vm.box = "ubuntu/trusty64"
  end
  
  config.vm.provider :aws do |aws, override|
    aws.access_key_id = "YOUR KEY"
    aws.secret_access_key = "YOUR SECRET KEY"
    aws.session_token = "SESSION TOKEN"
    aws.keypair_name = "KEYPAIR NAME"

    aws.ami = "ami-7747d01e"

    override.ssh.username = "ubuntu"
    override.ssh.private_key_path = "PATH TO YOUR PRIVATE KEY"
  end
  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"
  config.vm.network "public_network",
    bridge: ENV["VAGRANT_ADAPTER_CHOICE"]
    #,ip: "10.0.0.14",

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder ".", "/vagrant",
    type: "rsync", rsync__exclude: ".git/"

  config.vm.synced_folder "puppet/hieradata", "/tmp/vagrant-puppet/hieradata",
    type: "rsync"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provider :virtualbox do |v|
    memory = 4096
    cpu_count = 2
    v.customize ["modifyvm", :id, "--memory", memory]
    # Assign additional cores to the guest OS.
    v.customize ["modifyvm", :id, "--cpus", cpu_count]
    v.customize ["modifyvm", :id, "--ioapic", "on"]

    # This setting makes it so that network access from inside the vagrant guest
    # is able to resolve DNS using the hosts VPN connection.
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end
  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL

  # r10k configuration
  config.r10k.puppet_dir = 'puppet'
  config.r10k.puppetfile_path = 'puppet/Puppetfile'

  config.vm.provision :puppet do |puppet|
    if ENV.key?('PUPPET_OPTS')
      puppet.options = ENV['PUPPET_OPTS'].split(' ')
    end
    puppet.module_path = 'puppet/modules'
    puppet.manifests_path = 'puppet/manifests'
    puppet.manifest_file = 'default.pp'
    puppet.hiera_config_path = 'hiera.yaml'
    puppet.working_directory = '/tmp/vagrant-puppet'
    puppet.synced_folder_type = 'rsync'
    puppet.synced_folder_args = ['-a', '--delete', '--exclude=fixtures']
  end

  config.vm.provision :shell, inline: 'echo "DIR 01;36" > /home/vagrant/.dircolors'
end
