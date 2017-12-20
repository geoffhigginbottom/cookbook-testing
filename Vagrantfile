# -*- mode: ruby -*-
# vi: set ft=ruby :
# We set the last octet in IPV4 address here
nodes = {
 'controller01' => [1, 110],
 'controller02' => [1, 111],
 'controller03' => [1, 112],
 'compute01' => [1, 113],
 'compute02' => [1, 114],
 'lb01' => [1, 100],
 #'lb02' => [1, 101],
}

# Create the folder structure now so we can also create the extra volumes.
# Whilst Vagrant will create the first folder based on the 'groups' setting below
# using this path to store the extra volumes causes an initial failure if the 
# following two folders do not exist when vagrant.configure runs 
# dir = "#{ENV['HOME']}/VirtualBox VMs/cookbookv4"
# unless File.directory?( dir )
#     Dir.mkdir dir
# end # unless

# dir = "#{ENV['HOME']}/VirtualBox VMs/cookbookv4/additional-disks"
# unless File.directory?( dir )
#     Dir.mkdir dir
# end # unless

Vagrant.configure("2") do |config| 
  config.vm.box = "ubuntu/xenial64"
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

          # dir = "#{ENV['HOME']}/VirtualBox VMs/cookbookv4/additional-disks"
          # unless File.directory?( dir )
          #     Dir.mkdir dir
          # end # unless
          # file_to_disk = "#{dir}/#{hostname}-sdb.vmdk"
          # unless File.exists?( file_to_disk )
          #   vbox.customize ['createhd', '--filename', file_to_disk, '--size', 80 * 1024]
          # end # unless
          # vbox.customize ['storageattach', :id, '--storagectl', 'SCSI', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', file_to_disk]
     
          if prefix == "controller01" or prefix == "controller02" or prefix == "controller03"
            vbox.customize ["modifyvm", :id, "--memory", 4096]
            vbox.customize ["modifyvm", :id, "--cpus", 2]
          end # if prefix == controllernn

          if prefix == "compute01" or prefix == "compute02"
            vbox.customize ["modifyvm", :id, "--memory", 6144]
            vbox.customize ["modifyvm", :id, "--cpus", 2]
          end # if prefix == computenn
      
        end # box.vm virtualbox

        if prefix == "lb02" # only run once the compute02 VM has been brought on line
          config.vm.provision "ansible" do |ansible|
            # Disable default limit to connect to all the machines
            ansible.limit = "all"
            ansible.playbook = "vagrant_site.yml"
            ansible.groups = {
              "ntp-server" => ["controller01"],
              "ntp-client" => ["controller02", "controller03", "compute01", "compute02", "lb01", "lb02"],
              "general" => ["controller01", "controller02", "controller03", "compute01", "compute02", "lb01", "lb02"]
            }
            ansible.host_vars = {
              "controller01" => {"br_mgmt_ip" => "172.29.236.110", "br_vxlan_ip" => "172.29.240.110", "br_storage_ip" => "172.29.244.110"},
              "controller02" => {"br_mgmt_ip" => "172.29.236.111", "br_vxlan_ip" => "172.29.240.111", "br_storage_ip" => "172.29.244.111"},
              "controller03" => {"br_mgmt_ip" => "172.29.236.112", "br_vxlan_ip" => "172.29.240.112", "br_storage_ip" => "172.29.244.112"},
              "compute01" => {"br_mgmt_ip" => "172.29.236.113", "br_vxlan_ip" => "172.29.240.113", "br_storage_ip" => "172.29.244.113"},
              "compute02" => {"br_mgmt_ip" => "172.29.236.114", "br_vxlan_ip" => "172.29.240.114", "br_storage_ip" => "172.29.244.114"},
              "lb01" => {"br_mgmt_ip" => "172.29.236.100"},
              "lb02" => {"br_mgmt_ip" => "172.29.236.101"}
            }
            ansible.extra_vars = {
              ansible_python_interpreter: "/usr/bin/python3"
            }
          end # do ansible
        end # if prefix == lb02
      end # config.vm.define 
    end # count.times
  end # nodes.each
end # Vagrant.configure("2")
