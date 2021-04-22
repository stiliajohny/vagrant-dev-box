# -*- mode: ruby -*-
# vi: set ft=ruby :require 'rbconfig'

require 'yaml'

# GLOBAL VARIABLES TO BE USED
# Change if you only know what you are doing :)
VAGRANTFILE_API_VERSION = "2"
VM_NAME = "Archlinux-Vagrant"
VM_BOX_URL = "archlinux/archlinux"
VM_NAME_DOMAIN = ".local"
VM_CHECK_UPDATE = true
VM_CHECK_UPDATE = true
VM_MEMORY = 8192
VM_CPUS = 8
CPU_DIVIDER = 4 #provision CPU AND MEM as a portion of the Host Resources
MEM_DIVIDER = 4 #provision CPU AND MEM as a portion of the Host Resources
VM_IP = "192.168.56.151"
REPOS_PATH = "~/Documents/GitHub/"

MOUNT_PATHS={
    "sync_folder" =>{:local => "./sync_folder", :remote =>  "/home/vagrant/sync_folder" },
    "git_repo" =>{ :local => "~/", :remote =>  "/home/vagrant/git" }
    }
PORTS_FW={ #TODO validate ports are working
    "vscode-8080"=> {:guest => "8080", :host => "8080"}
}

# Calculate CPU and MEM based on a divider
host = RbConfig::CONFIG['host_os']

if host =~ /darwin/
    # sysctl returns Bytes and we need to convert to MB
    mem = `sysctl -n hw.memsize`.to_i / 1024
    cpu_count=`WMIC CPU Get DeviceID,NumberOfCores,NumberOfLogicalProcessors`
    #TODO Get CPU count on macos
    CPU_TO_PROVISON = VM_CPUS
elsif host =~ /linux/
    # meminfo shows KB and we need to convert to MB
    mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024
    cpu_count = `awk "/^processor/ {++n} END {print n}" /proc/cpuinfo 2> /dev/null || sh -c 'sysctl hw.logicalcpu 2> /dev/null || echo ": 2"' | awk \'{print \$2}\' `.chomp
elsif host =~ /mswin|mingw|cygwin/
    # meminfo shows KB and we need to convert to MB
    mem = `wmic computersystem Get TotalPhysicalMemory`.split[1].to_i / 1024
    #TODO Get CPU count on windows
    CPU_TO_PROVISON = VM_CPUS
else
    print host
    print "Host OS is udentified\n"
    print "Using default CPU and RAM"
    CPU_TO_PROVISON = VM_CPUS
    MEM_TO_PROVISION = VM_MEMORY
end

CPU_TO_PROVISON=cpu_count.to_i.div(CPU_DIVIDER)
MEM_TO_PROVISION=mem.to_i.div(MEM_DIVIDER)


print "-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-* \n"
print "Provisioned CPU: #{CPU_TO_PROVISON}\n"
print "Provisioned MEMORY: #{MEM_TO_PROVISION}\n"
print "-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-* \n"


# Check vagrant input and ask user for input to populate some variables
if  Dir.glob("#{File.dirname(__FILE__)}/.vagrant/machines/#{VM_NAME}/*").empty? || ARGV[0] == 'up'
    print "Enter the ABSOLUTE/RELATIVE path to the folder you keep your git projects: ( defaults to ~/ on host ) \n"
    print "e.g: /home/user/Documents/git_code/ \n"
    REPOS_PATH = STDIN.gets.chomp
    print "\n"
    if REPOS_PATH.nil? || REPOS_PATH.empty?
        REPOS_PATH="~/Documents/GitHub/"
        print "==> You need to give a path to mount your Git repos! \n"
        print "==> For now the default will be used... \n\n"
    end
    print "-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-* \n\n"
    print "The '#{REPOS_PATH}' will be mounted as '/home/vagrant/git/' \n"
    print "Press any key to continue..."
    enter = STDIN.gets.chomp
    print "\n"
elsif ARGV[0] == 'destroy'
    print "Have you saved all your work? \n"
end

# Main Vagrant file
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    config.vm.define "#{VM_NAME}" do |ubuntu|

        ubuntu.vm.provision "ansible" do |ansible|
            ansible.playbook = "./provisioners/ansible/pre-deploy.yml"
            ansible.verbose = ""
            ansible.compatibility_mode = "auto"
            ansible.raw_arguments = ["--connection=paramiko"]
        end
        ubuntu.vm.provision "ansible" do |ansible|
            ansible.playbook = "./provisioners/ansible/deploy.yml"
            ansible.verbose = ""
            ansible.compatibility_mode = "auto"
            #ansible.tags="virt" # NOTE Replace virt with the preffered role you want to force coma sepperated.
            #http://www.inanzzz.com/index.php/post/wfj9/running-ansible-provisioning-by-passing-arguments-in-vagrant
            ansible.raw_arguments = Shellwords.shellsplit(ENV["ANSIBLE_ARGS"]) if ENV["ANSIBLE_ARGS"]
            ansible.raw_arguments = ["--connection=paramiko"]
        end
        # ubuntu.vm.provision "ansible" do |ansible|
        #     ansible.playbook = "./provisioners/ansible/post-deploy.yml"
        #     ansible.verbose = ""
        #     ansible.compatibility_mode = "auto"
        #     ansible.raw_arguments = ["--connection=paramiko"]
        # end
        ubuntu.vm.box_check_update = VM_CHECK_UPDATE
        ubuntu.vm.box = "#{VM_BOX_URL}"
        ubuntu.vm.network :private_network, ip: "#{VM_IP}"
        ubuntu.vm.hostname = "#{VM_NAME}#{VM_NAME_DOMAIN}"
        PORTS_FW.each_with_index do |(name, port), index|
            ubuntu.vm.network "forwarded_port", guest: "#{port[:guest]}", host: "#{port[:host]}"
        end
        MOUNT_PATHS.each_with_index do |(name, info), index|
            ubuntu.vm.synced_folder "#{info[:local]}" , "#{info[:remote]}", :mount_options => ["rw"], create: true, disabled: false, automount: true, SharedFoldersEnableSymlinksCreate: true
        end
        ubuntu.vm.provider :virtualbox do |ubuntu|
            ubuntu.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
            ubuntu.customize ["modifyvm", :id, "--memory", MEM_TO_PROVISION]
            ubuntu.customize ["modifyvm", :id, "--cpus", CPU_TO_PROVISON]
            ubuntu.customize ["modifyvm", :id, "--name", "#{VM_NAME}"]
            ubuntu.customize ["modifyvm", :id, "--hwvirtex", "off"]
            ubuntu.customize ["modifyvm", :id, "--pae", "on"]
            ubuntu.customize ["modifyvm", :id, "--vram", "128"]
            ubuntu.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]
            ubuntu.customize ["modifyvm", :id, "--accelerate3d", "off"] # Works better for Apple Mac hosts
            ubuntu.name = "#{VM_NAME}"
            ubuntu.gui = false
        end
    end
end