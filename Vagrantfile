
esxi_box_url = "./vmware_esxi60.box"

nodes = [
#  { :hostname => 'controlcenter', :box => 'gosddc/trusty64'  },
  { :hostname => 'esx-01a', :box => 'esxi60', :box_url => esxi_box_url },
#  { :hostname => 'esx-02a', :box => 'esxi60', :box_url => esxi_box_url },
#  { :hostname => 'esx-03a', :box => 'esxi60', :box_url => esxi_box_url },
]

Vagrant.configure("2") do |config|
  if Vagrant.has_plugin?("vagrant-vcloud")
    config.vm.provider :vcloud do |vcloud|
      vcloud.vapp_prefix = "esxi"
      vcloud.ip_subnet = "172.16.32.1/255.255.255.0" # our test subnet with fixed IP adresses for everyone
      vcloud.ip_dns = ["10.100.20.2", "8.8.8.8"]  # SEAL DNS + Google
    end
    if Vagrant.has_plugin?("vagrant-proxyconf")
      config.vm.provider :vcloud do |cloud, override|
        override.proxy.enabled = false
      end
    end
    config.vm.provider :vcloud do |cloud, override|
      override.vm.usable_port_range = 2200..2999
    end
  end

  # Go through nodes and configure each of them.j
  nodes.each do |node|
    config.vm.define node[:hostname] do |node_config|

    if node[:hostname].include? 'esx-'
      node_config.ssh.username = 'root'
      node_config.ssh.shell = 'sh'
      node_config.ssh.insert_key = false
      node_config.vm.synced_folder ".", "/vagrant", nfs: true
    end

    ["vmware_fusion", "vmware_workstation"].each do |provider|
      node_config.vm.provider provider do |v, override|
        v.gui = true
        v.vmx["memsize"] = "4096"
        v.vmx["numvcpus"] = "2"
        # v.vmx["vhv.enable"] = "TRUE"
      end
    end

    node_config.vm.box = node[:box]
    node_config.vm.hostname = node[:hostname]
    node_config.vm.box_url = node[:box_url]
    node_config.vm.provision "shell", privileged: false, path: "provision.sh"

    node_config.vm.provider :vcloud do |v|
      # feature 0.4.0
      v.cpus = 4
      v.memory = 4096
    end
  end
end
end
