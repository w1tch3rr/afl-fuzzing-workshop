# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "private_network", ip: "192.168.33.100"
  # config.vm.network "public_network"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: <<-SHELL
    echo "nameserver 8.8.8.8" | tee /etc/resolv.conf > /dev/null
    apt-get update
    DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential wget gdb clang cmake openssl libssl-dev
    mkdir /tmp/afl-install && cd /tmp/afl-install
    wget -O afl-latest.tgz https://github.com/google/AFL/archive/v2.56b.tar.gz
    tar xzvf afl-latest.tgz
    rm -rf afl-latest.tgz
    cd AFL-*
    make
    cd llvm_mode
    LLVM_CONFIG=/usr/bin/llvm-config-6.0 make
    cd .. && make install

    cd /tmp
    wget https://gitlab.com/akihe/radamsa/uploads/a2228910d0d3c68d19c09cee3943d7e5/radamsa-0.6.c.gz
    gzip -d radamsa-0.6.c.gz
    sudo gcc -o /usr/local/bin/radamsa radamsa-0.6.c

    echo 'kernel.core_pattern = core' | sudo tee -a /etc/sysctl.conf
    sysctl -p
  SHELL
end
