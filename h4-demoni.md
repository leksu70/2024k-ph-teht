# H4 - demoni

# Tehtävä x - Lue ja tiivistä
Tehty Windows 10 -koneella 21.4.2024 klo 15.15-15.45.

Tehtävänä on lukea ja tiivistää tärkeimmät asiat muutamalla ranskalaisella viivalla [Karvisen (2023) blogista](https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file), [Saltin (2024) ohjeista](https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml) ja [Karvisen (2018) blogista](https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh).

* [Karvisen (2023) blogi](https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file) esittelee "Hello World!":in saltin tilan (```/srv/salt/hello/init.sls```) ja hallittavien koneiden (slave/orja) tilat (```/srv/salt/top.sls```).

* [Salt-projekti (2024) käyttöohjeet](https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml) kuvaavat **YAML**-kielen syntaksia (rules) ja datan strukturoimiseen käytettäviä skalaareja (scalars), listoja (lists) ja kirjastoja (dictionaries).

* [Karvisen (2018) blogi](https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh) käsittelee package-file-service:n (pkg-file-service) käyttöä luomaan ja hallitsemaan demonien (daemon) tiloja.

Lopuksi silmäilin Saltin ohjeita pkg-, file- ja service-tilafunktioille.

## Tehtävä a - Hello SLS! `init.sls`
Tehtävä tehty 21.4.2024 klo 16.10-16.40.

Tehtävänä on luoda "Hei maailma"-tila tekstitiedostoon.

Luodaan master-koneelle (host1) tarvittava `/srv/salt/`-hakemistorakenne `hello`-tilalle. 
```
sudo mkdir -p /srv/salt/hello
```

Luodaan hello-tila `sudo vi /srv/salt/hello/init.sls`-komennolla ja lisätään tilan hallinta.
```
/tmp/h4-hellosalt-20240421:
  file.managed:
    - contents:
      - Hei maailma!
```

Otetaan hello-tila käyttöön `sudo salt-call --local state.apply hello`.

Tarkastetaan toimiiko hello-tila odotuksen mukaisesti.
![hello-tila ja luodun tiedoston sisältö.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h4-a-hello.png "Hello-tila ja luodun tiedoston sisältö.")

Hello-tila luotu onnistuneesti master-koneelle.

## Tehtävä b - Top. `top.sls`
Tehtävä tehty 21.4.2024 klo 16.45-17.05.

Tilojen ajo automaattisesti onnistuu muokkaamalla `top.sls`-tiedostoa ja lisäämällä siihen halutut tilat `sudo vi /srv/salt/top.sls`-komennolla.
```
base:
  '*':
    - hello
```

Otetaan hello-tila käyttöön salt-orjilla (salt-minion) ajamalla komento salt-masterilla (host1) `sudo salt '*' state.apply`.
![Orjien hello-tila.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h4-b-top.png "Orjien hello-tila.")

Tarkastetaan orjakoneelta hello-tiedoston sisältö.
```
vagrant@host2:~$ cat /tmp/h4-hellosalt-20240421
Hei maailma!
```

Top-tilan käyttöönotto onnistui ja orjakoneelle luotiin tiedosto onnistuneesti.

## Tehtävä c - Apache easy mode

### Apachen käyttöönotto manuaalisesti
Tehtävä tehty 21.4.2024 klo 17.20-17.45.

Tehtävänä on ottaa Apache käyttöön manuaalisesti.

Asennetaan tarvittavat sovellukset Apache ja Curl.
```
vagrant@host0:~/$ sudo apt-get install apache2 curl -y
```

Luodaan Apache-serverin käyttämä konfiguraatiokansio ja konfiguraatio tiedostoon `index.html`.
```
vagrant@host0:~$ mkdir -p publicsites/leo
vagrant@host0:~$ cd publicsites/leo
vagrant@host0:~/publicsites/leo$ cat <<EOF >> index.html
Hello, tämä on tyhjä index.html-tiedosto host0-koneella.
> EOF
```

Siirytään Apache-site:n konfigurointikansioon `cd /etc/apache2/sites-available`-komennolla ja luodaan oma apache-palvelimen `leosite.conf`-konfiguraatio.
```
vagrant@host0:/etc/apache2/sites-available$ sudo vi leosite.conf
<VirtualHost *:80>
 ServerName leo
 DocumentRoot /home/vagrant/publicsites/leo/
 <Directory /home/vagrant/publicsites/leo/ >
   Require all granted
 </Directory>
</VirtualHost>
```

Siirtytään Apache-site:n aktivointikansioon `cd /etc/apache2/sites-enabled`-komennolla ja disabloidaa oletuspalvelin komennolla `sudo a2dissite 000-default.conf`. Aktivoidaan uusi juuri luotu leosite-palvelin komennolla `sudo a2ensite leosite.conf` ja käynnistetään Apache uudelleen `sudo systemctl reload apache2`.

Tarkastetaan Apachen toimivuus `curl`-komennon avulla.
```
vagrant@host0:~$ hostname -I
10.0.2.15 10.1.0.10
vagrant@host0:~$ curl -s 10.1.0.10
Hello, tämä on tyhjä index.html-tiedosto host0-koneella.
```

Apache-palvelu toimii normaalisti.

### Apachen käyttöönotto automaattisesti Salt-tilan avulla
Tehtävä tehty 21.4.2024 klo 18.10-20.28.

Otetaan Apache-palvelin käyttöön käyttäen Saltin tilaa hyväksi.

Luodaan ensin Saltin-tilaan Apache-kansio `sudo mkdir -p /srv/salt/apache` ja luodaan `init.sls`-tiedosto `sudo vi /srv/salt/apache/init.sls`-komennolla.
```
# Apachen asennus
apache2:
  pkg.installed

# Asennetaan curl komentorivitestausta varten
curl:
  pkg.installed

# Apachen leosite-palvelin
/etc/apache2/sites-available/leosite.conf:
  file.managed:
    - source: "salt://apache/leosite.conf"
    - watch_in:
      - service: "apache2.service"

# Aktivoidaan leosite-palvelin
/etc/apache2/sites-enabled/leosite.conf:
  file.symlink:
    - target: "../sites-available/leosite.conf"
    - watch_in:
      - service: "apache2.service"

# Leosite-palvelimen juuri
/home/vagrant/publicsites/leo:
  file.directory:
    - user: vagrant
    - group: vagrant
    - mode: 755
    - makedirs: True

# Leosite-palvelimen pääsivu
/home/vagrant/publicsites/leo/index.html:
  file.managed:
    - user: vagrant
    - group: vagrant
    - source: "salt://apache/index.html"
    - watch_in:
      - service: "apache2.service"

# Poistetaan oletus palvelin.
/etc/apache2/sites-enabled/000-default.conf:
  file.absent:
    - watch_in:
      - service: "apache2.service"

# Apachen palvelun seuranta
apache2.service:
  service.running
```

Luodaan `leosite.conf`-konfiguraatiotiedosto Apachea varten Saltin apache-kansioon komennolla `sudo vi /srv/salt/apache/leosite.conf`.
```
<VirtualHost *:80>
  ServerName leksula
  DocumentRoot /home/vagrant/publicsites/leo/
  <Directory /home/vagrant/publicsites/leo/>
    Require all granted
  </Directory>
</VirtualHost>
```

Luodaan `index.html`-tiedosto leosite:a varten komennolla `sudo vi /srv/salt/apache/index.html`.
```
Hello, tämä on tyhjä index.html-tiedosto.
```

Otetaan apache käyttöön Salt-masterilla komennolla `sudo salt-call --local state.apply apache` ja tarkastetaan Apache-serverin tila `curl -s curl -s 10.1.0.11`.

![Masterin apache-tila.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h4-c-apache.png "Masterin apache-tila.")

Apache ja curl otettu käyttöön onnistuneesti.

Päädyin välillä tilanteeseen, jolloin `sudo salt-call --local state.apply apache`-komennon ajo päättyi **killed**-tilaan ja suoraan komentokehotteeseen. Ajoin tämän jälkeen `echo $?`-komennon ja paluuarvo oli 137. Tämä paluuarvo saattaa merkitä muistin loppumista (paging).


# Lähteet
Karvinen, T. 2018. Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port. Blogi. https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh.

Karvinen, T. 2023. Infra as Code - Your wishes as a text file. Blogi. https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file.

Salt Project. 2024. Rules of YAML. https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml.
