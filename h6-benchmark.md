# Tehtävä h6 - Benchmark
Nämä tehtävät kuuluvat Tero Karvisen kurssille [Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/).

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori. Tässä vielä Salt versiot minioneilta (Debian 11 ja Windows 10) ja masterilta (Debian 11).

Käytetyt Saltin versiot (3006.8) komennolla `sudo salt '*' grains.item saltversion`.
![Salt versiot.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-salt-versions.png "Salt versiot.")

Tehtävien teko on aloitettu 5.5.2024 klo 20.30 ja päätetty 6.5.2024 klo 22.45.

## Tehtävä x - Lue ja tiivistä
Tehtävä aloitettu 5.5.2024 klo 21.30 ja valmis 6.5.2024 klo 01.25.

Tehtävässä käydään läpi [Salt Projectin (2024a) Windows Package Manageria](https://docs.saltproject.io/en/3002/topics/windows/windows-package-manager.html) keskittyen "Introduction, Install libraries, Populate the local Git repository, Update minion database, Install software package ja Usage osa. Eli sivun alusta kappaleen "Remove a package" loppuun, poislukien "Configuration"". 

[Karvisen (2024)](https://terokarvinen.com/2024/configuration-management-2024-spring/) ohjeessa oli vielä lisäys "Kannatta soveltaa asennuksesta idempotentti versio, ulkomuistista: `sudo salt-call --local -l info state.apply pkg.installed curl`".

Salt tarjoaa normaalin package managerin ominaisuudet paikallisiin ja etähallittaviin  Windows-järjesteliin (asennus, päivitys, poisto ja asennettujen pakettien hallinnan).

Käytössä on YAML/JINJA2-tiedostotyypit (pääte `.sls`) sovelluspakettien hallintaan ja tämä tiedosto sisältää tarvittavat tiedot sovelluspaketin asennukselle, kuten koko nimen, version, latausosoitteen, hiljaiset komentorivioptiot asennukselle ja poistolle sekä valinnan käytetäänkö Windows task scheduleria.

SaltStack ja Salt yhteisö pitävät yllä GitHubissa [salt-win-repo-ng -repositoryä](https://github.com/saltstack/salt-winrepo-ng), jonka voi kloonata itselle. Sitä voidaan käyttää joko Saltin tai Gitin kautta käyttämällä HTTP(S) ja FTP URLeja.

Salt Windows pakacke manageria hallinnoidaan kuten `yum` Linuxissa. Esim. `pkg.install` ja `pkg.installed` ovat käytettävissä.

### Salt Git repository
Jos käytetään Salt Gir repositorya pakettien hallintaan, asennetaan omalle koneelle `GitPython` tai `pygit2`. [Salt Projectin (2024b)](https://docs.saltproject.io/en/3002/topics/tutorials/gitfs.html) mukaan, mikäli molemmat näistä ovat asennettuina, Salt käyttää tällöin ensisijaisesti `pygit2`:sta. Komennon käyttö vaatii Pythonin `pip`-komennon, joten asennetaan se ensin komennolla `sudo apt-get update; sudo apt-get install python3-pip python3-pygit2 -y`.

![Python pip asennus.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-pip.png "Python pip asennus.")

Tarkastetaan asennus komennolla `sudo apt list --installed | grep -e python3-pip -e python3-pygit2` ja se näyttää olen asentuneen.

```
vagrant@host1:~$ sudo apt list --installed | grep -e python3-pip -e python3-pygit2

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

python3-pip/oldstable,now 20.3.4-4+deb11u1 all [installed]
python3-pygit2/oldstable,now 1.4.0+dfsg1-1 amd64 [installed,automatic]
vagrant@host1:~$
```

Python `pip`- ja `pygit2`-asennus onnistuivat normaalisti.

### Lokaali Salt Git repository
Luodaan lokaali repository komennolla `sudo salt-run winrepo.update_git_repos`, mutta se epäonnistuu.

Pitkän tutkinnan jälkeen vika paikantui `git`-komennon puuttumiseen masterilta. Asensin `git`in komennolla `sudo apt-get update; sudo apt-get install git -y` onnistuneesti.

Kokeilin uudelleen asentaa lokaaliin repositoryyn paketit komennolla `sudo salt-run winrepo.update_git_repos` ja se onnistui.

![Winrepo-asennus onnistuu.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-winrepo.png "Winrepo-asennus onnistuu.")

### Päivitä Windows minionien tietokannat
Päivitetään masterin Windows-pakettien tiedot Windows-minioneiden tietokantaan komennolla `sudo salt -G 'os:windows' pkg.refresh_db`.

![Windows-tietokannan päivitys.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-pkg-db-refresh.png "Windows-tietokannan päivitys.")

Tiedot päivittyivät [Salt Projektin (2024c)](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.win_pkg.html#salt.modules.win_pkg.refresh_db) mukaan `C:\ProgramData\Salt Project\Salt\var\cache\salt\minion\files\base\win\repo-ng\salt-winrepo-ng_git`-hakemistoon ja tähän hakemistoon viitataan `sls`-konfigurointitiedostossa `salt://win/repo-ng`.

### Käyttö
Listataan Windows-koneilla olevat asennetut paketit komennolla `sudo salt -G 'os:windows' pkg.list_pkgs`.

![Asennetut paketit Windowsissa.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-win-list_pkgs.png "Asennetut paketit Windowsissa.")

#### Paketin asentaminen Windowsiin masterilta
Testiasennetaan `cpu-z`-sovellus Windowsille komennolla `sudo salt * pkg.install 'cpu-z'`, sillä `curl` ei toimi oikein.

![CPU-Z:n asennus Windowsiin.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-win-inst-cpu-z.png "CPU-Z:n asennus Windowsiin.")

Testaan Windowsista.

![Ajetaan CPU-Z.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-win-run-cpu-z.png "Ajetaan CPU-Z.")

Sovellus toimii.

#### Paketin poisto Windowsista masterilta
Poistetaan aikaisemmin asennettu CPU-Z-paketti komennolla `sudo salt -G 'os:windows' pkg.remove 'cpu-z'`.

![CPU-Z:n poisto Windowsista.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-win-pkg-remove.png "CPU-Z:n poisto Windowsista.")

Sovellus poistui Windowsista normaalisti.

Testasin Windows-pakettien asennuksia useammalla sovelluksella ja niistä suurin osa ei toiminut ainakaan minulla. Esimerkkinä `curl`.

![Curlin:n asennus epäonnistuu Windowsiin.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-inst-curl-fails.png "Curlin:n asennus epäonnistuu Windowsiin.")

## Tehtävä a - Paketti Windowsia
Tehtävä aloitettu 6.5.2024 klo 19.40 ja valmis klo 20.15.

[Tero Karvisen (2018)](https://terokarvinen.com/2018/control-windows-with-salt/) blokikirjoituksessa opastetaan kuinka luodaan tila Windows-koneille.

### Tila Windows-koneille
Luodaan tila Windowsille käyttämällä `pkg.installed`-funktiota.

Luodaan hakemisto tilalle komennolla `sudo mkdir /srv/salt/windows_pkgs`.
Luodaan tilan `init.sls`-tiedosto komennolla `sudo vi /srv/salt/windows_pkgs/init.sls` ja lisätään sinne seuraavat rivit:

```
{% if "Windows" == grains["os"]  %}
windows_pkgs:
  pkg.installed:
    - pkgs:
      - xming
      - cpu-z
{% else %}
others:
  test.succeed_without_changes:
    - name: No changes in Linux machines.
{% endif %}
```

Tiedostossa testataan, onko kohdekone Windows tai muu. Windowsin tapauksessa asennetaan kaksi pakettia (`xming` ja `cpu-z`) ja muussa tapauksessa ei asenneta mitään vaan palautetaan "true"-arvo.

Ajetaan tila kaikille koneille komennolla `sudo salt '*' state.apply windows_pkgs` ja toistetaan sama komento uudelleen, koska ensimmäisellä kerralla ei tullut virhettä.

![Asetetaan tila windows_pkgs.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-a-inst-win-pkgs.png "Asetetaan tila windows_pkgs.")

Koska saman komennon ajo ei muuta tilaa, tila on idempotentti.

Testataan vielä asennetut sovellukset `xming` ja `cpu-z` Windows-koneesta käynnistämällä ne Start-valikon kautta.

![Kuvassa xming.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-a-xming.png "Kuvassa xming.")

![Kuvassa CPU-Z.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-a-cpu-z.png "Kuvassa CPU-Z.")

Eli tilan asennus `pkg.installed`-funktiolla onnistui.

## Tehtävä b - Benchmark
Tehtävä aloitettu 6.5.2024 klo 20.25 ja valmis klo 22.15.

Tehtävänannossa pyydettiin etsimään 3-7 keskitetyn hallinan projekti (esim. tämän kurssin "Oma moduli" lopputyönä). Tarkoituksena on arvioida töitä vain niiden kotisivujen perusteella. Arviointikriteerinä käytetään mitä hyötyä tästä on eli miksi haluaisin asentaa tämän itselleni.

Etsitään ainakin kolme työtä käyttäen Googlea apuna (hakusanat: "tero karvinen oma moduli"):
  * [Sampo Hautalan (2018a) Ph h7 / Oma moduli](https://sampohautala.wordpress.com/2018/12/09/ph-h7-oma-moduli/)
  * [Toni Vapalon (2021a) Oma moduli](https://tonivapalo.com/posts/palvelintenhallinta/phvt7/)
  * [Markus Pyhäranta (2016) Palvelinen hallinta - Oma moduli](https://markuspyharanta.com/2016/12/10/palvelinten-hallinta-oma-moduuli/)

### Benchmark - Sampo Hautalan (2018a/b) Ph h7 / Oma moduli
Sampo Hautala (2018b) tallensi [Salt-tiedostot omaan gittiin](https://github.com/sampohautala/) ja itse [ohjeen toiselle julkaisualustalle](https://sampohautala.wordpress.com/2018/12/09/ph-h7-oma-moduli/).

#### Käyttötarkoitus
Minusta tämä työ helpottaa aloittelevien front-end kehittäjien ja graafisten suunnittelijoiden työtä, sillä se tarjoaa sopivat sovellukset valmiiksi paketoituna Windows- ja Linux-ympäristöön. Nopeuttaa tarvittavien työkalujen asentamista.

#### Lisenssi
Lisenssinä hän käytti GNU General Public License v3.0, joka löytyi [Githubista](https://github.com/sampohautala/salt/blob/master/LICENSE). Tämä lisenssi sallii kaupallisen käytön, muokkaamisen, uudelleen jakelun, patentissa käytön ja yksityisen käytön, mutta käytöstä ei oteta vastuuta (eli omalla vastuulla) eikä työlle anneta takuuta. Tämä työ on lisenssin mukaan vapaa ja tästä muokatut tai edelleen jaetut työt tulee jakaa kuten alkuperäinen softa eli "free software".

#### Tekijä ja vuosi
Tämän työn on tehnyt Sampo Hautala vuonna 2018.

#### Riippuvuudet
Salt-työ on testattu Windows 10 Pro ja Ubuntu 18.04.1 LTS ympäristössä käyttämällä testauksessa selaimena Mozilla Firefox (Firefox Quantum 61.0.1 (64-bit)).

#### Kiinnostavaa
Mielestäni työ antaa hyvän kuvan, kuinka voidaan Saltilla helposti järjestää sopivia valmiita sovelluspaketteja ja helpottaa asennuksien tuskaa.

### Benchmark - Toni Vapalon (2021a/b) Oma moduli
Toni Vapalo (2021a) tallensi [ohjeen julkaisualustalle](https://tonivapalo.com/posts/palvelintenhallinta/phvt7/) ja Salt-tiedostot (Vapalo 2021b) omaan [giittin](https://github.com/Vapalo/vagrant-webdev-salt).

#### Käyttötarkoitus
Mikäli käyttäjä tarvitsee käyttöönsä Python flask ja React -kehitysympäristöt, tällöin hän voisi käyttää tätä apuna.

#### Lisenssi
Lisenssinä hän käytti myös GNU General Public License v3.0, joka löytyi [Githubista](https://github.com/Vapalo/vagrant-webdev-salt/blob/main/LICENSE). Tämä lisenssi sallii kaupallisen käytön, muokkaamisen, uudelleen jakelun, patentissa käytön ja yksityisen käytön, mutta käytöstä ei oteta vastuuta (eli omalla vastuulla) eikä työlle anneta takuuta. Tämä työ on lisenssin mukaan vapaa ja tästä muokatut tai edelleen jaetut työt tulee jakaa kuten alkuperäinen softa eli "free software".

#### Tekijä ja vuosi
Tämän työn on tehnyt Toni Vapalo vuonna 2021.

#### Riippuvuudet
Vagrantin alustaa ei kerrota, mutta oletan sen olevan (Debial) Linux, mutta tarkempaa käyttöjärjestelmäversion ei ole mainittu. Vagrantin avulla luodut virtuaalikoneet käyttävät Debian 10:tä.

#### Kiinnostavaa
Tässä työssä on kiinnostavaa nähdä, kuinka Linuxissa voidaan asentaa virtuaaliympäristöt vagrantin avulla.

### Benchmark - Markus Pyhärannan (2016a/b) Palvelinten hallinta - Oma moduuli
Markus Pyhäranta (2016a) tallensi ohjeen [omalle julkaisualustalle](https://markuspyharanta.com/2016/12/10/palvelinten-hallinta-oma-moduuli/) ja Puppetin ohjelmakoodin [gittiin](https://github.com/PyhaMarkus/puppet-lamp-module).

#### Käyttötarkoitus
Mikäli on tarvetta saada käyttöön LAMP-stackin, tällöin saattaisin harkita tämän käyttöä.

#### Lisenssi
Lisenssinä hän käytti myös GNU General Public License v3.0, joka löytyi [Githubista](https://github.com/PyhaMarkus/puppet-lamp-module/blob/master/LICENSE). Tämä lisenssi sallii kaupallisen käytön, muokkaamisen, uudelleen jakelun, patentissa käytön ja yksityisen käytön, mutta käytöstä ei oteta vastuuta (eli omalla vastuulla) eikä työlle anneta takuuta. Tämä työ on lisenssin mukaan vapaa ja tästä muokatut tai edelleen jaetut työt tulee jakaa kuten alkuperäinen softa eli "free software".

#### Tekijä ja vuosi
Tämän työn on tehnyt Markus Pyhäranta vuonna 2016.

#### Riippuvuudet
Alustana on käytetty Xubuntu 16.04.1 LTS versiota ja sitä on hallittu Puppetilla.

#### Kiinnostavaa
Kiinnostava nähdä myös muita kuin Salt-ratkaisuja.

## Tehtävä c - Testbench. Aja toisen tekemä tila.
Tämä tehtävä on jätetty tekemättä ajanpuutten vuoksi, sillä en ehdi palauttamaan harjoituksia tiistaina ennen deadlineä.

## Tehtävä d - Viisi ideaa
Tehtävä aloitettu 6.5.2024 klo 22.30 ja valmis klo 22.45.

Tehtävänä oli listata viisi ideaa omalle moduulille eli lopputehtävälle.

  1. Windowsin hallinta Saltin avulla esim. omien olemassa olevien ohjelmien automaattinen asentaminen. Haastava omalla koneella.
  2. Käytimme Ohjelmistoprojekti 1 -kurssilla eri tietokantoja ja tiimiläiset käyttivät eri käyttöjärjestelmällisiä koneita (PC ja Mac). Näiden eri tietokantojen käyttöönotoissa oli ongelmia esim. Macin kanssa, joten tätä tuskaa voisi helpottaa. 
  3. Haaga-Helia tarjoaa opiskelijoille jo alkuvaiheessa perussetin työkaluja, joten näiden asentaminen voisi olla automaattista.
  4. Vagrantin ja Saltin käyttö uusissa Maceissä (M1-M3), sillä ainakaan kurssin alussa VirtualBoxiin ei voinut asentaa valmiita boxeja.
  5. Opiskelun päättäneillä on paljon turhia sovelluksia kertynyt koneisiinsa eli kyseessä voisi olla puhdistustyökalu. Ensin kerätään ja sitten kysellään, mitä poistetaan tietokoneesta.

## Lähteet

Hautala, S. 2018a. Ph h7 / Oma moduli. https://sampohautala.wordpress.com/2018/12/09/ph-h7-oma-moduli/.

Hautala, S. 2018b. Github: Salt (Ph h7 / Oma moduli. ). https://github.com/sampohautala/.

Karvinen, T. 2018. Control Windows with Salt. https://terokarvinen.com/2018/control-windows-with-salt/.

Karvinen, T. 2024. Infra as Code - Palvelinten hallinta 2024 https://terokarvinen.com/2024/configuration-management-2024-spring/.

Pyhäranta, M. 2016a. Palvelinten hallinta - Oma moduuli. https://markuspyharanta.com/2016/12/10/palvelinten-hallinta-oma-moduuli/.

Pyhäranta, M. 2016b. GitHub: LAMP module. https://github.com/PyhaMarkus/puppet-lamp-module.

Salt Project. 2024b. Git Fileserver Backend Walkthrough. https://docs.saltproject.io/en/3002/topics/tutorials/gitfs.html.

Salt Project. 2024c. SALT.MODULES.WIN_PKG. https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.win_pkg.html#salt.modules.win_pkg.refresh_db.

Salt Project. 2024a. WINDOWS PACKAGE MANAGER. https://docs.saltproject.io/en/3002/topics/windows/windows-package-manager.html.

SaltStack GitHub. 2024. SaltStack Windows repository. https://github.com/saltstack/salt-winrepo-ng. 

Vapalo, T. 2021a. Oma moduli. https://tonivapalo.com/posts/palvelintenhallinta/phvt7/.

Vapalo. T. 2021b. Github: Vagrant Web dev Salt. https://github.com/Vapalo/vagrant-webdev-salt.
