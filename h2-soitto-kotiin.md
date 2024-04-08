# h2 Soitto kotiin (8.4.2024)

## x) Lue ja tiivistä (8.4.2024 20:15 - )
### Artikkeli [Two Machine Virtual Network With Debian 11 Bullseye and Vagrant](https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/)
Karvisen (2021) artikkelissa  käsiteltiin seuraavia aiheita:
* Kuinka voidaan asentaa Vagrant ja Virtualbox Ubuntu-koneseen.
* Asennuksen jälkeen Vagrantfile-tiedosto luodaan ja muokataan ohjeiden mukaan ja tiedostoon lisätään kaksi `config.vm.define "hostX" do | hostX |` osiota kahdelle koneelle. Muista tehdä molemmille myös `hostX.vm.hostname` ja `hostX.vm.network` määritykset.
* Käynnistetään virtuaalikoneet `vagrant up`-komennolla.
* Molempiin koneisiin voi kirjautua komennolla `vagrant ssh hostX`.
* Ohjeesta löytyy myös,
	* kuinka koneet voi poistaa Virtualbox:sta `vagrant destroy`-komennon avulla,
	* kuinka koneet voi luoda uudelleen `vagrant up`-komennon avulla ja
 	* kuinka IP-osoitteen voi helposti selvittää Debianissa komennolla `hostname -I`.
* Asennuksen voi tarkastaa molemmista koneista käyttämällä `ping`-komentoa.
(lopetus 20:40)

(aloitus 20:42)
### Artikkeli [Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux](https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/)
Karvisen (2018) artikkelissa käsiteltiin kuinka Salt:n avulla voidaan käsitellä tuhansia koneita. Aluksi Salt master-koneen asennus ja sen jälkeen slave-koneen asennus.
```
master$ sudo apt-get -y install salt-master
slave$ sudo apt-get -y install salt-minion
```

Tämän jälkeen muokataan slave-koneen tietoihin master-koneen IP-osoite ja käyttäjätunnus tarvittaessa, sillä slave-koneen tulee tietää mistä master-koneen löytää.
```
slave$ sudoedit /etc/salt/minion
master: 10.0.0.88
id: vagrant
```
Slave-koneen Salt-palvelu tulee käynnistää uudelleen.
```
slave$ sudo systemctl restart salt-minion.service
```
Jotta master-kone voisi hyväksyä sille vieraan slave-koneen hallintaan, master-koneen tulee hyväksyä se manuaalisesti.
```
master$ sudo salt-key -A
Unaccepted Keys:
vagrant
Proceed? [n/Y]
Key for minion vagrant accepted.
```

Tämän jälkeen slave-kone pystyy hallitsemaan yhtä konetta. Tämän voi tarkastaa vaikka `whoami`-komennolla.
```
master$ sudo salt '*' cmd.run 'whoami'
vagrant:
 root
```
Lopuksi master-kone voi testata muita komentoja slave-koneilta.
```
master$ sudo salt '*' cmd.run 'hostname -I'
master$ sudo salt '*' grains.items|less
master$ sudo salt '*' grains.items
master$ sudo salt '*' grains.item virtual
master$ sudo salt '*' pkg.install httpie
master$ sudo salt '*' sys.doc|less
```
(lopetus 21:15)

(aloitus 21:32)
### Artikkeli [Hello Salt Infra-as-Code](https://terokarvinen.com/2024/hello-salt-infra-as-code/)
Karvisen (2024) artikkelissa luotiin "Hello, World!" Salt-konfiguraatio. (Saltin asennus on kuvattu jo aiemmassa dokumentaatiossa, joten en kuvaa sitä tässä uudestaan.)
Jotta Salt voi jakaa hallitsemilleen koneille tiedostoja, luodaan salt-hakemisto master-koneelle.????????
```
slave$ sudo mkdir -p /srv/salt/hello/
slave$ cd /srv/salt/hello/
```
Luodaan `init.sls`-tiedosto hakemistoon `/srv/salt/hello/` (hello kertoo moduulin nimien).
```
/tmp/helloleo:
  file.managed
```
Käynnistetään `hello`-moduuli.
```
slave$ sudo salt-call --local state.apply hello
```
(lopetus 21:48)




## Lähteet
* Karvinen, T. 2018. Salt Quickstart – Salt Stack Master and Slave on Ubuntu Linux. https://terokarvinen.com/2018/salt-quickstart-salt-stack-master-and-slave-on-ubuntu-linux/
* Karvinen, T. 2021. Two Machine Virtual Network With Debian 11 Bullseye and Vagrant. https://terokarvinen.com/2021/two-machine-virtual-network-with-debian-11-bullseye-and-vagrant/
* Karvinen, T. 2024. Hello Salt Infra-as-Code. https://terokarvinen.com/2024/hello-salt-infra-as-code/
