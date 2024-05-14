# -*- mode: ruby -*-
# vi: set ft=ruby :
# Copyright 2019-2021 Tero Karvinen http://TeroKarvinen.com
# Modified by Leo Sutinen 2024.

### Scripts

### General Debian script
#
$debscript = <<-'EOF'

echo "Add latest Salt version to catalog." >> /tmp/debscript.txt 2>&1

# Install software
echo "Update catalog." >> /tmp/debscript.txt 2>&1
sudo apt-get update >> /tmp/debscript.txt 2>&1
echo "Install curl." >> /tmp/debscript.txt 2>&1
sudo apt-get install curl -y >> /tmp/debscript.txt 2>&1

# Add latest Salt project to keyring
echo "Add latest Salt version to catalog." >> /tmp/debscript.txt 2>&1
sudo mkdir /etc/apt/keyrings  >> /tmp/debscript.txt 2>&1
sudo curl -fsSL -o /etc/apt/keyrings/salt-archive-keyring-2023.gpg https://repo.saltproject.io/salt/py3/debian/11/amd64/SALT-PROJECT-GPG-PUBKEY-2023.gpg  >> /tmp/debscript.txt 2>&1
echo "deb [signed-by=/etc/apt/keyrings/salt-archive-keyring-2023.gpg arch=amd64] https://repo.saltproject.io/salt/py3/debian/11/amd64/3006 bullseye main" | sudo tee /etc/apt/sources.list.d/salt.list  >> /tmp/debscript.txt 2>&1

echo "Add alias ll to .bashrc" >> /tmp/debscript.txt 2>&1
echo "alias ll='ls -l --color'" >> ~vagrant/.bashrc 
EOF
#
### General Debian script END

### Salt Master
#
$masscript = <<-'EOF'

echo "Update catalog." >> /tmp/masscript.txt 2>&1
sudo apt-get update >> /tmp/masscript.txt 2>&1

# Needed for Windows support salt-winrepo-ng
echo "Install git." >> /tmp/masscript.txt 2>&1
sudo apt-get install git -y >> /tmp/masscript.txt 2>&1

echo "Install latest 3006 salt-master version." >> /tmp/masscript.txt 2>&1
sudo apt-get install salt-master -y >> /tmp/masscript.txt 2>&1
echo "Create /srv/salt directory." >> /tmp/masscript.txt 2>&1
sudo mkdir -p /srv/salt >> /tmp/masscript.txt 2>&1

echo "Restart salt-master." >> /tmp/masscript.txt 2>&1
sudo systemctl restart salt-master >> /tmp/masscript.txt 2>&1
EOF
#
### Salt Master END

### Salt Minion, Debian
#
$minscript = <<-'EOF'

echo "Update catalog." >> /tmp/minscript.txt 2>&1
sudo apt-get update >> /tmp/minscript.txt 2>&1
echo "Install latest salt-minion version." >> /tmp/minscript.txt 2>&1
sudo apt-get install salt-minion -y >> /tmp/minscript.txt 2>&1

# Configure Minion and restart Minion service to register Minion
sudo echo master: 10.1.0.21 > local.conf
sudo echo id: $(hostname -s) >> local.conf
sudo mv local.conf /etc/salt/minion.d >> /tmp/minscript.txt 2>&1
echo "Restart salt-master." >> /tmp/minscript.txt 2>&1
sudo systemctl restart salt-minion >> /tmp/minscript.txt 2>&1

EOF
#
### Salt Minion, Debian END

### Salt Minion, Windows 10
#
$winscript = <<-'EOF'

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls12'
Invoke-WebRequest -Uri https://repo.saltproject.io/salt/py3/windows/3006/Salt-Minion-3006.8-Py3-AMD64-Setup.exe -OutFile "$env:TEMP\Salt-Minion-3006.8-Py3-AMD64-Setup.exe"
& "$env:TEMP\Salt-Minion-3006.8-Py3-AMD64-Setup.exe" /S /minion-name=win10 /master=10.1.0.21

Set-WinUserLanguageList -LanguageList fi-FI -Force
Set-TimeZone 'FLE Standard Time'
EOF
#
### Salt Minion, Windows END


# 3 hosts 20240512 LS
Vagrant.configure("2") do | config |

	config.vm.synced_folder ".", "/vagrant", disabled: true
	config.vm.box = "debian/bullseye64"

	# Salt Master Debian 11
	config.vm.define "master" do | master |
		master.vm.hostname = "master"
		master.vm.network "private_network", ip: "10.1.0.21"

		master.vm.provider "virtualbox" do | vb |
			# Memory 2 GB
			vb.memory = 2048
		end

		master.vm.provision "shell", inline: $debscript
		master.vm.provision "shell", inline: $masscript

	end

	# Salt Minion Debian 11
	config.vm.define "minion", primary: true do | minion |
		minion.vm.hostname = "minion"
		minion.vm.network "private_network", ip: "10.1.0.22"

		minion.vm.provision "shell", inline: $debscript
		minion.vm.provision "shell", inline: $minscript
  	end

	# Sal Minion Windows 10 H2
  	config.vm.define "win10" do | win10 |
		win10.vm.box = "gusztavvargadr/windows-10"
		win10.vm.hostname = "win10"
		win10.vm.network "private_network", ip: "10.1.0.23"
		win10.vm.boot_timeout = 9000

		win10.vm.provider "virtualbox" do |vb|
			# Memory 12 GB, 2 Virtual CPUs.
			vb.memory = 12288
			vb.cpus = 2
			vb.customize ['modifyvm', :id, '--clipboard', 'bidirectional']  
		end

		win10.vm.provision "shell", inline: $winscript
	end
end
