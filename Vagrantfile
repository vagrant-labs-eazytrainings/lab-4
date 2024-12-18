RAM_MACHINE= "2048"
CPU_MACHINE= 2
OS_BOX= "centos/7"
VERSION_BOX= "2004.01"

Vagrant.configure("2") do |config|
  config.vm.box = OS_BOX
  config.vm.box_version = VERSION_BOX
  config.vm.network "private_network", ip: "10.0.0.10"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = RAM_MACHINE
    vb.cpus = CPU_MACHINE
  end
end
