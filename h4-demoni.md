# H4 - demoni

# Tehtävä x - Lue ja tiivistä
Tehty Windows 10 -koneella 21.4.2024 klo 15.15-15.45.

Tehtävänä on lukea ja tiivistää tärkeimmät asiat muutamalla ranskalaisella viivalla [Karvisen (2023) blogista](https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file), [Saltin (2024) ohjeista](https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml) ja [Karvisen (2018) blogista](https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh).

* [Karvisen (2023) blogi](https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file) esittelee "Hello World!":in saltin tilan (```/srv/salt/hello/init.sls```) ja hallittavien koneiden (slave/orja) tilat (```/srv/salt/top.sls```).

* [Salt-projekti (2024) käyttöohjeet](https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml) kuvaavat **YAML**-kielen syntaksia (rules) ja datan strukturoimiseen käytettäviä skalaareja (scalars), listoja (lists) ja kirjastoja (dictionaries).

* [Karvisen (2018) blogi](https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh) käsittelee package-file-service:n (pkg-file-service) käyttöä luomaan ja hallitsemaan demonien (daemon) tiloja.

Lopuksi silmäilin Saltin ohjeita pkg-, file- ja service-tilafunktioille.

## Tehtävä a - Hello SLS!
Tehtävä tehty 21.4.2024 klo 16.10-XXXX

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

# Lähteet
Karvinen, T. 2018. Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port. Blogi. https://terokarvinen.com/2018/04/03/pkg-file-service-control-daemons-with-salt-change-ssh-server-port/?fromSearch=karvinen%20salt%20ssh.

Karvinen, T. 2023. Infra as Code - Your wishes as a text file. Blogi. https://terokarvinen.com/2023/salt-vagrant/#infra-as-code---your-wishes-as-a-text-file.

Salt Project. 2024. Rules of YAML. https://docs.saltproject.io/salt/user-guide/en/latest/topics/overview.html#rules-of-yaml.
