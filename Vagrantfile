# -*- mode: ruby -*-
# vi: set ft=ruby :

def add_disk(config, disk_path)
  if(!File.exists?(disk_path))
    config.vm.provider "virtualbox" do |v|
      v.customize ['createhd', '--filename', disk_path, '--size', 20 * 1024]
    end
  end
  config.vm.provider "virtualbox" do |v|
    v.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_path]
  end
end


distro = "hashicorp/bionic64"


Vagrant.configure("2") do |config|
  config.vm.provision "shell" do |s|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    s.inline = <<-SHELL
      echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
    SHELL
  end

  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
  end

  config.vm.define "k3s-server1" do |master|
    master.vm.box = distro
    master.vm.hostname = "k3s-server1"
    master.vm.network "private_network", ip: "192.168.56.2"
    add_disk master, '/data/vagrant/disks/k3s-server1.vdi'
  end

  config.vm.define "k3s-server2" do |slave|
    slave.vm.box = distro
    slave.vm.hostname = "k3s-server2"
    slave.vm.network "private_network", ip: "192.168.56.3"
    add_disk slave, '/data/vagrant/disks/k3s-server2.vdi'
  end

  config.vm.define "k3s-server3" do |slave|
    slave.vm.box = distro
    slave.vm.hostname = "k3s-server3"
    slave.vm.network "private_network", ip: "192.168.56.4"
    add_disk slave, '/data/vagrant/disks/k3s-server3.vdi'
  end

  config.vm.define "k3s-agent1" do |slave|
    slave.vm.box = distro
    slave.vm.hostname = "k3s-agent1"
    slave.vm.network "private_network", ip: "192.168.56.5"
    add_disk slave, '/data/vagrant/disks/k3s-agent1.vdi'
  end

end
