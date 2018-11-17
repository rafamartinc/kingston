Vagrant.configure("2") do |config|

  if Gem::Version.new(Vagrant::VERSION) >= Gem::Version.new("2.1.3")
    config.vagrant.plugins = ["vagrant-proxyconf", "vagrant-vbguest"]

  elsif !Vagrant.has_plugin?("vagrant-proxyconf")
    puts "The Vagrant ProxyConf plugin must be installed prior to building this VM"
    system("vagrant plugin install vagrant-proxyconf")
    puts "Dependencies installed, please try the command again."
    exit

  elsif !Vagrant.has_plugin?("vagrant-vbguest")
    puts "The Vagrant VBGuest plugin must be installed prior to building this VM"
    system("vagrant plugin install vagrant-vbguest")
    puts "Dependencies installed, please try the command again."
    exit

  elsif !Vagrant.has_plugin?("vagrant-docker-compose")
    puts "The Vagrant Docker Compose plugin must be installed prior to building this VM"
    system("vagrant plugin install vagrant-docker-compose")
    puts "Dependencies installed, please try the command again."
    exit

  end

  #config.vm.box = "bento/centos-7.1"
  #config.vm.box = "centos/7"
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = "dockerhostvm"
  config.vm.define "dockerhostvm"

  config.vm.provider "virtualbox" do |v|
	  v.memory = 6144
	  v.cpus = 4
  end

  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"

  # Mapeados de puertos.
  config.vm.network "public_network"
  config.vm.network(:forwarded_port, guest: 80, host: 80)
  config.vm.network(:forwarded_port, guest: 443, host: 443)
  config.vm.network(:forwarded_port, guest: 22, host: 22)
  
  # Comentar en caso de no querer activar el proxy
  #config.proxy.http = "http://10.113.55.100:8080"
  
  # Instalación de docker y docker-compose.
  # Si ocurre algun error ejecutar "vagrant provision" manualmente despues de "vagrant up".
  config.vm.provision :docker
  config.vm.provision "shell", inline: "ps aux | grep 'sshd:' | awk '{print $2}' | xargs kill"

  config.vm.provision "shell", inline: <<-EOC
    test -e /usr/local/bin/docker-compose || \\
    curl -sSL https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` \\
      | sudo tee /usr/local/bin/docker-compose > /dev/null
    sudo chmod +x /usr/local/bin/docker-compose
    test -e /etc/bash_completion.d/docker-compose || \\
    curl -sSL https://raw.githubusercontent.com/docker/compose/$(docker-compose --version | awk 'NR==1{print $NF}')/contrib/completion/bash/docker-compose \\
      | sudo tee /etc/bash_completion.d/docker-compose > /dev/null
  EOC

  #config.vm.provision "shell" do |sh|
  #  sh.inline = 'docker-compose -f /vagrant/docker-compose.yml up -d --build'
  #end
  # Whenever you change the docker-compose file, you need to run: vagrant reload to redefine the box

 end