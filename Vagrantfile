# -*- mode: ruby -*-
# vi: set ft=ruby :
# We set the last octet in IPV4 address here
nodes = {
 "controller01" => [1, 110],
 "controller02" => [1, 111],
 "controller03" => [1, 112],
 "compute01" => [1, 113],
 "compute02" => [1, 114],
 "swift01" => [1, 115],
 "swift02" => [1, 116],
 "swift03" => [1, 117],
 # "lb02" => [1, 101],
 "lb01" => [1, 100],
}

Vagrant.configure("2") do |config| 
  # config.vm.box = "ubuntu/xenial64"
  # config.vm.box = "bento/ubuntu-16.04"
  config.vm.box = "geerlingguy/ubuntu1604"
  # config.vm.synced_folder ".", "/vagrant"
  config.vm.usable_port_range= 2800..2900
  nodes.each do |prefix, (count, ip_start)|
    count.times do |i|
      hostname = "%s" % [prefix, (i+1)]
      config.vm.define "#{hostname}" do |box|
        box.vm.hostname = "#{hostname}.cookbook.local"
        box.vm.network :private_network, ip: "10.240.0.#{ip_start+i}", :netmask => "255.255.255.0"
        box.vm.network :private_network, ip: "172.29.236.#{ip_start+i}", :netmask => "255.255.255.0"
        box.vm.network :private_network, ip: "172.29.240.#{ip_start+i}", :netmask => "255.255.255.0"
        box.vm.network :private_network, ip: "172.29.244.#{ip_start+i}", :netmask => "255.255.255.0"
        box.vm.provider :virtualbox do |vbox|
          # Defaults
          vbox.linked_clone = true
          vbox.name = "#{hostname}"
          vbox.customize ["modifyvm", :id, "--groups", "/cookbookv4"]
          vbox.customize ["modifyvm", :id, "--memory", 1024]
          vbox.customize ["modifyvm", :id, "--cpus", 1]
          vbox.customize ["modifyvm", :id, "--pae", "on"]
          vbox.customize ["modifyvm", :id, "--paravirtprovider", "kvm"]
          vbox.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
          vbox.customize ["modifyvm", :id, "--nictype2", "Am79C973"]
          vbox.customize ["modifyvm", :id, "--nictype3", "Am79C973"]
          vbox.customize ["modifyvm", :id, "--nictype4", "Am79C973"]
          vbox.customize ["modifyvm", :id, "--nictype5", "Am79C973"]
          vbox.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
          vbox.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
          vbox.customize ["modifyvm", :id, "--nicpromisc4", "allow-all"]
          vbox.customize ["modifyvm", :id, "--nicpromisc5", "allow-all"]


          # file_to_disk = "/media/vms/cookbookv4/#{hostname}/#{hostname}-sdc.vmdk"
          # unless File.exists?( file_to_disk )
          #   vbox.customize ["createhd", "--filename", file_to_disk, "--size", 80 * 1024]
          # end # unless
          # vbox.customize ["storageattach", :id, "--storagectl", "SCSI", "--port", 2, "--device", 0, "--type", "hdd", "--medium", file_to_disk]
     
          if prefix == "controller01" or prefix == "controller02" or prefix == "controller03"
            vbox.customize ["modifyvm", :id, "--memory", 2048]
            vbox.customize ["modifyvm", :id, "--cpus", 2]
            file_to_disk = "/media/virtual_machines/cookbookv4/#{hostname}/#{hostname}-sdc.vmdk"
            unless File.exists?( file_to_disk )
              vbox.customize ["createhd", "--filename", file_to_disk, "--size", 120 * 1024]
            end # unless
            # vbox.customize ["storageattach", :id, "--storagectl", "SCSI Controller", "--port", 2, "--device", 0, "--type", "hdd", "--medium", file_to_disk]
            # vbox.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 2, "--device", 0, "--type", "hdd", "--medium", file_to_disk] # For Bento Image
            vbox.customize ["storageattach", :id, "--storagectl", "IDE Controller", "--port", 0, "--device", 1, "--type", "hdd", "--medium", file_to_disk] # for geerlingguy
          end # if prefix == controllernn

          if prefix == "compute01" or prefix == "compute02"
            vbox.customize ["modifyvm", :id, "--memory", 4096]
            vbox.customize ["modifyvm", :id, "--cpus", 2]
            file_to_disk = "/media/virtual_machines/cookbookv4/#{hostname}/#{hostname}-sdc.vmdk"
            unless File.exists?( file_to_disk )
              vbox.customize ["createhd", "--filename", file_to_disk, "--size", 150 * 1024]
            end # unless
            # vbox.customize ["storageattach", :id, "--storagectl", "SCSI Controller", "--port", 2, "--device", 0, "--type", "hdd", "--medium", file_to_disk]
            # vbox.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 2, "--device", 0, "--type", "hdd", "--medium", file_to_disk] # For Bento Image
            vbox.customize ["storageattach", :id, "--storagectl", "IDE Controller", "--port", 0, "--device", 1, "--type", "hdd", "--medium", file_to_disk] # for geerlingguy
          end # if prefix == computenn
          
          if prefix == "swift01" or prefix == "swift02" or prefix == "swift03"
            vbox.customize ["modifyvm", :id, "--memory", 1024]
            vbox.customize ["modifyvm", :id, "--cpus", 1]
            file_to_disk = "/media/virtual_machines/cookbookv4/#{hostname}/#{hostname}-sdc.vmdk"
            unless File.exists?( file_to_disk )
              vbox.customize ["createhd", "--filename", file_to_disk, "--size", 40 * 1024]
              # vbox.customize ["createhd", "--filename", file_to_disk1, "--size", 40 * 1024]
              # vbox.customize ["createhd", "--filename", file_to_disk2, "--size", 40 * 1024]
              # vbox.customize ["createhd", "--filename", file_to_disk3, "--size", 40 * 1024]
            end # unless
            vbox.customise ["storagectl", :id, "--name", "SCSI Controller", "--add", "scsi", "--controller", "LSILogic", "--portcount", "4",]
            vbox.customize ["storageattach", :id, "--storagectl", "SCSI Controller", "--port", 0, "--device", 0, "--type", "hdd", "--medium", file_to_disk]
            # vbox.customize ["storageattach", :id, "--storagectl", "SCSI Controller", "--port", 1, "--device", 0, "--type", "hdd", "--medium", file_to_disk1]
            # vbox.customize ["storageattach", :id, "--storagectl", "SCSI Controller", "--port", 2, "--device", 0, "--type", "hdd", "--medium", file_to_disk2]
            # vbox.customize ["storageattach", :id, "--storagectl", "SCSI Controller", "--port", 3, "--device", 0, "--type", "hdd", "--medium", file_to_disk3]
          end # if prefix == swiftnn


        end # box.vm virtualbox

        if prefix == "lb01" # only run once the last VM has been brought on line
          config.vm.provision "ansible" do |ansible|
            # Disable default limit to connect to all the machines
            ansible.limit = "all"
            ansible.playbook = "vagrant_site.yml"
            ansible.groups = {
              "ntp-server" => ["controller01"],
              "ntp-client" => ["controller02", "controller03", "compute01", "compute02", "lb01"],
              "general" => ["controller01", "controller02", "controller03", "compute01", "compute02", "lb01"],
              "data-disk" => ["controller01", "controller02", "controller03", "compute01", "compute02"]
            }
            ansible.host_vars = {
              "controller01" => {"br_mgmt_ip" => "172.29.236.110", "br_vxlan_ip" => "172.29.240.110", "br_storage_ip" => "172.29.244.110"},
              "controller02" => {"br_mgmt_ip" => "172.29.236.111", "br_vxlan_ip" => "172.29.240.111", "br_storage_ip" => "172.29.244.111"},
              "controller03" => {"br_mgmt_ip" => "172.29.236.112", "br_vxlan_ip" => "172.29.240.112", "br_storage_ip" => "172.29.244.112"},
              "compute01" => {"br_mgmt_ip" => "172.29.236.113", "br_vxlan_ip" => "172.29.240.113", "br_storage_ip" => "172.29.244.113"},
              "compute02" => {"br_mgmt_ip" => "172.29.236.114", "br_vxlan_ip" => "172.29.240.114", "br_storage_ip" => "172.29.244.114"},
              "lb01" => {"br_mgmt_ip" => "172.29.236.100","br_vxlan_ip" => "172.29.240.100", "br_storage_ip" => "172.29.244.100"},
              "lb02" => {"br_mgmt_ip" => "172.29.236.101","br_vxlan_ip" => "172.29.240.101", "br_storage_ip" => "172.29.244.101"}
            }
            ansible.extra_vars = {
              ansible_python_interpreter: "/usr/bin/python3"
            }
          end # do ansible
        end # if prefix == lb01
      end # config.vm.define 
    end # count.times
  end # nodes.each
end # Vagrant.configure("2")
