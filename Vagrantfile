# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|

### Prieks ssh savienojuma var izmantot ssh agentu

#def install_ssh_key()
#puts "Adding ssh key to the ssh agent"
#puts "ssh-add #{Vagrant.source_root}/keys/vagrant"
#system "ssh-add #{Vagrant.source_root}/keys/vagrant"
#end
#install_ssh_key

# Noradam uz yaml aktivizaciju
require 'yaml'

# Nolasa yaml file
vagrant_root = File.dirname(__FILE__)
hosts = YAML.load_file(vagrant_root + '/topology.yml')

# Skripts prieks katra hosta
  hosts.each do |host|
    config.vm.define host["name"] do |srv|
      srv.vm.box = host["box"]

      if host.key?("forwarded_ports")
        host["forwarded_ports"].each do |port|
          srv.vm.network :forwarded_port, guest: port["guest"], host: port["host"], id: port["name"]
        end
      end

      if host.key?("links")
        host["links"].each do |link|
          ipaddr = "169.254.1.11"
          srv.vm.network "private_network", virtualbox__intnet: link["name"], ip: ipaddr, auto_config: false
        end
      end

# Automatiski nokonfigure hostname
      if /vEOS-lab-4.20.1F/.match(host['box'])
        $script = <<-SCRIPT
          FastCli -p 15 -c "configure
          hostname $1"
        SCRIPT

        srv.vm.provision "shell" do |s|
          s.inline = $script
          s.args = host["name"]
        end
      end
    end
  end
end
