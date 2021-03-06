# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/ubuntu-19.10"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.

  forwarded_ports = [3000, 4000, 5000, 8080]
  forwarded_ports.each do |p|
    config.vm.network :forwarded_port, guest: p, host: p
  end

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", type: 'dhcp'# ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "../projects", "/projects"

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

  config.ssh.forward_agent = true

  # Config from here: https://stefanwrobel.com/how-to-make-vagrant-performance-not-suck
  config.vm.provider "virtualbox" do |v|
    host = RbConfig::CONFIG['host_os']

    # Give VM 1/4 system memory & access to all cpu cores on the host
    if host =~ /darwin/
      cpus = `sysctl -n hw.ncpu`.to_i
      # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
    elsif host =~ /linux/
      cpus = `nproc`.to_i
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
    else # sorry Windows folks, I can't help you
      cpus = 4
      mem = 1024 * 4
    end

    v.customize ["modifyvm", :id, "--memory", mem]
    v.customize ["modifyvm", :id, "--cpus", cpus]
    v.customize ["modifyvm", :id, "--acpi", "off"]

    v.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 10000 ]

    v.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--cableconnected1", "on"]


    v.name = "devbox"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision 'ansible_local' do |ansible|
    ansible.playbook = 'dev.yml'
    ansible.provisioning_path = '/vagrant/playbooks'
  end
  #
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y build-essential zlib1g-dev libssl-dev libreadline6-dev libyaml-dev libpq-dev
  #   sudo apt-get install -y postgresql
  #   sudo apt-get remove -y ruby
  #   wget -O- https://toolbelt.heroku.com/install-ubuntu.sh | sh
  #
  #   sudo -u postgres psql -c "CREATE USER vagrant WITH CREATEDB PASSWORD 'vagrant';"
  #   sudo -u postgres psql -c "CREATE USER carwow WITH CREATEDB PASSWORD 'carwow';"
  #
  #   sudo apt-get install -y zsh
  #   sudo chsh -s $(which zsh) vagrant
  #   wget --no-check-certificate http://install.ohmyz.sh -O - | sh
  #
  #   cd /tmp
  #   wget http://ftp.ruby-lang.org/pub/ruby/2.2/ruby-2.2.3.tar.gz
  #   tar -xvzf ruby-2.2.3.tar.gz
  #   cd ruby-2.2.3/
  #   ./configure --prefix=/home/vagrant
  #   make
  #   sudo make install
  #
  #   wget -O- https://toolbelt.heroku.com/install-ubuntu.sh | sh
  #
  #   wget https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb && sudo dpkg -i erlang-solutions_1.0_all.deb
  #   sudo apt-get update
  #   sudo apt-get -y install esl-erlang elixir
  #
  #   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
  #   sudo apt-get install -y nodejs
  # SHELL
end
