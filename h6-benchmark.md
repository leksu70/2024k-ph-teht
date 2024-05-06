# Tehtävä h6 - Benchmark
Nämä tehtävät kuuluvat Tero Karvisen kurssille [Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/).

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori. Tässä vielä Salt versiot minioneilta ja masterilta.

Käytetyt Saltin versiot (3006.8) komennolla `sudo salt '*' grains.item saltversion`.
![Salt versiot.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-salt-versions.png "Salt versiot.")

Tehtävien teko on aloitettu 5.5.2024 klo 20.30 ja päätetty XX.2024 klo **XXXX**.

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


## Lähteet

Karvinen, T. 2024. [Infra as Code - Palvelinten hallinta 2024 https://terokarvinen.com/2024/configuration-management-2024-spring/.

Salt Project. 2024b. Git Fileserver Backend Walkthrough. https://docs.saltproject.io/en/3002/topics/tutorials/gitfs.html.

Salt Project. 2024c. SALT.MODULES.WIN_PKG. https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.win_pkg.html#salt.modules.win_pkg.refresh_db.

Salt Project. 2024a. WINDOWS PACKAGE MANAGER. https://docs.saltproject.io/en/3002/topics/windows/windows-package-manager.html.

SaltStack GitHub. 2024. SaltStack Windows repository. https://github.com/saltstack/salt-winrepo-ng. 

