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

  # disable firewall
  config.vm.provision "shell", inline: "ufw disable"

  # requirement for longhorn
  config.vm.provision "shell", inline: "apt-get install open-iscsi"

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
  end

  config.vm.define "k3s-server1" do |v|
    v.vm.box = distro
    v.vm.hostname = "k3s-server1"
    v.vm.network "private_network", ip: "192.168.56.2"
    add_disk v, '/data/vagrant/disks/k3s-server1.vdi'
    v.vm.provision "file", source: "./k3s-config/server1.yaml", destination: "$HOME/k3s-config.yaml"
    v.vm.provision "shell", inline: "mkdir -p /etc/rancher/k3s && mv /home/vagrant/k3s-config.yaml /etc/rancher/k3s/config.yaml"
    v.vm.provision "shell", inline: "curl -sfL https://get.k3s.io | sh -"
    v.vm.provision "file", source: "./longhorn.yaml", destination: "$HOME/longhorn.yaml"
  end

  config.vm.define "k3s-server2" do |v|
    v.vm.box = distro
    v.vm.hostname = "k3s-server2"
    v.vm.network "private_network", ip: "192.168.56.3"
    add_disk v, '/data/vagrant/disks/k3s-server2.vdi'
    v.vm.provision "file", source: "./k3s-config/server2.yaml", destination: "$HOME/k3s-config.yaml"
    v.vm.provision "shell", inline: "mkdir -p /etc/rancher/k3s && mv /home/vagrant/k3s-config.yaml /etc/rancher/k3s/config.yaml"
    v.vm.provision "shell", inline: "curl -sfL https://get.k3s.io | sh -"
  end

  config.vm.define "k3s-server3" do |v|
    v.vm.box = distro
    v.vm.hostname = "k3s-server3"
    v.vm.network "private_network", ip: "192.168.56.4"
    add_disk v, '/data/vagrant/disks/k3s-server3.vdi'
    v.vm.provision "file", source: "./k3s-config/server3.yaml", destination: "$HOME/k3s-config.yaml"
    v.vm.provision "shell", inline: "mkdir -p /etc/rancher/k3s && mv /home/vagrant/k3s-config.yaml /etc/rancher/k3s/config.yaml"
    v.vm.provision "shell", inline: "curl -sfL https://get.k3s.io | sh -"
  end

  config.vm.define "k3s-agent1" do |v|
    v.vm.box = distro
    v.vm.hostname = "k3s-agent1"
    v.vm.network "private_network", ip: "192.168.56.5"
    add_disk v, '/data/vagrant/disks/k3s-agent1.vdi'
    v.vm.provision "file", source: "./k3s-config/agent1.yaml", destination: "$HOME/k3s-config.yaml"
    v.vm.provision "shell", inline: "mkdir -p /etc/rancher/k3s && mv /home/vagrant/k3s-config.yaml /etc/rancher/k3s/config.yaml"
    v.vm.provision "shell", inline: "curl -sfL https://get.k3s.io | sh -s agent -"
  end



end
