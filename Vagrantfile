# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|

  config.vm.define "acs" do |acs|
    acs.vm.box = "ubuntu/xenial64"
    acs.vm.hostname = "acs"
    acs.vm.network "private_network", ip: "192.168.33.10"
  end

  config.vm.define "web" do |web|
    web.vm.box="nrel/CentOS-6.5-x86_64"
    web.vm.hostname = "web"
    web.vm.network "private_network", ip: "192.168.33.20"
    web.vm.network "forwarded_port", guest: 80, host: 8080
  end

#  config.vm.define "db" do |db|
#    db.vm.box = "nrel/CentOS-6.5-x86_64"
#    db.vm.hostname = "db"
#    db.vm.network "private_network", ip: "192.168.33.30"
#  end
 
# Does not pick up IP-address: 
#  config.vm.define "winnano" do |win|
#    win.vm.box = "mwrock/WindowsNano"
#	win.vm.hostname = "winnano"
#	win.vm.network "private_network", ip: "192.168.33.40"
#  end 
  
  config.vm.define "win2016" do |win2016|
    win2016.vm.box = "StefanScherer/windows_2016_docker"
	win2016.vm.hostname = "win2016"
	win2016.vm.network "private_network", ip: "192.168.33.50"
	win2016.vm.communicator = "winrm"
	win2016.winrm.username = "vagrant"
	win2016.winrm.password = "vagrant"
	win2016.vm.provider "virtualbox" do |vb|
	  vb.memory = 2048
	  vb.cpus = 2
	end
  end

# Does not pick up IP-address: 
#  config.vm.define "win2012" do |win2012|
#    win2012.vm.box = "jptoto/Windows2012R2"
#	win2012.vm.hostname = "win2012"
#	win2012.vm.network "private_network", ip: "192.168.33.60"
#	win2012.vm.communicator = "winrm"
#	win2012.winrm.username = "vagrant"
#	win2012.winrm.password = "vagrant"
#	win2012.vm.provider "virtualbox" do |vb|
#	  vb.memory = 2048
#	  vb.cpus = 2
#	end
#  end
end
