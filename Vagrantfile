# -*- mode: ruby -*-
# vi: set ft=ruby :
VM_BOX  = 'ubuntu/xenial64'
Vagrant.configure(2) do |config|
 config.vm.define "worker0" do |worker0|
    worker0.vm.box = VM_BOX
    worker0.vm.hostname = "worker0"
    worker0.vm.network :private_network, ip: "192.168.0.2"
    worker0.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
    end
  end
 config.vm.define "lb0" do |lb0|
    lb0.vm.box = VM_BOX
    lb0.vm.hostname = "lb0"
    lb0.vm.network :private_network, ip: "192.168.0.3"
    lb0.vm.provider "virtualbox" do |vb|
      vb.memory = 512
    end
  end
  config.vm.define "master0" do |master0|
    master0.vm.box = VM_BOX
    master0.vm.hostname = "master0"
    master0.vm.network :private_network, ip: "192.168.0.4"
    master0.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
    end
  end
 config.vm.define "master1" do |master1|
    master1.vm.box = VM_BOX
    master1.vm.hostname = "master1"
    master1.vm.network :private_network, ip: "192.168.0.5"
    master1.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
    end
  end 
  config.vm.define "master2" do |master2|
    master2.vm.box = VM_BOX
    master2.vm.hostname = "master2"
    master2.vm.network :private_network, ip: "192.168.0.6"
    master2.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
    end
  end 
  config.vm.define "etc0" do |etc0|
    etc0.vm.box = VM_BOX
    etc0.vm.hostname = "etc0"
    etc0.vm.network :private_network, ip: "192.168.0.7"
    etc0.vm.provider "virtualbox" do |vb|
      vb.memory = 512
    end
  end
 config.vm.define "etc1" do |etc1|
    etc1.vm.box = VM_BOX
    etc1.vm.hostname = "etc1"
    etc1.vm.network :private_network, ip: "192.168.0.8"
    etc1.vm.provider "virtualbox" do |vb|
      vb.memory = 512
    end
  end
 config.vm.define "etc2" do |etc2|
    etc2.vm.box = VM_BOX
    etc2.vm.hostname = "etc2"
    etc2.vm.network :private_network, ip: "192.168.0.9"
    etc2.vm.provider "virtualbox" do |vb|
      vb.memory = 512
    end
  end
  config.vm.define "build1" do |build1|
    build1.vm.box = VM_BOX
    build1.vm.hostname = "build1"
    build1.vm.network :private_network, ip: "192.168.0.10"
    build1.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
    end
  end
end
