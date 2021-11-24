servers = [

    {
		:name => "servera",
		:type => "server",
		:disk_size => "1GB",
		:box => "bento/centos-8",
		:eth1 => "192.168.56.10",
		:mem => "1024",
		:cpu => "1"
	},
	{
		:name => "serverb",
		:type => "server",
		:disk_size => "1GB",
		:box => "bento/centos-8",
		:eth1 => "192.168.56.11",
		:mem => "1024",
		:cpu => "1"
	},
	{
		:name => "serverc",
		:type => "server",
		:disk_size => "1GB",
		:box => "bento/centos-8",
		:eth1 => "192.168.56.12",
		:mem => "1024",
		:cpu => "1"
	},
    {
		:name => "serverd",
		:type => "server",
		:disk_size => "1GB",
		:box => "bento/centos-8",
		:eth1 => "192.168.56.13",
		:mem => "1024",
		:cpu => "1"
	},
	{
		:name => "workstation",
		:type => "workstation",
		:box => "bento/centos-8",
		:eth1 => "192.168.56.14",
		:mem => "2048",
		:cpu => "2"
	}
]

$configureBox = <<-SCRIPT

	#Script for all nodes
	sudo yum install epel-release -y
	
	#Edit /etc/hosts

    cat >> /etc/hosts <<EOF
192.168.56.9   workstation.lab.example.com	workstation
192.168.56.10   servera.lab.example.com	servera
192.168.56.11   serverb.lab.example.com	serverb
192.168.56.12   serverc.lab.example.com	serverc
192.168.56.13   serverd.lab.example.com	serverd
EOF

	# Change SSH Configuration
	sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
    echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config
    systemctl restart sshd.service

SCRIPT

$configureWorkstation = <<-SCRIPT

	#Install sshpass
	yum install sshpass -y

	# Generate SSH key and distribute it to other hosts
	cat /dev/zero | sudo --user=vagrant ssh-keygen -q -N ""
	for server in servera serverb serverc serverd; do sudo --user=vagrant sshpass -p "vagrant" ssh-copy-id -i /home/vagrant/.ssh/id_rsa.pub vagrant@$server; done

SCRIPT


Vagrant.configure("2") do |config|

		servers.each do |opts|
				config.vm.define opts[:name] do |config|

						config.vm.box = opts[:box]
						config.vm.disk :disk, size: opts[:disk_size]
						config.vm.box_version = opts[:box_version]
						config.vm.hostname = opts[:name]
						config.vm.network :private_network, ip: opts[:eth1]


						config.vm.provider "virtualbox" do |v|

								v.name = opts[:name]
								v.customize ["modifyvm", :id, "--groups", "/RH294 Lab"]
								v.customize ["modifyvm", :id, "--memory", opts[:mem]]
								v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]

						end

						config.vm.provision "shell", inline: $configureBox

			if opts[:type] == "workstation"
				config.vm.provision "shell", inline: $configureWorkstation
			end



		end

	end

end
