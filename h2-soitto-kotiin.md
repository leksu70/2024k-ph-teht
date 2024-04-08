# h2 Soitto kotiin (8.4.2024)

## x) Lue ja tiivistä (8.4.2024 20:15-20:40)
### Artikkeli [Two Machine Virtual Network With Debian 11 Bullseye and Vagrant](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/)
Karvisen (2021) artikkelissa  käsiteltiin seuraavia aiheita:
* Kuinka voidaan asentaa Vagrant ja Virtualbox Ubuntu-koneseen.
* Asennuksen jälkeen Vagrantfile-tiedosto luotiin ja muokattiin ohjeiden mukaan ja tiedostoon lisätttiin kaksi `config.vm.define "hostX" do | hostX |` osiota kahdelle koneelle. Tämä muistettiin tehdä molemmille koneille, sekä tehtiin `hostX.vm.hostname` ja `hostX.vm.network` määritykset.
* Käynnistetiin virtuaalikoneet `vagrant up`-komennolla.
* Molempiin koneisiin pystyttiin kirjautua komennolla `vagrant ssh hostX`.
* Ohjeesta löytyi myös,
	* kuinka koneet voi poistaa Virtualbox:sta `vagrant destroy`-komennon avulla,
	* kuinka koneet voi luoda uudelleen `vagrant up`-komennon avulla ja
 	* kuinka IP-osoitteen voi helposti selvittää Debianissa komennolla `hostname -I`.
* Asennus voitiin tarkastaa molemmista koneista käyttämällä `ping`-komentoa.

### Artikkeli [Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/) (8.4.2024 20:45-21:15)
Karvisen (2018) artikkelissa käsiteltiin kuinka Salt:n avulla voidaan käsitellä tuhansia koneita. Aluksi asennettiin päivitettiin apt:n katalogi ja sen jälkeen Salt master-kone sekä slave-kone.
```
master$ sudo apt-get update
master$ sudo apt-get -y install salt-master
slave$ sudo apt-get update
slave$ sudo apt-get -y install salt-minion
```

Tämän jälkeen muokattiin slave-koneen tietoihin master-koneen IP-osoite ja käyttäjätunnus, sillä slave-koneen tulee tietää mistä master-koneen löytää.
```
slave$ sudoedit /etc/salt/minion
master: 10.0.0.88
id: vagrant
```
Slave-koneen Salt-palvelu tuli käynnistää uudelleen.
```
slave$ sudo systemctl restart salt-minion.service
```
Jotta master-kone voisi hyväksyä sille vieraan slave-koneen hallintaan, master-koneen tuli hyväksyä se manuaalisesti.
```
master$ sudo salt-key -A
Unaccepted Keys:
vagrant
Proceed? [n/Y]
Key for minion vagrant accepted.
```

Tämän jälkeen slave-kone pystyi hallitsemaan yhtä konetta. Tämä voitiin tarkastaa esim. `whoami`-komennolla.
```
master$ sudo salt '*' cmd.run 'whoami'
vagrant:
 root
```
Lopuksi master-konetta voitiin testata käyttämällä muita komentoja slave-koneella.
```
master$ sudo salt '*' cmd.run 'hostname -I'
master$ sudo salt '*' grains.items|less
master$ sudo salt '*' grains.items
master$ sudo salt '*' grains.item virtual
master$ sudo salt '*' pkg.install httpie
master$ sudo salt '*' sys.doc|less
```

### Artikkeli [Hello Salt Infra-as-Code](https://terokarvinen.com/2024/hello-salt-infra-as-code/) (8.4.2024 21:32-21:48)
Karvisen (2024) artikkelissa luotiin "Hello, World!" Salt-konfiguraatio. (Saltin asennus oli kuvattu jo aiemmassa dokumentaatiossa, joten en kuvammut sitä tässä uudestaan.)
Jotta Salt pystyi jakamaan hallitsemilleen koneille tiedostoja, luotiin salt-hakemisto master-koneelle.????????
```
slave$ sudo mkdir -p /srv/salt/hello/
slave$ cd /srv/salt/hello/
```
Luodiin `init.sls`-tiedosto hakemistoon `/srv/salt/hello/` (hello kertoi moduulin nimen).
```
/tmp/helloleo:
  file.managed
```
Käynnistettiiän `hello`-moduuli.
```
slave$ sudo salt-call --local state.apply hello
```

## a) Kaksi virtuaalikonetta (8.4.2024 21:55-22.35)
Tehtävä oli asentaa kaksi virtuaalikonetta samaan verkkoon ja osoittamaan, että niitä pystyttiin käyttämään. Tehtävässä lopuksi testatiin toimivuus `ping`-komenolla. Tehtävässä käytettiin [Karvisen (2021) esimerkkiä](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/) apuna.

Luotiin uusi `Vagrantfile`-tiedosto `twohost`-hakemistoon. Omassa koneessani oli Windows 10 -käyttöjärjestelmä ja käytin git:n shelliä komentorivinä. Luodun tiedoston tulostus.
```
$ cat Vagrantfile
# -*- mode: ruby -*-
# vi: set ft=ruby :
# Copyright 2019-2021 Tero Karvinen http://TeroKarvinen.com
# Modified by Leo Sutinen 2024.
Vagrant.configure("2") do |config|
        config.vm.synced_folder ".", "/vagrant", disabled: true
        config.vm.box = "debian/bullseye64"
        # 2 konetta 20240402 LS
        config.vm.define "host1" do |host1|
                host1.vm.hostname = "host1"
                host1.vm.network "private_network", ip: "10.1.0.11"
        end
        config.vm.define "host2", primary: true do |host2|
                host2.vm.hostname = "host2"
                host2.vm.network "private_network", ip: "10.1.0.12"
  end
end
```
Luotiin ja käynnistettiin kaksi virtuaalikonetta Vagrantilla. Komennon tulostuksen piilotuksessa on käytetty apuna [Stackoverflow:sta Cousineaun (2021)](https://stackoverflow.com/questions/51997371/how-can-i-hide-block-of-text-using-youtrack-markdown-syntax) vastausta.
```
$ vagrant up
```
<details><summary>Komennon koko tulostus.</summary>

```
Bringing machine 'host1' up with 'virtualbox' provider...
Bringing machine 'host2' up with 'virtualbox' provider...

==> host1: Importing base box 'debian/bullseye64'...
==> host1: Matching MAC address for NAT networking...
==> host1: Checking if box 'debian/bullseye64' version '11.20240212.1' is up to date...
==> host1: Setting the name of the VM: twohost_host1_1712603536365_58101
==> host1: Clearing any previously set network interfaces...
==> host1: Preparing network interfaces based on configuration...
    host1: Adapter 1: nat
    host1: Adapter 2: hostonly
==> host1: Forwarding ports...
    host1: 22 (guest) => 2222 (host) (adapter 1)
==> host1: Booting VM...
==> host1: Waiting for machine to boot. This may take a few minutes...
    host1: SSH address: 127.0.0.1:2222
    host1: SSH username: vagrant
    host1: SSH auth method: private key
    host1:
    host1: Vagrant insecure key detected. Vagrant will automatically replace
    host1: this with a newly generated keypair for better security.
    host1:
    host1: Inserting generated public key within guest...
    host1: Removing insecure key from the guest if it's present...
    host1: Key inserted! Disconnecting and reconnecting using new SSH key...
==> host1: Machine booted and ready!
==> host1: Checking for guest additions in VM...
    host1: The guest additions on this VM do not match the installed version of
    host1: VirtualBox! In most cases this is fine, but in rare cases it can
    host1: prevent things such as shared folders from working properly. If you see
    host1: shared folder errors, please make sure the guest additions within the
    host1: virtual machine match the version of VirtualBox you have installed on
    host1: your host and reload your VM.
    host1:
    host1: Guest Additions Version: 6.0.0 r127566
    host1: VirtualBox Version: 7.0
==> host1: Setting hostname...
==> host1: Configuring and enabling network interfaces...
==> host2: Importing base box 'debian/bullseye64'...
==> host2: Matching MAC address for NAT networking...
==> host2: Checking if box 'debian/bullseye64' version '11.20240212.1' is up to date...
==> host2: Setting the name of the VM: twohost_host2_1712603579423_14513
==> host2: Fixed port collision for 22 => 2222. Now on port 2200.
==> host2: Clearing any previously set network interfaces...
==> host2: Preparing network interfaces based on configuration...
    host2: Adapter 1: nat
    host2: Adapter 2: hostonly
==> host2: Forwarding ports...
    host2: 22 (guest) => 2200 (host) (adapter 1)
==> host2: Booting VM...
==> host2: Waiting for machine to boot. This may take a few minutes...
    host2: SSH address: 127.0.0.1:2200
    host2: SSH username: vagrant
    host2: SSH auth method: private key
    host2:
    host2: Vagrant insecure key detected. Vagrant will automatically replace
    host2: this with a newly generated keypair for better security.
    host2:
    host2: Inserting generated public key within guest...
    host2: Removing insecure key from the guest if it's present...
    host2: Key inserted! Disconnecting and reconnecting using new SSH key...
==> host2: Machine booted and ready!
==> host2: Checking for guest additions in VM...
    host2: The guest additions on this VM do not match the installed version of
    host2: VirtualBox! In most cases this is fine, but in rare cases it can
    host2: prevent things such as shared folders from working properly. If you see
    host2: shared folder errors, please make sure the guest additions within the
    host2: virtual machine match the version of VirtualBox you have installed on
    host2: your host and reload your VM.
    host2:
    host2: Guest Additions Version: 6.0.0 r127566
    host2: VirtualBox Version: 7.0
==> host2: Setting hostname...
==> host2: Configuring and enabling network interfaces...

==> host1: Machine 'host1' has a post `vagrant up` message. This is a message
==> host1: from the creator of the Vagrantfile, and not from Vagrant itself:
==> host1:
==> host1: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports

==> host2: Machine 'host2' has a post `vagrant up` message. This is a message
==> host2: from the creator of the Vagrantfile, and not from Vagrant itself:
==> host2:
==> host2: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports
```
</details>

Virtuaalikoneiden luonti onnistui. Tarkastettiin luotujen virtuaalikoneiden yhteydet molemmilla koneilla. Esimerkkinä toimi host1-kone.
```
leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/twohost
$ vagrant ssh host1
```
<details><summary>Komennon koko tulostus.</summary>

```
Linux host1 5.10.0-28-amd64 #1 SMP Debian 5.10.209-2 (2024-01-31) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

Last login: Mon Apr  8 19:30:30 2024 from 10.0.2.2
vagrant@host1:~$
```
</details>

Tarkastettiin yhteydet molempien virtuaalikoneiden välillä `ping`-komennolla.
```
vagrant@host1:~$ ping -c 1 10.1.0.12
PING 10.1.0.12 (10.1.0.12) 56(84) bytes of data.
64 bytes from 10.1.0.12: icmp_seq=1 ttl=64 time=0.436 ms

--- 10.1.0.12 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.436/0.436/0.436/0.000 ms
```

```
vagrant@host2:~$ ping -c 1 10.1.0.11
PING 10.1.0.11 (10.1.0.11) 56(84) bytes of data.
64 bytes from 10.1.0.11: icmp_seq=1 ttl=64 time=0.364 ms

--- 10.1.0.11 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.364/0.364/0.364/0.000 ms
```
Todettiin, että yhteydet toimivat normaalisti, sillä yhtään ICMP-pakettia ei ollut hukattu.

## b) Saltin herra-orja arkkitehtuuri toimimaan verkon yli (8.4.2024 23:15-23:45)
Tehtävänä oli asentaa Saltin master-slave arkkitehtuuri toimimaan verkon yli. Verkkona käytettiin virtuaalista verkkoa virtuaalikoneiden välillä. Tehtävässä käytettiin [Karvisen (2018) ohjetta](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/) apuna.

Aluksi päivitettii molempien virtuaalikoneiden katalogi molemmilla koneilla.
```
vagrant@host1:~$ sudo apt-get update
```
<details><summary>Komennon koko tulostus.</summary>

```
Get:1 https://deb.debian.org/debian bullseye InRelease [116 kB]
Get:2 https://security.debian.org/debian-security bullseye-security InRelease [48.4 kB]
Get:3 https://deb.debian.org/debian bullseye-updates InRelease [44.1 kB]
Get:4 https://deb.debian.org/debian bullseye-backports InRelease [49.0 kB]
Get:5 https://deb.debian.org/debian bullseye/main Sources [8500 kB]
Get:6 https://security.debian.org/debian-security bullseye-security/main Sources [170 kB]
Get:7 https://security.debian.org/debian-security bullseye-security/main amd64 Packages [271 kB]
Get:8 https://security.debian.org/debian-security bullseye-security/main Translation-en [175 kB]
Get:9 https://deb.debian.org/debian bullseye/main amd64 Packages [8068 kB]
Get:10 https://deb.debian.org/debian bullseye/main Translation-en [6236 kB]
Get:11 https://deb.debian.org/debian bullseye-updates/main Sources [7908 B]
Get:12 https://deb.debian.org/debian bullseye-updates/main amd64 Packages [18.8 kB]
Get:13 https://deb.debian.org/debian bullseye-updates/main Translation-en [10.9 kB]
Get:14 https://deb.debian.org/debian bullseye-backports/main Sources [381 kB]
Get:15 https://deb.debian.org/debian bullseye-backports/main amd64 Packages [405 kB]
Get:16 https://deb.debian.org/debian bullseye-backports/main Translation-en [345 kB]
Fetched 24.8 MB in 7s (3603 kB/s)
Reading package lists... Done
```
</details>

```
vagrant@host2:~$ sudo apt-get update
```

Seuraavaksi asennettiin host1-koneelle Salt master ja host2-koneelle Salt slave.
```
vagrant@host1:~$ sudo apt-get -y install salt-master
vagrant@host2:~$ sudo apt-get -y install salt-minion
```
Asennuksissa ei tullut virheitä.
Konfiguroitiin Salt slave eli host2-koneeseen master koneen tiedot muokkaamalla `/etc/salt/minion`-tiedostoa.
```
vagrant@host2:~$ sudoedit /etc/salt/minion

master: 10.1.0.11
id: host2
```
Koska salt-palvelu oli päällä, se piti käynnistää uudelleen.
```
vagrant@host2:~$ sudo systemctl restart salt-minion.service
```
Jotta Salt master hyväksyy uuden slaven, sen ensin täytyi hyväksyä slaven avain.
```
vagrant@host1:~$ sudo salt-key -A
The following keys are going to be accepted:
Unaccepted Keys:
host2
Proceed? [n/Y] y
Key for minion host2 accepted.
```
Tämän jälkeen testattiin kahdella eri komennolla toimiko yhteys Salt masterilta slavelle.
```
vagrant@host1:~$ sudo salt '*' cmd.run 'whoami'
host2:
    root
vagrant@host1:~$ sudo salt '*' grains.item fqdn
host2:
    ----------
    fqdn:
        host2
```
Kyllä yhteys toimi normaalisti.

## c) Shell-komento orjalla (9.4.2024 00:14-00:15)
Tehtävänä oli ajaa shell-komento slavella Saltin master-slave yhteyden yli. Tämä tehtävä tehtiin tehtävän b) lopussa ajamallla whoami shell-komento.

## d) Idempotentti komentojen ajo master-slave yhteydessä (9.4.2024 00:15-00:35)
Tehtävänä oli ajaa idempotentteja (state.single) komentoja master-slave yhteyden yli. Apuna käytettiin [VMwaren Salt User Guidea](https://docs.saltproject.io/salt/user-guide/en/latest/).

### pkg.installed
Tarkastettiin onko vim-editori asennettuna slavelle.
```
vagrant@host1:~$ sudo salt '*' state.single pkg.installed name='vim'
host2:
----------
          ID: vim
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: vim
     Started: 21:21:24.291150
    Duration: 7031.862 ms
     Changes:
              ----------
              libgpm2:
                  ----------
                  new:
                      1.20.7-8
                  old:
              vim:
                  ----------
                  new:
                      2:8.2.2434-3+deb11u1
                  old:
              vim-runtime:
                  ----------
                  new:
                      2:8.2.2434-3+deb11u1
                  old:

Summary for host2
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1
Total run time:   7.032 s
vagrant@host1:~$ sudo salt '*' state.single pkg.installed name='vim'
host2:
----------
          ID: vim
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 21:23:12.587941
    Duration: 46.724 ms
     Changes:

Summary for host2
------------
Succeeded: 1
Failed:    0
------------
Total states run:     1
Total run time:  46.724 ms
vagrant@host1:~$
```
Ajettiin komento `sudo salt '*' state.single pkg.installed name='vim'` kahteen kertaan, jotta nähtiin muuttuiko editorin asennuksen tila. Tila ei muuttunut ("Changes:"), joten kyseessä on idempotentti tila.

### user.present
Testattiin, löytyykö vagrant-käyttäjä slave koneesta.
```
vagrant@host1:~$ sudo salt '*' state.single user.present vagrant
host2:
----------
          ID: vagrant
    Function: user.present
      Result: True
     Comment: User vagrant is present and up to date
     Started: 21:30:18.033859
    Duration: 34.494 ms
     Changes:

Summary for host2
------------
Succeeded: 1
Failed:    0
------------
Total states run:     1
Total run time:  34.494 ms
```
Vagrant käyttäjä löytyi host2-koneesta ja ajamalla komento uudelleen ei tapahtunut muutosta ("Changes:"). Tila oli idempotentti.

### service.running
Tarkastettiin, oliko cron-palvelu käynnissä slave koneella.
```
vagrant@host1:~$ sudo salt '*' state.single service.running cron.service
host2:
----------
          ID: cron.service
    Function: service.running
      Result: True
     Comment: The service cron.service is already running
     Started: 21:47:44.268845
    Duration: 49.428 ms
     Changes:

Summary for host2
------------
Succeeded: 1
Failed:    0
------------
Total states run:     1
Total run time:  49.428 ms
```
Kyllä cron-palvelu oli käynnissä. Tila on idempotentti, jos palvelu pysyy pystyssä eikä se jostain syystä kaadu tai sitä ei sammuteta manuaalisesti eli sen tila ei muutu ("Changes:").

## e) Tekninen tieto verkon yli (9.4.2024 00:55-01:00)
Tehtävänä oli kerätä teknistä tietoa orjista verkon yli grans.item:n avulla.

Haettin slave-koneelta käyttöjärjestelmätieto ja konfiguroitu Salt masterin IP-osoite.
```
vagrant@host1:~$ sudo salt '*' grains.item osfinger master
host2:
    ----------
    master:
        10.1.0.11
    osfinger:
        Debian-11
```

## f) Hello, IaC, infraa koodina (9.4.2024 XXXXX-XXXXX)
Tämä on kesken!

## Lähteet
* Cousineau, D. 2021. Stackoverflow: How can I hide block of text using YouTrack Markdown syntax? https://stackoverflow.com/questions/51997371/how-can-i-hide-block-of-text-using-youtrack-markdown-syntax
* Karvinen, T. 2018. Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux. https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/
* Karvinen, T. 2021. Two Machine Virtual Network With Debian 11 Bullseye and Vagrant. https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/
* Karvinen, T. 2024. Hello Salt Infra-as-Code. https://terokarvinen.com/2024/hello-salt-infra-as-code/
* VMware Inc. 2021-2024. Salt Project User Guide. https://docs.saltproject.io/salt/user-guide/en/latest/
