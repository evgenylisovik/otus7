# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "centos/7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

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

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL  
  yum install -y \
  redhat-lsb-core \wget \
  rpmdevtools \
  rpm-build \
  createrepo \
  yum-utils \
  gcc
  yum install -y libuuid-devel openldap-devel autoconf \
  lua-devel \
  libxml2-devel \
  openssl-devel pcre-devel zlib-devel libtool doxygen 
  useradd builder -m
  runuser -l vagrant -c  'wget https://dlcdn.apache.org/httpd/httpd-2.4.58.tar.bz2 --no-check-certificate'
  runuser -l vagrant -c  'wget https://dlcdn.apache.org//apr/apr-1.7.4.tar.bz2 --no-check-certificate'
  runuser -l vagrant -c  'rpmbuild -ts apr-1.7.4.tar.bz2'
  runuser -l vagrant -c  'rpm -i /home/vagrant/rpmbuild/SRPMS/apr-1.7.4-1.src.rpm'
  runuser -l vagrant -c  'rpmbuild -bb /home/vagrant/rpmbuild/SPECS/apr.spec'
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/apr-1.7.4-1.x86_64.rpm
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/apr-devel-1.7.4-1.x86_64.rpm
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/apr-debuginfo-1.7.4-1.x86_64.rpm 
  yum install -y apr-util-devel
  rm -r /home/vagrant/rpmbuild/ --force
  runuser -l vagrant -c  'rpmbuild -ts httpd-2.4.58.tar.bz2'
  runuser -l vagrant -c  'rpm -i /home/vagrant/rpmbuild/SRPMS/httpd-2.4.58-1.src.rpm'
  runuser -l vagrant -c  'rpmbuild -bb /home/vagrant/rpmbuild/SPECS/httpd.spec'
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/httpd-2.4.58-1.x86_64.rpm
  mkdir /usr/share/httpd
  mkdir /usr/share/httpd/html
  mkdir /usr/share/httpd/html/repo
  mkdir /usr/share/httpd/html/repo/paket
  cp rpmbuild/RPMS/x86_64/httpd-2.4.58-1.x86_64.rpm /usr/share/httpd/html/repo/paket
  createrepo /usr/share/httpd/html/repo
  ln -s /usr/share/httpd/html/repo /var/www/html
  systemctl start httpd  
  SHELL
end
