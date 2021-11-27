# -*- mode: ruby -*-
# vi: set ft=ruby :require 'rbconfig'

require 'yaml'
require 'net/http'
require 'uri'
require "open-uri"

ENV["LC_ALL"] = "en_GB.UTF-8"
# Allow to define the config file via env variable
config_file = ENV['VAGRANT_CONFIG_YAML'] || 'vagrant.yaml'

# Read the config file
if File.file?(config_file)
    $cfg = JSON.parse(JSON.dump(YAML.load_file(config_file)), symbolize_names: true)
else
    abort("ERROR: Can not read the Vagrant YAML configuration from %s." % config_file)
end

settings = $cfg[:settings]

# GLOBAL VARIABLES TO BE USED
# Change if you only know what you are doing :)
VAGRANTFILE_API_VERSION = settings[:VAGRANTFILE_API_VERSION]
VM_GUI = settings[:VM_GUI]
VM_NAME = settings[:VM_NAME]
VM_BOX_URL = settings[:VM_BOX_URL]
VM_NAME_DOMAIN = settings[:VM_NAME_DOMAIN]
VM_CHECK_UPDATE = settings[:VM_CHECK_UPDATE]
VM_DEFAULT_MEMORY = settings[:VM_DEFAULT_MEMORY]
VM_DEFAULT_CPU = settings[:VM_DEFAULT_CPU]
VM_DISK_SIZE = settings[:VM_DISK_SIZE]
VM_ICON = settings[:VM_ICON]
CPU_DIVIDER = settings[:CPU_DIVIDER]
MEM_DIVIDER = settings[:MEM_DIVIDER]
VM_IP = settings[:VM_IP]
VM_POST_UP_MESSAGE = settings[:VM_POST_UP_MESSAGE]
REPOS_PATH = settings[:REPOS_PATH]
ANSIBLE_CHOISE = settings[:ANSIBLE_CHOISE]

MOUNT_PATHS={
    "sync_folder" =>{:local => "./sync_folder", :remote =>  "/home/vagrant/sync_folder" },
    "git_repo" =>{ :local => "~/", :remote =>  "/home/vagrant/git" }
    }
PORTS_FW={
    "vscode"=> {:guest => "8080", :host => "8080", :protocol => "tcp", :auto_correct => "true"},
    "vncserver0"=> {:guest => "5900", :host => "5900", :protocol => "tcp", :auto_correct => "true"},
    "vncserver1"=> {:guest => "5901", :host => "5901", :protocol => "tcp", :auto_correct => "true"},
    "novnc0-xorg"=> {:guest => "6080", :host => "6080", :protocol => "tcp", :auto_correct => "true"},
    "novnc1-vncserver"=> {:guest => "6081", :host => "6081", :protocol => "tcp", :auto_correct => "true"}
}

# Calculate CPU and MEM based on a divider
host = RbConfig::CONFIG['host_os']

if host =~ /darwin/
    # sysctl returns Bytes and we need to convert to MB
    mem = `sysctl -n hw.memsize`.to_i / 1024/1024
    cpu_count=`sysctl -n hw.ncpu`
elsif host =~ /linux/
    # meminfo shows KB and we need to convert to MB
    mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024
    cpu_count = `awk "/^processor/ {++n} END {print n}" /proc/cpuinfo 2> /dev/null || sh -c 'sysctl hw.logicalcpu 2> /dev/null || echo ": 2"' | awk \'{print \$2}\' `.chomp
elsif host =~ /mswin|mingw|cygwin/
    # meminfo shows KB and we need to convert to MB
    mem = `wmic computersystem Get TotalPhysicalMemory`.split[1].to_i / 1024 /1024
    cpu_count = `wmic computersystem Get NumberOfLogicalProcessors`.split[1].to_i
else
    print host
    print "Host OS is udentified\n"
    print "Using default CPU: #{VM_DEFAULT_CPU} and RAM: #{VM_DEFAULT_MEMORY}"
    CPU_TO_PROVISON = VM_DEFAULT_CPUS
    MEM_TO_PROVISION = VM_DEFAULT_MEMORY
end

# Calculate a portion of the Host's resources
CPU_TO_PROVISON=cpu_count.to_i.div(CPU_DIVIDER)
MEM_TO_PROVISION=mem.to_i.div(MEM_DIVIDER)


# Check vagrant input and ask user for input to populate some variables
if  Dir.glob("#{File.dirname(__FILE__)}/.vagrant/machines/#{VM_NAME}/*").empty? || ARGV[0] == 'up'
    print "-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-* \n"
    print "Provisioned CPU: #{CPU_TO_PROVISON}\n"
    print "Provisioned MEMORY: #{MEM_TO_PROVISION}\n"
    print "-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-* \n"
    if REPOS_PATH.nil? || REPOS_PATH.empty?
        print "Enter the ABSOLUTE/RELATIVE path to the folder you keep your git projects: ( defaults to ~/ on host ) \n"
        print "e.g: /home/user/Documents/git_code/ \n"
        REPOS_PATH = STDIN.gets.chomp
        print "\n"
        REPOS_PATH="~/Documents/GitHub/"
        print "==> You need to give a path to mount your Git repos! \n"
        print "==> For now the default will be used... \n\n"
    else
        print "The '#{REPOS_PATH}' will be mounted as '/home/vagrant/git/' \n"
        spinner = Enumerator.new do |e|
            loop do
              e.yield '|'
              e.yield '/'
              e.yield '-'
              e.yield '\\'
            end
          end

          1.upto(100) do |i|
            progress = "=" * (i/10) unless i < 5
            printf("\rLoading: [%-10s] %d%% %s", progress, i, spinner.next)
            sleep(0.03)
          end
        print "\n"
        if ARGV[1]
            VM_NAME = ARGV[1]
        end
        print "The new box will be called: " + VM_NAME + "\n"
    end
    print "-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-* \n\n"

elsif ARGV[0] == 'destroy'
    print "Have you saved all your work? \n"
    if ARGV[1] =='-f'
        if ARGV[2]
            VM_NAME = ARGV[2]
        end
    else
        if ARGV[1]
            VM_NAME = ARGV[1]
        end
    end
    print "The box to destroy will be : " + VM_NAME
elsif ARGV[0] == 'reload'
    printf "Reloading...\n"
    if ARGV[1]
        VM_NAME = ARGV[1]
    end
    print "The box to reload will be : " + VM_NAME
end


open("#{VM_ICON}") do |image|
    File.open("#{File.dirname(__FILE__)}/vm_icon.png", "wb") do |file|
        file.write(image.read)
    end
end


def gui_enabled?
    !ENV.fetch('GUI', '').empty?
end

username = STDIN.gets.chomp


# Main Vagrant file
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    required_plugins = [
        "vagrant-hostmanager",
        "vagrant-vbguest",
        "vagrant-hosts",
        "vagrant-ansible-local",
        "vagrant-disksize",
    ]
    # if encounted issues with installing plugins do VAGRANT_DISABLE_STRICT_DEPENDENCY_ENFORCEMENT=1 before a vagrant command
    config.vagrant.plugins = required_plugins

    config.ssh.forward_agent = true
    config.vm.post_up_message = "#{VM_POST_UP_MESSAGE}"

    config.vm.define "#{VM_NAME}" do |box|
        box.vm.provision "#{ANSIBLE_CHOISE}" do |ansible|
            ansible.playbook = "./provisioners/ansible/pre-deploy.yml"
            ansible.verbose = "v"
            ansible.compatibility_mode = "auto"
            # ansible.raw_arguments = ["--connection=paramiko"]
        end
        box.vm.provision "#{ANSIBLE_CHOISE}" do |ansible|
            ansible.playbook = "./provisioners/ansible/main-deploy.yml"
            # ansible.verbose = "v"
            ansible.compatibility_mode = "auto"
            # ansible.tags="ansible-role-prometheus-node-exporter" # NOTE Replace virt with the preffered role you want to force coma sepperated.
            #http://www.inanzzz.com/index.php/post/wfj9/running-ansible-provisioning-by-passing-arguments-in-vagrant
            ansible.raw_arguments = Shellwords.shellsplit(ENV["ANSIBLE_ARGS"]) if ENV["ANSIBLE_ARGS"]
            # ansible.raw_arguments = ["--connection=paramiko"]
        end
        box.vm.provision "#{ANSIBLE_CHOISE}" do |ansible|
            ansible.playbook = "./provisioners/ansible/post-deploy.yml"
            ansible.verbose = ""
            ansible.compatibility_mode = "auto"
            ansible.raw_arguments = ["--connection=paramiko"]
        end
        box.vm.box_check_update = VM_CHECK_UPDATE
        box.vm.box = "#{VM_BOX_URL}"
        box.disksize.size = "#{VM_DISK_SIZE}"
        box.vm.network :private_network, ip: "#{VM_IP}"
        box.vm.hostname = "#{VM_NAME}#{VM_NAME_DOMAIN}"
        PORTS_FW.each_with_index do |(name, port), index|
            box.vm.network "forwarded_port", guest: "#{port[:guest]}", host: "#{port[:host]}", protocol: "#{port[:protocol]}", auto_correct: "#{port[:auto_correct]}"
        end
        MOUNT_PATHS.each_with_index do |(name, info), index|
            box.vm.synced_folder "#{info[:local]}" , "#{info[:remote]}", :mount_options => ["rw"], create: true, automount: true, SharedFoldersEnableSymlinksCreate: false
        end
        box.vm.provider :virtualbox do |vb| #  customisations can be found here: https://www.virtualbox.org/manual/ch08.html
            vb.check_guest_additions = false
            vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
            vb.customize ["modifyvm", :id, "--memory", MEM_TO_PROVISION]
            vb.customize ["modifyvm", :id, "--cpus", CPU_TO_PROVISON]
            vb.customize ["modifyvm", :id, "--name", "#{VM_NAME}"]
            vb.customize ["modifyvm", :id, "--hwvirtex", "on"]
            vb.customize ["modifyvm", :id, "--pae", "on"]
            vb.customize ["modifyvm", :id, "--vtxvpid", "on"]
            vb.customize ["modifyvm", :id, "--vtxux", "on"]
            vb.customize ["modifyvm", :id, "--paravirtprovider", "kvm"]
            vb.customize ["modifyvm", :id, "--vram", "128"]
            vb.customize ["modifyvm", :id, "--graphicscontroller", "vboxvga"]
            vb.customize ["modifyvm", :id, "--accelerate3d", "off"] # Works better for Apple Mac hosts
            vb.customize ["modifyvm", :id, "--vram", "32"]
            vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
            vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
            vb.customize ["modifyvm", :id, "--draganddrop", "bidirectional"]
            vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
            vb.customize ["modifyvm", :id, "--usb", "off"]
            vb.customize ["modifyvm", :id, "--usbehci", "off"]
            vb.customize ["modifyvm", :id, "--uartmode1", "file",File.join(Dir.pwd, "/#{VM_NAME}.log")]
            vb.customize ["modifyvm", :id, "--iconfile", "#{File.dirname(__FILE__)}/vm_icon.png"]
            vb.name = "#{VM_NAME}"
            vb.gui = gui_enabled?
        end
    end
end