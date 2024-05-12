# Tehtävä 7 - Oma miniprojekti

Nämä tehtävät kuuluvat Tero Karvisen kurssille [Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/).

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori. Tässä vielä Salt versiot minioneilta (Debian 11 ja Windows 10) ja masterilta (Debian 11).

Käytetyt Saltin versiot (3007.0 (Chlorine)) komennolla `sudo salt '*' grains.item saltversion`.
![Salt versiot.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-salt-versions.png "Salt versiot.")

Tehtävien teko on aloitettu 12.5.2024 klo 16.30 ja päätetty XX.5.2024 klo XXXXX.

Tehtävässä on tarkoitus hallita ohjelmistokehitykseen tarkoitettua Windows-tietokonetta, jonka takia virtuaalikoneeseen tulee asentaa Windows 10, sekä seuraavat työkalut: **XXXXXXXXXXXXXXXXXXXXXXXXXXXX**.

## Konfigurointitiedoston skriptit
Tehtävä aloitettu 12.5.2024 klo 16.30 ja valmis klo **XXXXXX**.

Konfiguroinnissa on käytetty [Tero Karvisen (2023)](https://terokarvinen.com/2023/salt-vagrant/) blokikirjoitusta hyväksi.

Debian-koneiden provisoinnissa käytetään kolmea eri skriptiä: `$debscript`, `$masscript` ja `$minscript`. Ensimmäinen on tarkoitettu kaikille Debian-järjestelmille, toinen on tarkoitettu master-koneelle ja kolmas on tarkoitettu minion-koneille.

Ensimmäinen skripti on tarkoitettu Debian-käyttöjärjestelmille, joihin halutaan asentaa Salt master tai minion. Skripti sisältää Debian-koneille paketinhallinnan päivityksen, `curl`-komennon asentamisen, Saltin päivitetyn paketin tiedot ja aliaksen luonnin komentokehotteeseen.
```
### General Debian script
#
$debscript = <<-'EOF'

echo "Add latest Salt version to catalog." >> /tmp/debscript.txt 2>&1
# https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/debian.html#install-salt-on-debian-11-bullseye-amd64

# Install software
echo "Update catalog." >> /tmp/debscript.txt 2>&1
sudo apt-get update >> /tmp/debscript.txt 2>&1
echo "Install curl." >> /tmp/debscript.txt 2>&1
sudo apt-get install curl -y >> /tmp/debscript.txt 2>&1

# Add latest Salt project to keyring
echo "Add latest Salt version to catalog." >> /tmp/debscript.txt 2>&1
sudo mkdir /etc/apt/keyrings  >> /tmp/debscript.txt 2>&1
sudo curl -fsSL -o /etc/apt/keyrings/salt-archive-keyring-2023.gpg https://repo.saltproject.io/salt/py3/debian/11/amd64/SALT-PROJECT-GPG-PUBKEY-2023.gpg  >> /tmp/debscript.txt 2>&1
echo "deb [signed-by=/etc/apt/keyrings/salt-archive-keyring-2023.gpg arch=amd64] https://repo.saltproject.io/salt/py3/debian/11/amd64/latest bullseye main" | sudo tee /etc/apt/sources.list.d/salt.list  >> /tmp/debscript.txt 2>&1

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
**KESKEN!!!**
Lisäksi Windows-koneiden provisointiin käytetään `$winscript`-skriptiä.

## Luodaan Vagrantin konfigurointitiedosto

Luodaan hakemisto projektille komennolla `mkdir Vko8`.**???** ja luodaan Vagrant-hakemisto sen alle komennolla `mkdir Vagrant`.
Siirrtytään Vagrant-hakemistoon `cd Vagrant`-komennolla.

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

Master-virtuaalikoneen voi provisoida `vagrant up master`-komennolla. 

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

Minion-virtuaalikoneen voi provisoida `vagrant up minion`-komennolla.

### Luodaan Windows 10 Salt Minion -virtuaalikone 
Luodaan `Vagrantfile`-tiedostoon Windows minionille osio, mikä sisältää virtuaalikoneen hostnamen, IP-osoitteen privaattiverkkoon ja shell-skriptin `$winscript` suorituksen. provisointivaiheessa.
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

## Lähteet

Karvinen, T. 2018. Control Windows with Salt. https://terokarvinen.com/2018/control-windows-with-salt/.

Karvinen, T. 2023. Salt Vagrant - automatically provision one master and two slaves. https://terokarvinen.com/2023/salt-vagrant/.

Karvinen, T. 2024. Infra as Code - Palvelinten hallinta 2024 https://terokarvinen.com/2024/configuration-management-2024-spring/.

Salt Project. 2024a. Install Salt on Debian 11 (Bullseye) amd64. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/debian.html#install-salt-on-debian-11-bullseye-amd64.

Salt Project. 2024b. Windows. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html.