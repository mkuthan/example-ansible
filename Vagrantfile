VAGRANTFILE_API_VERSION = "2"

cluster = {
    "node1" => { :ip => "192.168.50.10",  :cpus => 1, :mem => 1024 },
    "node2" => { :ip => "192.168.50.20",  :cpus => 1, :mem => 1024 },
    "node3" => { :ip => "192.168.50.30",  :cpus => 1, :mem => 1024 },
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :machine
  end

  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |config|

      config.vm.provider :virtualbox do |vb, override|
        override.vm.box = "ubuntu/trusty64"
        override.vm.hostname = hostname
        override.vm.network :private_network, ip: "#{info[:ip]}"

        vb.name = "example-ansible-" + hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus] ]
      end

      if index == cluster.size - 1
        config.vm.provision :ansible do |ansible|
          ansible.playbook = "site.yml"
          ansible.verbose = ""
          ansible.limit = "all"
          ansible.sudo = true
          ansible.extra_vars = {
              default_iface: "eth1"
          }
          ansible.groups = {
            "zookeeper" => ["node1", "node2", "node3"],
            "kafka" => ["node1", "node2", "node3"],
          }
        end
      end

    end
  end
end
