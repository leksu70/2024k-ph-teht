# h1 Viisikko 2.4.2024

## Ympäristö
Raportti tehty 2.4.2024 klo 13.50 alkaen omalla LEKSULA-PC-koneella, jossa on käytössä Windows 10 Pro -käyttöjärjestelmä. 

## Tehtävä a - Hello Windows/Mac Salt World!
Tehtävänä oli asentaa Salt-masterkone omaan työasemaan. Asensin Saltin käyttäen Windows-pakettia, joka oli ladattavissa
[Salt Project-sivulla](https://docs.saltproject.io/salt/install-guide/en/latest/topics/downloads.html). Testasin asennuksen onnistumisen ajamalla seuraavan komennon komentoriviltä Administrator-käyttäjänä:

	leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant
	$ salt-call --version
	salt-call 3007.0 (Chlorine)

## Tehtävä b - Hello Vagrant!
Tehtävänä oli asentaa Vagrant-master omaan koneeseen ja tarkastaa sen toimivuus. Tein tämän tunnilla ja käytin tässä apuna 
[Vagrantin](https://developer.hashicorp.com/vagrant/install) Windowsin installointipakettia.

	leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant
	$ vagrant --version
	Vagrant 2.4.1

## Tehtävä c - Uusi Linux-virtuaalikone Vagrantilla
Loin uuden virtuaalikoneen käyttäen Vagrantia ja käynnistin virtuaalikoneen. Virtuaalikoneena käytin valmista Debian-linuxia.

	leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant
	$ vagrant init debian/bullseye64
	A `Vagrantfile` has been placed in this directory. You are now
	ready to `vagrant up` your first virtual environment! Please read
	the comments in the Vagrantfile as well as documentation on
	`vagrantup.com` for more information on using Vagrant.

 	leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant
 	$ vagrant up
	Bringing machine 'default' up with 'virtualbox' provider...
	==> default: Checking if box 'debian/bullseye64' version '11.20240212.1' is up to date...
	==> default: Clearing any previously set forwarded ports...
	==> default: Clearing any previously set network interfaces...
	==> default: Preparing network interfaces based on configuration...
	    default: Adapter 1: nat
	==> default: Forwarding ports...
	    default: 22 (guest) => 2222 (host) (adapter 1)
	==> default: Booting VM...
	==> default: Waiting for machine to boot. This may take a few minutes...
	    default: SSH address: 127.0.0.1:2222
	    default: SSH username: vagrant
	    default: SSH auth method: private key
	==> default: Machine booted and ready!
	==> default: Checking for guest additions in VM...
	    default: The guest additions on this VM do not match the installed version of
	    default: VirtualBox! In most cases this is fine, but in rare cases it can
	    default: prevent things such as shared folders from working properly. If you see
	    default: shared folder errors, please make sure the guest additions within the
	    default: virtual machine match the version of VirtualBox you have installed on
	    default: your host and reload your VM.
	    default:
	    default: Guest Additions Version: 6.0.0 r127566
	    default: VirtualBox Version: 7.0
	==> default: Mounting shared folders...
	    default: /vagrant => C:/leksa/vagrant
	==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
	==> default: flag to force provisioning. Provisioners marked to run always will still run.
	
	==> default: Machine 'default' has a post `vagrant up` message. This is a message
	==> default: from the creator of the Vagrantfile, and not from Vagrant itself:
	==> default:
	==> default: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports


### Tehtävä a - Saltin asennus Linux-koneeseen
Tehtävänä oli asentaa Salt (salt-minion) Linuxille (uuteen virtuaalikoneeseen). Käytin tässä asennuksessa hyväksi 
[Tero Karvisen Run Salt Command Locally](https://terokarvinen.com/2021/salt-run-command-locally/)-sivulta. Tässä versiotiedot
asennetusta salt-minion:sta.

	vagrant@bullseye:~$ sudo salt-call --version
	salt-call 3002.6

### Tehtävä b - Viisi tärkeintä

#### Tilafunktio pkg
Ajamalla seuraavan komennon saadaan selville finger-komennon asennuksen tila.

	vagrant@bullseye:~$ sudo salt-call --local -l info state.single pkg.installed finger
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Running state [finger] at time 11:40:45.578120
	[INFO    ] Executing state pkg.installed for [finger]
	[INFO    ] Executing command dpkg-query in directory '/root'
	[INFO    ] Executing command apt-cache in directory '/root'
	[INFO    ] Executing command apt-get in directory '/root'
	[INFO    ] Executing command dpkg in directory '/root'
	[INFO    ] Executing command systemd-run in directory '/root'
	[INFO    ] Executing command dpkg-query in directory '/root'
	[INFO    ] Made the following changes:
	'finger' changed from 'absent' to '0.17-17'
	
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Completed state [finger] at time 11:40:48.824694 (duration_in_ms=3246.588)
	local:
	----------
	          ID: finger
	    Function: pkg.installed
	      Result: True
	     Comment: The following packages were installed/updated: finger
	     Started: 11:40:45.578106
	    Duration: 3246.588 ms
	     Changes:
	              ----------
	              finger:
	                  ----------
	                  new:
	                      0.17-17
	                  old:
	
	Summary for local
	------------
	Succeeded: 1 (changed=1)
	Failed:    0
	------------
	Total states run:     1
	Total run time:   3.247 s

Asennetusta Linux-koneesta löytyi finger-komento, sillä Result oli True ja "summary for local" näyttää tilan "Succeeded: 1". Koska komentoa ei ollut ajettu aikaisemmin, summaryssä on maininta "(changed=1)".

#### Tilafunktio file
Luodaan tiedosto /tmp/helloleo, jonka sisältönä on "Sutinen" ja tarkastetaan manuaalisesti sen olemassa olo cat-komennolla.

	vagrant@bullseye:~$ sudo salt-call --local -l info state.single file.managed /tmp/helloleo contents="Sutinen"
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Running state [/tmp/helloleo] at time 11:53:43.366966
	[INFO    ] Executing state file.managed for [/tmp/helloleo]
	[INFO    ] File changed:
	New file
	[INFO    ] Completed state [/tmp/helloleo] at time 11:53:43.379870 (duration_in_ms=12.904)
	local:
	----------
	          ID: /tmp/helloleo
	    Function: file.managed
	      Result: True
	     Comment: File /tmp/helloleo updated
	     Started: 11:53:43.366966
	    Duration: 12.904 ms
	     Changes:
	              ----------
	              diff:
	                  New file
	
	Summary for local
	------------
	Succeeded: 1 (changed=1)
	Failed:    0
	------------
	Total states run:     1
	Total run time:  12.904 ms
	vagrant@bullseye:~$ cat /tmp/helloleo
	Sutinen

 "local"-osion alaosiossa Changes löytyy tiedoston luonti tila, joka on "New file" eli uusi tiedosto. Summary-osiossa näkyy onnistunut komennon suoritus "Suceeded: 1 (changed=1)" ja Result-kenttä oli True. Lopuksi tarkastettiin /tmp/helloleo-tiedoston sisältö oikeaksi (Sutinen).

#### Tilafunktio service
Tarkastetaan onko sshd-service päällä seuraavalla komennolla.

	vagrant@bullseye:~$ sudo salt-call --local -l info state.single service.running sshd
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Running state [sshd] at time 12:00:34.347293
	[INFO    ] Executing state service.running for [sshd]
	[INFO    ] Executing command systemctl in directory '/root'
	[INFO    ] Executing command systemctl in directory '/root'
	[INFO    ] Executing command systemctl in directory '/root'
	[INFO    ] The service sshd is already running
	[INFO    ] Completed state [sshd] at time 12:00:34.403616 (duration_in_ms=56.321)
	local:
	----------
	          ID: sshd
	    Function: service.running
	      Result: True
	     Comment: The service sshd is already running
	     Started: 12:00:34.347295
	    Duration: 56.321 ms
	     Changes:
	
	Summary for local
	------------
	Succeeded: 1
	Failed:    0
	------------
	Total states run:     1
	Total run time:  56.321 ms

sshd-service on päällä ja se on nähtävissä "local"-osiossa (Result: true).

#### Tilafunktio user
Tarkastetaan, onko vagrant-käyttäjätunnus olemassa seuraavalla komennolla.

	vagrant@bullseye:~$ sudo salt-call --local -l info state.single user.present vagrant
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Running state [vagrant] at time 12:04:18.102122
	[INFO    ] Executing state user.present for [vagrant]
	[INFO    ] User vagrant is present and up to date
	[INFO    ] Completed state [vagrant] at time 12:04:18.130330 (duration_in_ms=28.211)
	local:
	----------
	          ID: vagrant
	    Function: user.present
	      Result: True
	     Comment: User vagrant is present and up to date
	     Started: 12:04:18.102119
	    Duration: 28.211 ms
	     Changes:
	
	Summary for local
	------------
	Succeeded: 1
	Failed:    0
	------------
	Total states run:     1
	Total run time:  28.211 ms

 local-osiosta nähdään, että vagrant-käyttäjä on olemassa (Result: True) ja komento ajettiin onnistuneesti (Succeeded: 1).

#### Tilafunktio cmd
cmd-tilafunktiolla voidaan ajaa omia komentoja.

	vagrant@bullseye:~$ sudo salt-call --local -l info state.single cmd.run 'finger'
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Running state [finger] at time 12:08:11.028263
	[INFO    ] Executing state cmd.run for [finger]
	[INFO    ] Executing command 'finger' in directory '/root'
	[INFO    ] {'pid': 3065, 'retcode': 0, 'stdout': 'Login     Name       Tty      Idle  Login Time   Office     Office Phone\nvagrant              pts/0      20  Apr  2 11:25 (10.0.2.2)\nvagrant              pts/1          Apr  2 11:47 (10.0.2.2)', 'stderr': ''}
	[INFO    ] Completed state [finger] at time 12:08:11.042522 (duration_in_ms=14.259)
	local:
	----------
	          ID: finger
	    Function: cmd.run
	      Result: True
	     Comment: Command "finger" run
	     Started: 12:08:11.028263
	    Duration: 14.259 ms
	     Changes:
	              ----------
	              pid:
	                  3065
	              retcode:
	                  0
	              stderr:
	              stdout:
	                  Login     Name       Tty      Idle  Login Time   Office     Office Phone
	                  vagrant              pts/0      20  Apr  2 11:25 (10.0.2.2)
	                  vagrant              pts/1          Apr  2 11:47 (10.0.2.2)
	
	Summary for local
	------------
	Succeeded: 1 (changed=1)
	Failed:    0
	------------
	Total states run:     1
	Total run time:  14.259 ms

local-osiosta nähdään, että komento suoritettiin onnistuneesti (Result: True). Changes-osiosta on nähtävissä finger-komennon prosessi id (pid: 3065), virhekanavan tilan olevan tyhjä (stderr:) ja komennon tulostus stdout-osiossa.

### Tehtävä c - Idempotentti
[Wikipedian](https://fi.wikipedia.org/wiki/Idempotenssi) mukaan idempotenssi tarkoittaa metodia/tilaa, jossa esimerkiksi ajamalla komento tai operaatio tuottaa saman tuloksen, kun sitä suoritetaan yhden tai useamman kerran. Esimerkkinä sshd-servicen tilan tarkastus.

	vagrant@bullseye:~$ sudo salt-call --local -l info state.single service.running sshd
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Running state [sshd] at time 12:19:16.933195
	[INFO    ] Executing state service.running for [sshd]
	[INFO    ] Executing command systemctl in directory '/root'
	[INFO    ] Executing command systemctl in directory '/root'
	[INFO    ] Executing command systemctl in directory '/root'
	[INFO    ] The service sshd is already running
	[INFO    ] Completed state [sshd] at time 12:19:16.979988 (duration_in_ms=46.792)
	local:
	----------
	          ID: sshd
	    Function: service.running
	      Result: True
	     Comment: The service sshd is already running
	     Started: 12:19:16.933196
	    Duration: 46.792 ms
	     Changes:
	
	Summary for local
	------------
	Succeeded: 1
	Failed:    0
	------------
	Total states run:     1
	Total run time:  46.792 ms

sshd-servicen tila ei muutu, ellei palvelua ajeta alas. Mikäli muutoksia olisi, Changes-kenttä antaisi siitä tiedon.
Toinen esimerkki on aikaisemmin luodusta /tmp/helloleo-tiedostosta ja sen sisällöstä. Jos tiedoston attribuutteja tai sisältöä muutetaan tai se poistetaan, tiedoston tila pysyy samana ja silloin tila on idempotentti.

	vagrant@bullseye:~$ sudo salt-call --local -l info state.single file.managed /tmp/helloleo contents="Sutinen"
	[INFO    ] Loading fresh modules for state activity
	[INFO    ] Running state [/tmp/helloleo] at time 12:27:50.435483
	[INFO    ] Executing state file.managed for [/tmp/helloleo]
	[INFO    ] File /tmp/helloleo is in the correct state
	[INFO    ] Completed state [/tmp/helloleo] at time 12:27:50.472246 (duration_in_ms=36.763)
	local:
	----------
	          ID: /tmp/helloleo
	    Function: file.managed
	      Result: True
	     Comment: File /tmp/helloleo is in the correct state
	     Started: 12:27:50.435483
	    Duration: 36.763 ms
	     Changes:
	
	Summary for local
	------------
	Succeeded: 1
	Failed:    0
	------------
	Total states run:     1
	Total run time:  36.763 ms

Koska Changes-kenttä on tyhjä ja Result-kenttä on True, muutoksia tilaan ei ole tullut. Joten komennon ajaminen ei muuta tulosta, tällöin tila on idempotentti.

### Tehtävä d - Tietoa koneesta

#### Saltin grains.items-tekniikka
Keräsin virtuaalikoneesta tietoja grains.items-tekniikalla ajamalla seuraavan komennon.

	vagrant@bullseye:~$ sudo salt-call --local -l info grains.items
	local:
	    ----------
	    biosreleasedate:
	        12/01/2006
	    ...
	    osfinger:
        	Debian-11
	    ...
	    hwaddr_interfaces:
        	----------
        	eth0:
            08:00:27:8d:c0:4d
        	lo:
            00:00:00:00:00:00
	    ...
	    virtual:
        	VirtualBox
	    ...

Seuraava komento kertoo BIOSin julkaisupäivän, käytetyn käyttöjärjestelmän ja käytetyn virtuaalialustan tiedot. Nämä tiedot ovat eri kuin fyysisen työaseman tiedot.

#### Saltin grains.item-tekniikka
Voimme muuttaa aikaisempaa komentoa ja keskittyä vain kolmeen kiinnostavaan tietoon ajamalla seuraava komento.

	vagrant@bullseye:~$ sudo salt-call --local -l info grains.item biosreleasedate osfinger virtual
	local:
	    ----------
	    biosreleasedate:
	        12/01/2006
	    osfinger:
	        Debian-11
	    virtual:
	        VirtualBox

## Lähteet
- Cone, Matt (2024). Markdown Cheat Sheet. [https://www.markdownguide.org/cheat-sheet/](https://www.markdownguide.org/cheat-sheet/)
- HashiCorp: Install Vagrant. [https://developer.hashicorp.com/vagrant/install](https://developer.hashicorp.com/vagrant/install)
- Karvinen, Tero: Oppitunti 27.3.2024.
- Karvinen, Tero: Run Salt Command Locally. [https://terokarvinen.com/2021/salt-run-command-locally/](https://terokarvinen.com/2021/salt-run-command-locally/)
- Wikipedia: [https://fi.wikipedia.org/wiki/Idempotenssi](https://fi.wikipedia.org/wiki/Idempotenssi)
