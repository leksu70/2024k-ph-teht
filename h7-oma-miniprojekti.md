# Tehtävä 7 - Oma miniprojekti

Nämä tehtävät kuuluvat Tero Karvisen kurssille [Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/).

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori. Tässä harjoituksessa on käytetty Saltin 3006.8 (Sulfur) versioita, sillä uudempi Saltin version 3007 ei näyttänyt toimivan Windows Minionina (salt-minion-palvelu jäi paused-tilaan).

Tehtävien teko on aloitettu 12.5.2024 klo 16.30 ja lopetettu 13.5.2024 klo 02.25. Tehtävien tekoa on jatkettu 13.5.2024 12.40 ja päätetty klo XXXX.

Tehtävässä on tarkoitus hallita ohjelmistokehitykseen tarkoitettua Windows-tietokonetta, jonka takia virtuaalikoneeseen tulee asentaa Windows 10 Vagrantin avulla, sekä seuraavat työkalut: **XXXXXXXXXXXXXXXXXXXXXXXXXXXX**. 

Virtuaalikoneen alusta on Oraclen VirtualBox (versio 7.0). Tässä harjoituksessa ei keskitytä VirtualBoxin asentamiseen, mutta sen voi asentaa [Oraclen VirtualBox -sivustolta](https://www.virtualbox.org/wiki/Downloads).

## Vagrantin asennus
Tehtävä aloitettu 13.5.2024 klo 12.45 ja lopetettu 13.5.2024 klo 13.30.

Tässä tehtävässä käytetään Hashicorpin Vagrantia (version 2.4.1). Vagrantin voi ladata [Hashicorpin (2024) websivulta](https://developer.hashicorp.com/vagrant/install#windows).

Käynnistä Vagrantin vagrant_2.4.1_windows_amd64.msi-asennusohjelma tuplaklikkaamalla `vagrant_2.4.1_windows_amd64.msi`-installeria.

![Windows File Manager](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-1-vagrant-filemgr.png "Windows File Manager")

Seuraavaksi avautuu asennusohjelma, josta hyväksytään lisenssi valitsemalla `I accept the terms in the License Agreement` ja painetaan `Install`-painiketta 

![Asennuksen aloitus painamalla Install-painiketta](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-2-vagrant-install.png "Asennuksen aloitus painamalla Install-painiketta")

ja asennus voi alkaa.

![Asennus käynnissä](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-3-vagrant-installing.png "Asennus käynnissä")

Asennuksen valmistuttua painetaan `Finish`-painiketta.

![Asennus valmis](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-4-vagrant-finished.png "Asennus valmis")

Lopuksi hyväksytään Vagrantin viimeistelyyn tarvittava tietokoneen uudelleenkäynnistäminen painamalla `Yes`-painiketta.

![Tietokoneen uudelleenkäynnistys](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-5-vagrant-win-restart.png "Tietokoneen uudelleenkäynnistys")

Kun tietokone on käynnistynyt uudelleen ja on kirjauduttu sisään, avataan komentotulkki. Komentotulkin voi avata valitsemalla `Windows Start`-ikoni hiiren oikealla painikkeella 

![Windows Start-valikko](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-6-win-start-small.png "Windows Start-valikko")

ja valitsemalla `Run` avautuu uusi ikkuna, johon kirjoitetaan `cmd` ja painetaa `OK`-painiketta.

![Run-ikkuna](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-7-run-win.png "Run-ikkuna")

Tällöin komentotulkki avautuu. Komentotulkin kautta testataan Vagrantin versio ajamalla komento `vagrant --version`.

![Vagrantin versio](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-8-cmd-vagrant.png "Vagrantin versio")

Näin voidaan todeta, että Vagrant on asentunut koneeseen. Kun Vagrantia suoritetaan myöhemmässä vaiheessa, voidaan todeta, että Vagrant toimii normaalisti.

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

Toinen skripti sisältää `git`in ja `salt-master`in asentamisen, `/srv/salt`-hakemiston luonnin ja lopuksi `salt-master`-palvelun uudelleen käynnistämisen.
```
### Salt Master
#
$masscript = <<-'EOF'

echo "Update catalog." >> /tmp/masscript.txt 2>&1
sudo apt-get update >> /tmp/masscript.txt 2>&1

# Needed for Windows support salt-winrepo-ng
echo "Install git." >> /tmp/masscript.txt 2>&1
sudo apt-get install git -y >> /tmp/masscript.txt 2>&1

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

## Konfigurointitiedoston skripti Windowsille
**KESKEN!!!**
Tehtävä aloitettu 12.5.2024 klo 16.30 ja lopetettu 13.5.2024 klo 01.50.

Konfiguroinnissa on käytetty [Tero Karvisen (2023)](https://terokarvinen.com/2023/salt-vagrant/) blokikirjoitusta hyväksi.

Windows-koneen provisoinnissa käytetään apuna shell-skriptiä `$winscript`. Shell-skripti on tarkoitettu Windows minion -virtuaalikoneille ja sen tehtävänä on asettaa sekuriteettiprotokolla (TLS 1.2), ladata Salt Minion 3006 -asennuspaketti ja asentaa se, sekä asettaa näppäimistö suomenkieliseksi ja TIMEZONE-asetuksen.

Salt-Minionin asennuksen komentorivioptiot on esitelty [Salt Projektin (2024b) sivulla](https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html#windows-nullsoft-exe-install-options). PowerShell-komentojen käytössä on ollut apuna [SaltStack GitHubin (2024)](https://github.com/saltstack/salt-bootstrap) sivusto.

```
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
Luodaan `Vagrantfile`-tiedostoon Windows minionille osio, mikä sisältää virtuaalikoneen hostnamen, IP-osoitteen privaattiverkkoon, 12 GB virtuaalimuistin, 2 virtuaali-CPUn, leikepöydän asetukset ja shell-skriptin `$winscript` suorituksen provisointivaiheessa. 
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
Tehtävä aloitettu 13.5.2024 klo 18.15 ja lopetettu 13.5.2024 klo 19.20.

Aloitetaan avaamalla oma shell-komentotulkki - itse käytän Gitin Bash-shelliä ja avaan sen valmiista kuvakkeesta. Tässä voidaan käyttää myös muita komentotulkkeja.

![Bash-shell](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-10-bash-shell.png "Bash-shell")

Luodaan projektille kansio komennolla `mkdir Vko8` ja sinne alikansio luodaan Vagrant-hakemisto sen alle komennolla `mkdir Vko8/Vagrant`. Siirrtytään Vagrant-hakemistoon `cd Vko8/Vagrant`-komennolla.

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
```
</details>

Tiedoston luomisen jälkeen voidaan Vagrantin avulla luoda virtuaalikoneet sekä asentaa niihin `salt-master`in ja `salt-minion`it. Suoritetaan `vagrant up`-komento ja odotetaan virtuaalikoneiden luomisen ja Saltin asennuksien päättymistä. Käynnistymisen koko output-tiedosto on nähtävissä [h7-vagrant-up.txt](https://github.com/leksu70/2024k-ph-teht/blob/master/h7-vagrant-up.txt)-tiedostossa.

```shell
leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/Vko8/Vagrant
$ date; vagrant up; date
Mon May 13 01:13:23 FLEDT 2024
Bringing machine 'master' up with 'virtualbox' provider...
Bringing machine 'minion' up with 'virtualbox' provider...
Bringing machine 'win10' up with 'virtualbox' provider...
==> master: Importing base box 'debian/bullseye64'...
...
==> win10: Configuring and enabling network interfaces...
==> win10: Running provisioner: shell...
    win10: Running: inline PowerShell script

==> master: Machine 'master' has a post `vagrant up` message. This is a message
==> master: from the creator of the Vagrantfile, and not from Vagrant itself:
==> master:
==> master: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports

==> minion: Machine 'minion' has a post `vagrant up` message. This is a message
==> minion: from the creator of the Vagrantfile, and not from Vagrant itself:
==> minion:
==> minion: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports
Mon May 13 01:29:13 FLEDT 2024

leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/Vko8/Vagrant
$
```

Omassa koneessani kaikkien kolmen virtuaalikoneen ensikäynnistämiseen menee aikaa noin 16 minuuttia, joista pisimmän ajan vie Windows-virtuaalikoneen käynnistäminen. Kolmen koneen uudelleenkäynnistäminen on huomattavasti nopeampaa ja se vie minun tietokoneellani hieman yli kolme minuuttia.

### Tarkastetaan virtuaalikoneiden asennukset ja hyväksytään minioneiden rekisteröinnit
Tehtävä aloitettu 13.5.2024 klo 18.45 ja lopetettu 13.5.2024 klo 19.20.

Koska virtuaalikoneisiin asentui skriptien kautta Salt, voidaan Salt Masterilta (`master`-kone) käydä tarkastamassa, ovatko Salt Minionit yrittäneet rekisteröityä masterille.

Kirjaudutaan `master`-koneeseen komennolla `vagrant ssh master` ja suoritetaan `sudo salt-key -L`-komento. Mikäli minion-koneet ovat rekisteröityneet normaalisti, tällöin niiden pitäisi löytyä `Unaccepted Keys`:n alapuolelta.

![Salt Minionien tarkastus master-koneella](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-11-ssh-salt-key.png "Salt Minionien tarkastus master-koneella")

Hyväksytään minionien rekisteröintipyynnöt (eli hyväksytään minionien julkiset salausavaimet) `master`-koneella komennolla `sudo salt-key -A` ja vastaamalla Proceed?-kysymykseen `y`:llä.

![Salt Minionien hyväksyntä master-koneella](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-12-salt-key-accept.png "Salt Minionien hyväksyntä master-koneella")

Tarkastetaan `sudo salt '*' grains.item osfinger`-komennolla, että `grains`-rajapinta toimii.

![Minionien osfinger](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-13-salt-osfinger.png "Minionien osfinger")

Kaikkiin kolmeen virtuaalikoneeseen Salt on asennettu onnistuneesti, sillä minionien rekisteröiminen onnistui ja `grains`-rajapinta osasi palauttaa pyydetyt tiedot.

## Saltin konfigurointi
Tehtävä aloitettu 13.5.2024 klo 19.30 ja lopetettu 13.5.2024 klo XXXXX.

### Windows-virtuaalikoneen ympäristö
Tehtävässä asennetaan Windows-koneelle Python-kehitysympäristö. Tarvittavat sovellukset ovat Visual Studio Code ja Python-tulkki.

Tässä tehtävässä käytetään apuna [Tero Karvisin (2018) neuvoja](https://terokarvinen.com/2018/control-windows-with-salt/) `salt-winrepo-ng`:n käyttöönottoon.

#### Otetaan käyttöön salt-winrepo-ng
Otetaan yhteys `master`-koneeseen komentotulkista komennolla `vagrant ssh master`. Luodaan `/srv/salt/win`-kansio master-koneelle ja kansion ryhmäksi `salt`, sekä `salt`-käyttäjälle oikeudet kansioon.
```bash
vagrant@master:~$ sudo mkdir /srv/salt/win
vagrant@master:~$ sudo chown root:salt /srv/salt/win
vagrant@master:~$ sudo chmod 775 /srv/salt/win
vagrant@master:~$ sudo ls -ld /srv/salt/win
drwxrwxr-x 2 root salt 4096 May 13 16:42 /srv/salt/win
vagrant@master:~$
```

Asennetaan `repo`- ja `repo-ng`-repositorit `master`-koneelle komennolloilla:
```bash
vagrant@master:~$ sudo salt-run winrepo.update_git_repos
https://github.com/saltstack/salt-winrepo-ng.git:
    True
https://github.com/saltstack/salt-winrepo.git:
    True
```

Päivitetään masterin Windows-pakettien tiedot Windows-minioneiden tietokantaan komennolla `sudo salt -G 'os:windows' pkg.refresh_db`.
```bash
vagrant@master:~$ sudo salt -G 'os:windows' pkg.refresh_db
win10:
    ----------
    failed:
        0
    success:
        312
    total:
        312
vagrant@master:~$
```

Tarkastetaan, että Windows-kone `win10` päivitti tietokantansa. Kirjaudutaan sisään komentotulkista komennolla `vagrant ssh win10` ja tarkastetaan `dir "C:\ProgramData\Salt Project\Salt\var\cache\salt\minion\files\base\win\repo-ng\salt-winrepo-ng\_\vscode.sls"`-komennolla onko tscode.sls-tiedosto olemassa.
```shell
Microsoft Windows [Version 10.0.19045.4291]
(c) Microsoft Corporation. All rights reserved.

vagrant@WIN10 C:\Users\vagrant>dir "C:\ProgramData\Salt Project\Salt\var\cache\s
alt\minion\files\base\win\repo-ng\salt-winrepo-ng\_\vscode.sls"
 Volume in drive C is Windows
 Volume Serial Number is 2AD3-0DDF

 Directory of C:\ProgramData\Salt Project\Salt\var\cache\salt\minion\files\base\
win\repo-ng\salt-winrepo-ng\_

05/13/2024  07:57 PM             1,629 vscode.sls
               1 File(s)          1,629 bytes
               0 Dir(s)  116,042,846,208 bytes free

vagrant@WIN10 C:\Users\vagrant>
```

Koska `vscode.sls`-tiedosto löytyy `win10`-koneelta, Saltin tietokannan päivitys on onnistunut Windows-koneelle.

#### Luodaan vscode:lle Salt-tila
Kirjaudutaan `master`-koneelle komentotulkista komennolla `vagrant ssh master`.

Luodaan `vscode`a varten oma hakemisto komennolla `sudo mkdir /srv/salt/win-vscode` sekä luodaan sinne `init.sls`-tiedosto komennolla `sudo vi /srv/salt/win-vscode/init.sls`. Itse käytän mielellään `vi`-editoria, sillä olen käyttänyt sitä yli 20 vuotta.
```ruby
{% if "Windows" == grains["os"]  %}
windows_pkgs:
  pkg.installed:
    - pkgs:
      - vscode
{% else %}
others:
  test.succeed_without_changes:
    - name: No changes in Linux machines.
{% endif %}
```

##### Lisätään uudempi versio vscodesta
Tarkastetaan, mikä on viimeisin vscode versio tiedostosta `/srv/salt/win/repo-ng/salt-winrepo-ng/_/vscode.sls`, jonka se pystyy asentamaan. Tiedostossa on suurin versio 1.50.1.

Muokataan tiedostoa komennolla `sudo vi /srv/salt/win/repo-ng/salt-winrepo-ng/_/vscode.sls` ja lisätään sinne [versio 1.79.1](https://code.visualstudio.com/updates/v1_79) eli rivi `('1.79.1', '4cb974a7aed77a74c7813bdccd99ee0d04901215'),`. Jotta lataus onnistuu, jälkimmäinen listan elementiksi lisätään koodin commit-tieto, mikä löytyy [GitHub microsoft/vscoden](https://github.com/search?q=repo%3Amicrosoft%2Fvscode+1.79.1+commit&type=issues) sivulta, kun etsii sivulta versionumeroa.
```ruby
# due to winrepo installer limitations you need to manually download x86 + x64 System installer from
# https://code.visualstudio.com/Download
# and put it on the winrepo on master to install it the 'salt://win/repo-ng/vscode/...

{% set PROGRAM_FILES = "%ProgramFiles%" %}
{% set versions = {'5':['70', '61']} %}
{% set VERSIONS = (
        ('1.79.1', '4cb974a7aed77a74c7813bdccd99ee0d04901215'),
        ('1.50.1', 'd2e414d9e4239a252d1ab117bd7067f125afd80a'),
        ('1.49.3', '2af051012b66169dde0c4dfae3f5ef48f787ff69'),
        ('1.48.2', 'a0479759d6e9ea56afa657e454193f72aef85bd0'),
        ('1.39.1', '88f15d17dca836346e787762685a40bb5cce75a8'),
        ('1.38.1', 'b37e54c98e1a74ba89e03073e5a3761284e3ffb0'),
        ('1.37.1', 'f06011ac164ae4dc8e753a3fe7f9549844d15e35'),
        ('1.36.1', '2213894ea0415ee8c85c5eea0d0ff81ecc191529'),
    )
%}

vscode:
  {% for version, guid in VERSIONS %}
  '{{version}}':
    full_name: 'Microsoft Visual Studio Code'
    {% if grains['cpuarch'] == 'AMD64' %}
#    installer: 'salt://win/repo-ng/vscode/VSCodeSetup-x64-{{ version }}.exe'
    installer: 'https://az764295.vo.msecnd.net/stable/{{ guid }}/VSCodeSetup-x64-{{version}}.exe'
{% else %}
#    installer: 'salt://win/repo-ng/vscode/VSCodeSetup-ia32-{{ version }}.exe'
    installer: 'https://az764295.vo.msecnd.net/stable/{{ guid }}/VSCodeSetup-ia32-{{version}}.exe'
{% endif %}
    uninstaller: '{{ PROGRAM_FILES }}\Microsoft VS Code\unins000.exe'
    install_flags: '/SP- /VERYSILENT /NORESTART /MERGETASKS="!RUNCODE,ADDCONTEXTMENUFILES,ADDCONTEXTMENUFOLDERS,ADDTOPATH"'
    uninstall_flags: '/VERYSILENT /NORESTART'
    msiexec: False
    locale: en_US
    reboot: False
  {% endfor %}
```

Tämän jälkeen tietokanta (salt-winrepo-ng) pitää päivittää Windows-minioneille komennolla `sudo salt -G 'os:windows' pkg.refresh_db`.

Kokeillaan asentaa `master`-koneelta vscode manuaalisesti komennolla `sudo salt -G 'os:windows' pkg.install vscode`.

![Vscoden asennus manuaalisesti](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-20-sal-win-inst-man-vscode.png "Vscoden asennus manuaalisesti")

Tämä muutos toimi, joten poistetaan vscode komennolla `sudo salt -G 'os:windows' pkg.remove vscode`.

![Vscoden poisto manuaalisesti](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-20-sal-win-remove-man-vscode.png "Vscoden poisto manuaalisesti")

Otetaan käyttöön Salt-tila `win-vscode` komennolla `sudo salt '*' state.apply win-vscode`.

![Vscode käyttöön Salt-tilalla](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-22-salt-win-vscode.png "Vscode käyttöön Salt-tilalla")

Ajettu komento uudelleen eikä tila muutu.

![Salt-tilan ajo uudestaa](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-23-salt-win-vscode-again.png "Salt-tilan ajo uudestaa")

Tila on siis idempotentti.

##### Tarkastus Windows-koneelta
Tarkastetaan vscoden asennus vielä Windows-koneelta kirjautumalla siihen VirtualBoxin avulla manuaalisesti kirjautumisikkunan kautta. Ensin avataan VirtualBox, jonka näkymä on seuraava.

![VirtualBox-näkymä](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-24-vb.png "VirtualBox-näkymä")

Valitaan luotu Windows-kone (`Vagrant_win10_...`) ja painetaan `Show`-nappulaa. Avautuneesta Windows-ikkunasta painetaan hiirellä kerran ja kirjautumisnäkymä tulee esiin. `Vagrant`-käyttäjän oletussalasana on `vagrant`. Annetaan salasana ja painetaan `Submit`-painiketta (nuoli oikealle).

![Annetaan vagrant-käyttäjän salasana](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-25-win-login.png "Annetaan vagrant-käyttäjän salasana")

Kirjautumisen jälkeen painetaan `Start`-nappulaa hiiren vasemmalla nappulalla ja nähdään, että `Visual Studio Code` on asentunut Windows-koneelle. 

![Start-valikko](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-26-win-start.png "Start-valikko")

Klikataan `Visual Studio Codea` ja odotetaan, että se käynnistyy. Valitaan ylävalikosta `kolme pistettä`, sekä `Help`in alta `About`.

![Etsitän Help-valikko](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-27-vscode-help-about.png "Etsitän Help-valikko")

Uusi ikkuna avautuu, kun painetaa `About`-valintaa.

![About-ikkuna](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-28-vscode-about.png "About-ikkuna")

Näin voidaan todentaa, että oikea Visual Studio Code on asentunut.

Lopuksi painetaan `OK`-nappulaa ja suljetaan Visual Studio Code.

#### Luodaan python3:lle Salt-tila Windows-koneelle
Tehtävä aloitettu 13.5.2024 klo 23.05 ja lopetettu 13.5.2024 klo 23.25.

Luodaan `master`-koneella win-python3-tila luomalla sille oma kansio komennolla `sudo mkdir /srv/salt/win-python3`. Lisätään sinne `init.sls`-tiedosto komennolla `sudo vi /srv/salt/win-python3/init.sls` ja tiedostoon lisätään rivit sekä talletetaan tiedosto.
```ruby
{% if "Windows" == grains["os"]  %}
windows_pkgs:
  pkg.installed:
    - pkgs:
      - python3_x64
{% else %}
others:
  test.succeed_without_changes:
    - name: No changes in Linux machines.
{% endif %}
```

Suoritetaan `win-python3`-tila komennolla `sudo salt '*' state.apply win-python3` ja suoritetaan se uudelleen.

![win-python3-tila](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-30-state-win-python3.png "win-python3-tila")

Ensimmäisellä kerralla asennus onnistui molemmille koneille. Linux-koneille tätä ei asenneta, mutta `win10`-koneelle se asentui. Kun sama komento ajettiin uudelleen, muutosta ei tapahtunut. Tila on idempotentti.

### Luodaan uusi käyttäjä kaikkiin minion-koneisiin


## Lähteet

HashiCorp (2024). Install Vagrant (Windows). https://developer.hashicorp.com/vagrant/install#windows.

Karvinen, T. 2018. Control Windows with Salt. https://terokarvinen.com/2018/control-windows-with-salt/.

Karvinen, T. 2023. Salt Vagrant - automatically provision one master and two slaves. https://terokarvinen.com/2023/salt-vagrant/.

Karvinen, T. 2024. Infra as Code - Palvelinten hallinta 2024 https://terokarvinen.com/2024/configuration-management-2024-spring/.

Oracle (2023). Download VirtualBox. https://www.virtualbox.org/wiki/Downloads.

Salt Project. 2024a. Install Salt on Debian 11 (Bullseye) amd64. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/debian.html#install-salt-on-debian-11-bullseye-amd64.

Salt Project. 2024b. Windows. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html.

SaltStack GitHub (2024). Bootstrapping Salt. https://github.com/saltstack/salt-bootstrap.

Visual Studio Code (2024a). April 2024 (version 1.89). https://code.visualstudio.com/updates/v1_89.

Visual Studio code (2024b). Download visual Studio Code Insiders.

Visual Studio Code (2023a). May 2023 (version 1.79). https://code.visualstudio.com/updates/v1_79.

GitHub microsoft/vscode (2023). https://github.com/search?q=repo%3Amicrosoft%2Fvscode+1.79.1+commit&type=issues.

