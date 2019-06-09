# -*- mode: ruby -*-
# vi: set ft=ruby :

def set_resources(vm, options)
  vm.provider :virtualbox do |v|
    v.cpus   = options[:cpu]
    v.memory = options[:ram]
  end

  # VMware Fusion
  vm.provider :vmware_fusion do |v|
    v.vmx["numvcpus"] = options[:cpu]
    v.vmx["memsize"]  = options[:ram]
  end
end

Vagrant.configure(2) do |config|
  config.ssh.insert_key = false
  config.ssh.forward_agent = !!ENV['VAGRANT_SSH_FORWARD']

  # make sure we run correct version
  centos_version = ENV['CENTOS_VERSION'] || '7'
  if centos_version !~ /^(6|7|8)$/
    puts 'Variable CENTOS_VERSION was not set correctly, so reverting to CentOS 7'
    centos_version = 7
  end

  # spin 3 VMs
  (1..3).each do |index|
    config.vm.define :"rhcs0#{index}" do |node|
      set_resources(node.vm, cpu:1, ram: 4096)
      node.vm.box      = "centos/#{centos_version}"
      node.vm.hostname = "rhcs0#{index}.example.lan"
      node.vm.network :forwarded_port, guest: 22, host: "922#{index}", id: "ssh"
      node.vm.network :private_network, ip: "10.10.11.10#{index}", :netmask => "255.255.255.0", virtualbox__intnet: "rhcs"
      # install puppet master
      node.vm.provision 'shell', inline: 'sudo /vagrant/scripts/prepare_vagrant'
    end
  end

end
