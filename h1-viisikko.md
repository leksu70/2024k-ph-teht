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

Testasin myös muita Saltin komentoja, jotka löytyivät 
.


