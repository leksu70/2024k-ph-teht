# h1 Viisikko 2.4.2024

## Ympäristö
Raportti tehty 2.4.2024 klo 13.50 alkaen omalla LEKSULA-PC-koneella, jossa on käytössä Windows 10 Pro -käyttöjärjestelmä. Materiaalina käytetty

## Tehtävä a - Hello Windows/Mac Salt World!
Tehtävänä oli asentaa Salt-masterkone omaan työasemaan. Asensin Saltin käyttäen Windows-paketti, joka oli ladattavissa
[Salt Project-sivulla](https://docs.saltproject.io/salt/install-guide/en/latest/topics/downloads.html). Testasin asennuksen onnistumisen 
ajamalla seuraavan komennon komentoriviltä Administrator-käyttäjänä:

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
Loin uuden virtuaalikoneen käyttäen Vagrantia.

	leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant
	$ vagrant init debian/bullseye64
	A `Vagrantfile` has been placed in this directory. You are now
	ready to `vagrant up` your first virtual environment! Please read
	the comments in the Vagrantfile as well as documentation on
	`vagrantup.com` for more information on using Vagrant.

## Tehtävä d - Saltin asennus Linux-koneeseen
Tehtävänä oli asentaa Salt (salt-minion) Linuxille (uuteen virtuaalikoneeseen). Käytin tässä asennuksessa hyväksi 
[Tero Karvisen Run Salt Command Locally](https://terokarvinen.com/2021/salt-run-command-locally/)-sivulta. Tässä versiotiedot
asennetusta salt-minion:sta.

	vagrant@bullseye:~$ sudo salt-call --version
	salt-call 3002.6

## Tehtävä e - Viisi tärkeintä

### Tilafunktio pkg
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

Asennetusta Linux-koneesta löytyi finger-komento, sillä "summary for local" näyttää tilan "Succeeded: 1". Koska komentoa ei ollut ajettu aikaisemmin, summaryssä on maininta "(changed=1)".

### Tilafunktio file
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

 "local"-osion alaosiossa Changes löytyy tiedoston luonti tila, joka on "New file" eli uusi tiedosto. Summary-osiossa näkyy onnistunut komennon suoritus "Suceeded: 1 (changed=1)". Lopuksi tarkastettiin /tmp/helloleo-tiedoston sisältö oikeaksi (Sutinen).

### Tilafunktio service
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

### Tilafunktio user
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

 local-osiosta nähdään, että vagrant-käyttäjä on olemassa (Result: True).
