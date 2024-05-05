# Tehtävä h6 - Benchmark
Nämä tehtävät kuuluvat Tero Karvisen kurssille [Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/).

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori. Tässä vielä Salt versiot minioneilta ja masterilta.

![Salt versiot.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-salt-versions.png "Salt versiot.")

Tehtävien teko on aloitettu 5.5.2024 klo 20.30 ja päätetty XX.2024 klo XXXX.

## Tehtävä x - Lue ja tiivistä
Tehtävä aloitettu 5.5.2024 klo 21.30 ja valmis klo XXXX.

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
![Windows repon asennus epäonnistuu.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-winrepo-fails.png "Windows repon asennus epäonnistuu.")

Pitkän tutkinnan jälkeen vika paikantui `git`-komennon puuttumiseen masterilta. Asensin `git`in komennolla `sudo apt-get update; sudo apt-get install git -y` onnistuneesti.

Kokeilin uudelleen asentaa lokaaliin repositoryyn paketit komennolla `sudo salt-run winrepo.update_git_repos` ja se onnistui.

![Winrepo-asennus onnistuu.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h6-x-winrepo.png "Winrepo-asennus onnistuu.")

## Lähteet

Karvinen, T. 2024. [Infra as Code - Palvelinten hallinta 2024 https://terokarvinen.com/2024/configuration-management-2024-spring/.

pygit2. pygit2 - libgit2 bindings in Python. https://pypi.org/project/pygit2/.


Salt Project. 2024b. Git Fileserver Backend Walkthrough. https://docs.saltproject.io/en/3002/topics/tutorials/gitfs.html.

Salt Project. 2024a. WINDOWS PACKAGE MANAGER. https://docs.saltproject.io/en/3002/topics/windows/windows-package-manager.html.

SaltStack GitHub. 2024. SaltStack Windows repository. https://github.com/saltstack/salt-winrepo-ng. 

