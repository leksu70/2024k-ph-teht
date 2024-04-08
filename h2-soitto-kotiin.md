# h2 Soitto kotiin (8.4.2024)

## x) Lue ja tiivistä (8.4.2024 20:15 - )

Karvisen (2021) artikkelissa [Two Machine Virtual Network With Debian 11 Bullseye and Vagrant](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/) käsiteltiin seuraavia aiheita:
* Kuinka voidaan asentaa Vagrant ja Virtualbox Ubuntu-koneseen.
* Asennuksen jälkeen Vagrantfile-tiedosto luodaan ja muokataan ohjeiden mukaan ja tiedostoon lisätään kaksi `config.vm.define "hostX" do | hostX |` osiota kahdelle koneelle. Muista tehdä molemmille myös `hostX.vm.hostname` ja `hostX.vm.network` määritykset.
* Käynnistetään virtuaalikoneet `vagrant up`-komennolla.
* Molempiin koneisiin voi kirjautua komennolla `vagrant ssh hostX`.
* Ohjeesta löytyy myös,
	* kuinka koneet voi poistaa Virtualbox:sta `vagrant destroy`-komennon avulla ja
   	* kuinka koneet voi luoda uudelleen `vagrant up`-komennon avulla.
* Asennuksen voi tarkastaa molemmista koneista käyttämällä `ping`-komentoa.



## Lähteet
* Karvinen, T. 2021. Two Machine Virtual Network With Debian 11 Bullseye and Vagrant. https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/
* 
