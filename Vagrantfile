# Local test lab: two Ubuntu servers matching inventory/hosts.ini
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  { "web1" => "192.168.56.11", "web2" => "192.168.56.12" }.each do |name, ip|
    config.vm.define name do |node|
      node.vm.hostname = name
      node.vm.network "private_network", ip: ip
      node.vm.provider "virtualbox" do |vb|
        vb.memory = 512
        vb.cpus = 1
      end
    end
  end
end
