MACHINES = {
  :inetRouter => {
        :vm_name => "inetRouter",
        #:public => {:ip => "10.10.10.1", :adapter => 1},
        :net => [   
                    #ip, adpter, netmask, virtualbox__intnet
                    ["192.168.255.1", 2, "255.255.255.252",  "router-net"], 
                    ["192.168.56.10", 8, "255.255.255.0"],
                ]
  },

  :centralRouter => {
        :vm_name => "centralRouter",
        :net => [
                   ["192.168.255.2",  2, "255.255.255.252",  "router-net"],
                   ["192.168.57.1",    3, "255.255.255.240",  "dir-net"],
                   ["192.168.57.33",   4, "255.255.255.240",  "hw-net"],
                   ["192.168.57.65",   5, "255.255.255.192",  "mgt-net"],
                   ["192.168.255.9",  6, "255.255.255.252",  "office1-central"],
                   ["192.168.255.5",  7, "255.255.255.252",  "office2-central"],
                   ["192.168.56.11",  8, "255.255.255.0"],
                ]
  },

  :centralServer => {
        :vm_name => "centralServer",
        :net => [
                   ["192.168.57.2",    2, "255.255.255.240",  "dir-net"],
                   ["192.168.56.12",  8, "255.255.255.0"],
                ]
  },

  :office1Router => {
        :vm_name => "office1Router",
        :net => [
                   ["192.168.255.10",  2,  "255.255.255.252",  "office1-central"],
                   ["192.168.58.1",     3,  "255.255.255.192",  "dev1-net"],
                   ["192.168.58.65",    4,  "255.255.255.192",  "test1-net"],
                   ["192.168.58.129",   5,  "255.255.255.192",  "managers-net"],
                   ["192.168.58.193",   6,  "255.255.255.192",  "office1-net"],
                   ["192.168.56.20",   8,  "255.255.255.0"],
                ]
  },

  :office1Server => {
        :vm_name => "office1Server",
        :net => [
                   ["192.168.58.130",  2,  "255.255.255.192",  "managers-net"],
                   ["192.168.56.21",  8,  "255.255.255.0"],
                ]
  },

  :office2Router => {
       :vm_name => "office2Router",
       :net => [
                   ["192.168.255.6",  2,  "255.255.255.252",  "office2-central"],
                   ["192.168.59.1",    3,  "255.255.255.128",  "dev2-net"],
                   ["192.168.59.129",  4,  "255.255.255.192",  "test2-net"],
                   ["192.168.59.193",  5,  "255.255.255.192",  "office2-net"],
                   ["192.168.56.30",  8,  "255.255.255.0"],
               ]
  },

  :office2Server => {
       :vm_name => "office2Server",
       :net => [
                  ["192.168.59.2",    2,  "255.255.255.128",  "dev2-net"],
                  ["192.168.56.31",  8,  "255.255.255.0"],
               ]
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      box.vm.box = "generic/debian12"
      box.vm.box_version = "4.3.12"
      box.vm.host_name = boxconfig[:vm_name]
      
      box.vm.provider "virtualbox" do |v|
        v.memory = 512
        v.cpus = 1
       end

      boxconfig[:net].each do |ipconf|
        box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], virtualbox__intnet: ipconf[3])
      end

      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end

      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
      SHELL
      if boxconfig[:vm_name] == "office2Server"
        box.vm.provision "ansible" do |ansible|
          ansible.playbook = "ansible/playbook.yml"
          ansible.inventory_path = "ansible/hosts"
          ansible.host_key_checking = "false"
          ansible.limit = "all"
        end
      end

    end
  end
end


