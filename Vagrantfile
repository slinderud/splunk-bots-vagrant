# _*_ mode: ruby _*_
# vi: set ft=ruby :


# Import Config
require 'yaml'
devices = YAML.load_file(File.join(File.dirname(__FILE__),'devices.yml'))




Vagrant.configure(2) do |config|

  #i think this messes up some things
  config.ssh.insert_key = false

  groups = [] # Define array to hold ansible groups
  num_nodes = 0
  populated_ansible_groups = Hash.new # Create hash to contain iterated groups


  # Create array of Ansible Groups from iterated nodes
  devices.each do |node|
    num_nodes = node
    node['groups'].each do |group|
      groups.push(group)
    end
  end
  # Remove duplicate Ansible Groups
  groups = groups.uniq

  # Iterate through array of Ansible Groups
  groups.each do |group|
    group_nodes = []
    # Iterate list of nodes
    devices.each do |node|
      node['groups'].each do |nodegroup|
        # Check if node is a member of iterated group
        if nodegroup == group
          group_nodes.push(node['name'])
        end
      end
      populated_ansible_groups[group] = group_nodes
    end
  end


  # Dynamic Ansible Groups iterated from nodes.yml
  ansible_groups = populated_ansible_groups

  devices.each do |host|
    #check if host is enabled
    if host['enable'] == true
      config.vm.define host['name'] do |device|
        device.vm.box = host['box']
        device.vm.hostname = host['name']
        if host["links"]
          host['links'].each do |link|
            device.vm.network 'private_network', auto_config: false, virtualbox__intnet: "link_seg__#{link['name']}"
          end #link
        end #if links
        if host.key?("hw")
          device.vm.provider "virtualbox" do |vbox|
            if host['hw']['cpulimit'] == true
              vbox.customize [
                "modifyvm", :id,
                "--cpuexecutioncap", "#{host["hw"]["cpulimit"]}"
              ]
            end #cpulimit
            #ram
            if host["hw"]["ram"]
              vbox.memory = host["hw"]["ram"]
            end #ram
            #cpucores
            if host["hw"]["core"]
              vbox.cpus = host["hw"]["core"]
            end #if cpu
            if host["hw"]["disk"]
              device.disksize.size = "#{host["hw"]["disk"]}GB"
            end #if disk
          end #vbox
        end #if hw
        #ip config
        if host.has_key?('ip')
          device.vm.network "private_network", ip: host['ip']
        end #if ip
        #folders config
        if host.has_key?('folders')
          host['folders'].each do |folder|
            mount_opts = folder['type'] == 'nfs' ? ['actimeo=1'] : []
            device.vm.synced_folder folder['map'], folder['to'],
              type: folder['type'],
              mount_options: mount_opts
           end #end folder
        end #endif folders
      end #device
    end #enabled
  end #host
  if !Vagrant::Util::Platform.windows?
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "deploy.yml"
      ansible.groups = ansible_groups
      ansible.verbose = "-vvv"
    end #end ansible
  end #if windows
end #config


