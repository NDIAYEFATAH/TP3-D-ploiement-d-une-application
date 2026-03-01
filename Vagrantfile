Vagrant.configure("2") do |config|
  #  server-back
  config.vm.define "server-back" do |serverback|
    serverback.vm.box = "ubuntu/jammy64"
    serverback.vm.box_check_update = false
    serverback.vm.hostname = "server-back"
    serverback.vm.network "forwarded_port", guest: 8082, host: 8083
    serverback.vm.network "private_network", ip: "192.168.33.11"

    serverback.vm.synced_folder ".", "/vagrant"

    serverback.vm.provider "virtualbox" do |vb|
      vb.name   = "server-back"
      vb.gui    = false
      vb.memory = "2048"
      vb.cpus   = "2"
    end

    #serverback.vm.provision "shell", path: "scripts/provision-back.sh"
  end


  #  server-dba 
  config.vm.define "server-dba" do |serverdba|
    serverdba.vm.box = "ubuntu/jammy64"
    serverdba.vm.box_check_update = false
    serverdba.vm.hostname = "server-dba"
    serverdba.vm.network "private_network", ip: "192.168.33.12"

    serverdba.vm.synced_folder ".", "/vagrant"

    serverdba.vm.provider "virtualbox" do |vb|
      vb.name   = "server-dba"
      vb.gui    = false
      vb.memory = "2048"
      vb.cpus   = "2"
    end

    #serverdba.vm.provision "shell", path: "scripts/provision-dba.sh"
  end


  #  server-front
  config.vm.define "server-front" do |serverfront|
    serverfront.vm.box = "ubuntu/jammy64"
    serverfront.vm.box_check_update = false
    serverfront.vm.hostname = "server-front"
    serverfront.vm.network "forwarded_port", guest: 80, host: 8080
    serverfront.vm.network "private_network", ip: "192.168.33.13"

    serverfront.vm.synced_folder ".", "/vagrant"

    serverfront.vm.provider "virtualbox" do |vb|
      vb.name   = "server-front"
      vb.gui    = false
      vb.memory = "2048"
      vb.cpus   = "2"
    end

    #serverfront.vm.provision "shell", path: "scripts/provision-front.sh"
  end

end
