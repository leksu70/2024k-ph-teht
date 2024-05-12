# Tehtävä 7 - Oma miniprojekti

Nämä tehtävät kuuluvat Tero Karvisen kurssille [Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/).

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori. Tässä vielä Salt versiot minioneilta (Debian 11 ja Windows 10) ja masterilta (Debian 11).

Tässä harjoituksessa on käytetty Saltin 3006.8 (Sulfur) versioita.

Tehtävien teko on aloitettu 12.5.2024 klo 16.30 ja lopetettu 13.5.2024 klo 02.25. Tehtävien tekoa on jatkettu 13.5.2024 XXXX ja päätetty klo XXXX.

Tehtävässä on tarkoitus hallita ohjelmistokehitykseen tarkoitettua Windows-tietokonetta, jonka takia virtuaalikoneeseen tulee asentaa Windows 10, sekä seuraavat työkalut: **XXXXXXXXXXXXXXXXXXXXXXXXXXXX**.

## Konfigurointitiedoston skriptit Debianille
Tehtävä aloitettu 12.5.2024 klo 16.30 ja lopetettu 13.5.2024 klo 02.25. Tätä harjoitusta jatketaan huomenna klo **XXXXXX**.

Konfiguroinnissa on käytetty [Tero Karvisen (2023)](https://terokarvinen.com/2023/salt-vagrant/) blokikirjoitusta ja [Salt Projektin (2024a)](https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/debian.html#install-salt-on-debian-11-bullseye-amd64) ohjetta hyväksi.

Debian-koneiden provisoinnissa käytetään kolmea eri skriptiä: `$debscript`, `$masscript` ja `$minscript`. Ensimmäinen on tarkoitettu kaikille Debian-järjestelmille, toinen on tarkoitettu master-koneelle ja kolmas on tarkoitettu minion-koneille.

Ensimmäinen skripti on tarkoitettu Debian-käyttöjärjestelmille, joihin halutaan asentaa Salt master tai minion. Skripti sisältää Debian-koneille paketinhallinnan päivityksen, `curl`-komennon asentamisen, Saltin päivitetyn paketin tiedot ja aliaksen luonnin komentokehotteeseen.
```
### General Debian script
#
$debscript = <<-'EOF'

echo "Add version 3006 Salt version to catalog." >> /tmp/debscript.txt 2>&1

# Install software
echo "Update catalog." >> /tmp/debscript.txt 2>&1
sudo apt-get update >> /tmp/debscript.txt 2>&1
echo "Install curl." >> /tmp/debscript.txt 2>&1
sudo apt-get install curl -y >> /tmp/debscript.txt 2>&1

# Add Salt project to keyring
echo "Add latest Salt version to catalog." >> /tmp/debscript.txt 2>&1
sudo mkdir /etc/apt/keyrings  >> /tmp/debscript.txt 2>&1
sudo curl -fsSL -o /etc/apt/keyrings/salt-archive-keyring-2023.gpg https://repo.saltproject.io/salt/py3/debian/11/amd64/SALT-PROJECT-GPG-PUBKEY-2023.gpg  >> /tmp/debscript.txt 2>&1
echo "deb [signed-by=/etc/apt/keyrings/salt-archive-keyring-2023.gpg arch=amd64] https://repo.saltproject.io/salt/py3/debian/11/amd64/3006 bullseye main" | sudo tee /etc/apt/sources.list.d/salt.list  >> /tmp/debscript.txt 2>&1

echo "Add alias ll to .bashrc" >> /tmp/debscript.txt 2>&1
echo "alias ll='ls -l --color'" >> ~vagrant/.bashrc 
EOF
#
### General Debian script END
```

Toinen skripti sisältää `salt-master`in asentamisen, `/srv/salt`-hakemiston luonnin ja lopuksi `salt-master`-palvelun uudelleen käynnistämisen.
```
### Salt Master
#
$masscript = <<-'EOF'

echo "Update catalog." >> /tmp/masscript.txt 2>&1
sudo apt-get update >> /tmp/masscript.txt 2>&1

echo "Install latest salt-master version." >> /tmp/masscript.txt 2>&1
sudo apt-get install salt-master -y >> /tmp/masscript.txt 2>&1
echo "Create /srv/salt directory." >> /tmp/masscript.txt 2>&1
sudo mkdir -p /srv/salt >> /tmp/masscript.txt 2>&1

echo "Restart salt-master." >> /tmp/masscript.txt 2>&1
sudo systemctl restart salt-master >> /tmp/masscript.txt 2>&1
EOF
#
### Salt Master END
```

Kolmas skripti sisältää `salt-minion`in asentamisen, konfiguroinnin ja `salt-minion`-palvelun uudelleenkäynnistämisen.
```
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
```

## Konfigurointitiedoston skriptit Windowsille
**KESKEN!!!**
Tehtävä aloitettu 12.5.2024 klo 16.30 ja valmis klo **XXXXXX**.

Konfiguroinnissa on käytetty [Tero Karvisen (2023)](https://terokarvinen.com/2023/salt-vagrant/) blokikirjoitusta hyväksi.

Windows-koneen provisoinnissa käytetään apuna shell-skriptiä `$winscript`. Shell-skripti on tarkoitettu Windows minion -virtuaalikoneille ja sen tehtävänä on asettaa sekuriteettiprotokolla (TLS 1.2), ladata Salt Minion 3006 -asennuspaketti ja asentaa se, sekä asettaa TIMEZONE-asetuksen.

Salt-Minionin asennuksen komentorivioptiot on esitelty [Salt Projektin (2024b) sivulla](https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html#windows-nullsoft-exe-install-options). PowerShell-komentojen käytössä on ollut apuna [SaltStack GitHubin (2024)](https://github.com/saltstack/salt-bootstrap) sivusto.

```
### Salt Minion, Windows 10
#
$winscript = <<-'EOF'

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]'Tls12'
Invoke-WebRequest -Uri https://repo.saltproject.io/salt/py3/windows/3006/Salt-Minion-3006.8-Py3-AMD64-Setup.exe -OutFile "$env:TEMP\Salt-Minion-3006.8-Py3-AMD64-Setup.exe"
& "$env:TEMP\Salt-Minion-3006.8-Py3-AMD64-Setup.exe" /S /minion-name=win10 /master=10.1.0.21

Set-TimeZone 'FLE Standard Time'
EOF
#
### Salt Minion, Windows END
```

## Virtuaalikoneiden konfigurointiosiot


### Salt Master -virtuaalikone
Luodaan `Vagrantfile`-tiedostoon masterille osio, mikä sisältää virtuaalikoneen hostnamen, IP-osoitteen privaattiverkkoon ja 2 GB muistimäärän. 
```
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
```
Lisäksi provisointivaiheessa suoritetaan kaksi shell-skriptiä `$debscript` ja `$masscript`, mitkä ovat esitelty aiemmin. Provisoinnissa käytetään boxina yleistä `config.vm.box = "debian/bullseye64"`-määritystä.

Master-virtuaalikoneen voi provisoida ja käynnistää `vagrant up master`-komennolla. 

**TÄHÄN KUVA MASTERIN PROVISOINNISTA.**

### Salt Debian Minion -virtuaalikone
Luodaan `Vagrantfile`-tiedostoon minionille osio, mikä sisältää virtuaalikoneen hostnamen, IP-osoitteen privaattiverkkoon ja kahden skriptin ajon provisointivaiheessa.

```
# Salt Minion Debian 11
config.vm.define "minion", primary: true do | minion |
	minion.vm.hostname = "minion"
	minion.vm.network "private_network", ip: "10.1.0.22"

	minion.vm.provision "shell", inline: $debscript
	minion.vm.provision "shell", inline: $minscript
end
```
Lisäksi provisointivaiheessa suoritetaan kaksi shell-skriptiä `$debscript` ja `$minscript`, mitkä ovat esitelty aiemmin. Provisoinnissa käytetään boxina yleistä `config.vm.box = "debian/bullseye64"`-määritystä.

Minion-virtuaalikoneen voi provisoida ja käynnistää `vagrant up minion`-komennolla.

### Windows 10 Salt Minion -virtuaalikone 
Luodaan `Vagrantfile`-tiedostoon Windows minionille osio, mikä sisältää virtuaalikoneen hostnamen, IP-osoitteen privaattiverkkoon, 12 GB virtuaalimuistin, 2 virtuaali-CPUn, leikepöydän asetukset ja shell-skriptin `$winscript` suorituksen. provisointivaiheessa. 
```
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
        # tämä vielä on testattavana.
        vb.customize ['modifyvm', :id, '--clipboard', 'bidirectional']  
    end

    win10.vm.provision "shell", inline: $winscript
end
```

Windows Minion-virtuaalikoneen voi provisoida ja käynnistää `vagrant up win10`-komennolla.

## Luodaan kolme virtuaalikonetta

Aloitetaan luomalla hakemisto projektille komennolla `mkdir Vko8`.**???** ja luodaan Vagrant-hakemisto sen alle komennolla `mkdir Vagrant`.
Siirrtytään Vagrant-hakemistoon `cd Vagrant`-komennolla.

Luodaan Vagrant-tiedosto, jonka sisältö on seuraava
<details><summary>Vagrantfile-tiedosto.</summary>

```ruby
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
```
</details>

Tiedoston luomisen jälkeen voidaan Vagrantin avulla luoda virtuaalikoneet sekä asentaa niihin `salt-master`in ja `salt-minion`it. Suoritetaan `vagrant up`-komento ja odotetaan virtuaalikoneiden luomisen ja Saltin asennuksien päättymistä.

Omassa koneessani kaikkien kolmen virtuaalikoneen ensikäynnistämiseen menee aikaa noin 16 minuuttia, joista pisimmän ajan vie Windows-virtuaalikoneen käynnistäminen.

## Lähteet

Karvinen, T. 2018. Control Windows with Salt. https://terokarvinen.com/2018/control-windows-with-salt/.

Karvinen, T. 2023. Salt Vagrant - automatically provision one master and two slaves. https://terokarvinen.com/2023/salt-vagrant/.

Karvinen, T. 2024. Infra as Code - Palvelinten hallinta 2024 https://terokarvinen.com/2024/configuration-management-2024-spring/.

Salt Project. 2024a. Install Salt on Debian 11 (Bullseye) amd64. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/debian.html#install-salt-on-debian-11-bullseye-amd64.

Salt Project. 2024b. Windows. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html.

SaltStack GitHub (2024). Bootstrapping Salt. https://github.com/saltstack/salt-bootstrap
