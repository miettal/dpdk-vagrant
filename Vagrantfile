# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"

  # config.vm.box_check_update = false
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  # config.vm.network "private_network", ip: "192.168.33.10"
  #config.vm.network "public_network"
  # config.vm.synced_folder "../data", "/vagrant_data"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "2048"
    vb.customize ["modifyvm", :id, "--nic2","natnetwork"]
  end


  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y libpcap-dev
#    apt-get upgrade
#    apt-get dist-upgrade
    
    rm -rf *
    wget -q http://fast.dpdk.org/rel/dpdk-17.05.tar.gz
    ln -s dpdk-17.05.tar.gz dpdk.tar.gz
    tar xf dpdk.tar.gz
    ln -s dpdk-17.05 dpdk
    cd dpdk
    make config T=x86_64-native-linuxapp-gcc
    sed -ri 's,(PMD_PCAP=).*,\\1y,' build/.config
    make

#    ./build/app/testpmd -c3 -n3 --vdev=net_pcap1,iface=eth1 - -- -i --nb-cores=1 --nb-ports=1 --total-num-mbufs=2048
#    ./usertools/dpdk-devbind.py  --status

    cd examples/helloworld/
    export RTE_SDK=/home/vagrant/dpdk
    export RTE_TARGET=build
    make
  SHELL

  config.vm.provision "shell", run: "always", inline: <<-SHELL
    mkdir -p /mnt/huge
    mount -t hugetlbfs nodev /mnt/huge
    echo 64 > /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages
    ip link set up dev eth1
  SHELL
end
