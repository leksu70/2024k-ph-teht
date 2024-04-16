# h3 Toimiva versio

## Tehtävä x
Tehtävänä on lukea ja tiivistää [Chaconin ja Straubin (2014a) esittely Git:stä](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F):
* Git käsittelee tietoja kuin se käsittelisi pienen tiedostojärjestelmän snapshottien sarjoja. Jotta Git olisi tehokas, se ei tallenna kopiota muuttumattomista tiedoista vaan luo linkin edelliseen aikaisemmin tallennettuun tietoon. Gitin tiedot ovat kuin snapshottien sarja.
* Gitin etuihin kuuluu mm. se, että melkein kaikki Gitin tapahtumat ovat paikallisia eli ne tapahtuvat omassa koneessa eikä Gitin palvelin puolen järjestelmässä (remote). Tämän vuoksi Git ei rasita verkkoa eikä palvelimen päätä. Esimerkkinä Git-projektin historian selaaminen: Git hakee tarvittavat tiedot paikallisesta Gitin tietokannasta eikä tähän tarvita muita tietokoneita (esim. palvelinta).
* Jotta Git toimisi kunnolla tarvittaessa myös muiden projektin jäsenten kanssa, oman koneen tulee olla yhteydessä Internettiin, jotta Git pystyy päivittämään yhteisen tietovaraston. Tämä ei kuitenkaan estä omassa koneessa tehtäviä muokkauksia ja päivittämään tietovaraston myöhemmin ajantasalle.
* Git pitää tiedon eheänä, sillä kaikista tiedostoista laskettaa tarkistussummat (checksum, SHA-1 hash, 40 merkkiä pitkä hexadesimaaliluku) ja näihin tiedostoihin viitataan käyttämällä tarkistussummaa. Tämä takaa tiedon tiedon eheyden siirtoyhteydellä ja Git huomaa aina siihen tehdyt muutokset ja tiedoston korruptoimisen.
* Tiedoston voi lisätä Gitin hallintaa `git add tiedosto1`-komennolla.
* Gitin hallitsemat tiedostot ja hakemistot voivat olla kolmessa eri tilassa:
  * Modified: tiedostoa on muokattu, mutta sitä ei ole valittu lisättäväksi paikalliseen Gitin tietokantaan.
  * Staged: tiedosto on lisätty (`git add tiedosto1`) eli valittu seuraavaan snapshottiin ja siitä on nykyinen versio odottamassa seuraavaa valitun/valittujen tiedoston/tiedostojen snapshottia (`git commit`).
  * Commited: tiedosto on onnistuneesti lisätty paikalliseen Gitin tietokantaa.

### Gitin komentosarja
Gitin käyttö sisältää yleensä seuraavan komentojen sarjan: `git add . && git commit; git pull && git push`. Nämä yksittäiset komennot tarkoittavat:
* `git add .`: Git-projektin uudet tai muuttuneet tiedostot lisätään ja valitaan seuraavaan snapshottiin.
* `git commit`: Git-projektin edellisellä lisäyksellä mukaan otetut tiedostot siirretään paikalliseen Gitin tietokantaan.
* `git pull`: Noudetaan Git-projektin muuttuneet tiedostot palvelimen tietovarastosta paikalliseen Gitin tietokantaan. Tällä kurssilla käytämme GitHubia tietovarastona.
* `git push`: Siirretään paikallisen Gitin tietokannassa olevat muutokset palvelimen tietovarastoon.

Olen käyttänyt Gittiä usealla kurssilla, joten nämä tiedot tulevat ulkomuistista.

### Gitin tietovarasto terokarvinen/suolax/ historia
Kloonasin Tero Karvisen suolax-tietovaraston omalle koneelle komennolla
`git clone https://github.com/terokarvinen/suolax.git`. Tutkitaan varaston historiaa komennolla `git log --patch --color | less -R` käänteisessä järjestyksessä eli lopusta alkaen:
* commit ed1835a9cc6f788d676e68ca7522cf2e94a4b359:
  * viimeisimmän muutoksen on tehnyt Tero Karvinen <teropkg@gmx.com>
  * muutos on tehty "Wed Apr 10 20:55:09 2024 +0300" eli silloin on ajettu `git commit -m "Improve usage instructions"`
  * aikaisemmin ajettu `git add .` on löytänyt vain yhden muuttuneen tiedoston `README.md`.
* commit eb5cb455a39a762a9ed10df8f4610400dfd530f8:
  * muutoksen tekijä on Tero Karvinen <teroweb@gmx.com>
  * muutos on tehty "Wed Apr 10 20:04:43 2024 +0300" eli silloin on ajettu `git commit -m "Clean up README.md"`, mutta sitä ennen oli ajettu `git add .`, muuttuneet tiedostot olivat `Makefile` ja `README.md`
* ja niin edelleen.

![Tero Karvisen git-varaston suolax historia.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/suolax.png "git log --patch ---color | less -R")

Lähteenä on käytetty [Chacon & Straubin (2024b) websivuston lukua 2.3](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History).


## Tehtävä a - online
Luotu uusi [2024k-ph-teht-summer-varasto](https://github.com/leksu70/2024k-ph-teht-summer) GitHubiin mukaillen Tero Karvisen (2023) blogitekstiä.
![2024k-ph-teht-summer varasto luotu GitHubiin.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/summer.png "GitHubin varasto 2024k-ph-teht-summer")

## Tehtävä b - online
Kloonattu tehtävän a varasto uuteen 2024k-ph-teht-summer-dolly-hakemistoon.
```
leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant
$ git clone git@github.com:leksu70/2024k-ph-teht-summer.git 2024k-ph-teht-summer-dolly
Cloning into '2024k-ph-teht-summer-dolly'...
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 10 (delta 1), reused 5 (delta 0), pack-reused 0
Receiving objects: 100% (10/10), 13.64 KiB | 229.00 KiB/s, done.
Resolving deltas: 100% (1/1), done.)
```
Tehty muutoksia `README.md`-tiedostoon. Gitin varasto näyttää nyt tältä:
![Päivitetty 2024k-ph-teht-summer-varasto](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/dolly.png "GitHubin Dolly päivitetty varastoon 2024k-ph-teht-summer").

## Tehtävä c - Doh!
Tehty tyhmä muutos 2024k-ph-teht-summer-dolly-hakemistoon ja ajettu peruutuskomento.

```
leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/2024k-ph-teht-summer-dolly (master)
$ git add .

leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/2024k-ph-teht-summer-dolly (master)
$ git reset --hard
HEAD is now at f87fab1 Add tehtävä b - Dolly
```
Huomattavaa on, että kaikki muutokset poistuivat ja tiedoston muutokset poistuivat.

## Tehtävä d - Tukki
Ajettu `git log --patch --color`-komento tutkiakseni `2024k-ph-teht-summer`-varaston historiaa:
* `commit f87fab19a96eda1fa0119bb54b09baf0f5ce04a9`:
  * ajettu komento `git commit -m "Add tehtävä b - Dolly"`
  * muutoksen tekijä: `Leo Sutinen @PC <leo.sutinen@myy.haaga-helia.fi>`
  * muutosajankohta: `Mon Apr 15 22:02:57 2024 +0300`
  * muuttunut tiedosto: `README.md`
  * plus-merkillä alkavat rivit ovat lisäyksiä tiedostoon ja vastaavasti miinus-merkillä alkavat rivit ovat poistoja tiedostosta
  * `@@ -3,5 +3,19 @@` kertoo, että muutokset ovat riveillä 5-19.
* `commit 8d87ad0209543427717d5518167ebb70279ae2b3`
  * ajettu komento `git commit -m "Add lähde"`
  * muutoksen tekijä: `Leo Sutinen @PC <leo.sutinen@myy.haaga-helia.fi>`
  * muutosajankohta: `Mon Apr 15 21:36:22 2024 +0300`
  * `@@ -1,7 +1,7 @@`: muutoksia tehty riville 7.

![Varaston 2024k-ph-teht-summer historia](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/tukki.png "Varaston 2024k-ph-teht-summer historiaa").

# Tehtävä e - Suolattu rakki
Tero Karvisen (2024) tehtävässä pyydettiin ajamaan Salt-tiloja omasta varastostasi. "(Salt tiedostot mistä vain hakemistosta `--file-root teronSaltHakemisto`. Esimerkiksi `sudo salt-call --local --file-root srv/salt/ state.apply`, huomaa suhteellinen polku)".

Loin [uuden varaston `2024k-ph-vko3-teht-e`](https://github.com/leksu70/2024k-ph-vko3-teht-e) GitHubiin [Tero Karvisen (2023)](https://terokarvinen.com/2023/create-a-web-page-using-github/) ohjeita mukaillen.

Loin oman `srv/salt`-hakemistorakenteen, jonne lisäsin `hello`- ja `favourites`-kansiot.
```
vagrant@host1:~/gits/2024k-ph-vko3-teht-e$ tree
.
├── LICENSE
├── README.md
└── srv
    └── salt
        ├── favourites
        │   └── init.sls
        ├── hello
        │   └── init.sls
        └── top.sls

4 directories, 5 files
```

`favourites/init.sls`-tiedoston sisältö:
```
tree:
  pkg.installed

favourite-packages:
  pkg.installed:
    - pkgs:
      - git
      - cowsay
      - cmatrix
      - trash-cli
      - wget
      - curl
      - finger
      - fingerd
```

Ja `hello/init.sls`-tiedoston sisältö:
```
file_helloleo:
  file.managed:
    - name: /tmp/helloleo
    - user: vagrant
    - group: vagrant
    - mode: 640
    - contents:
      - Rivi 1
      - Rivi 2
```

Ajetaan salt-komento vain `hello`:lle: 

```
sudo salt-call --local --file-root srv/salt/ state.apply hello
```

![Salt-komento ajettu.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/vko3-teht-e-hello-apply.png "Ajettu salt-komento.") 

Ajettu komento sudo `salt-call --local --file-root srv/salt/ state.apply`, jolloin myös `favourites` on ajettu.
![Salt-komento ajettu kaikille.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/vko3-teht-e-all-apply.png "Ajettu salt-komento kaikille").

## Lähteet
  * Chacon, S. & Straub, B. 2014a. Pro Git: 1.3 Getting Started - What is Git?. 2. painos. Luettavissa: https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F
  * Chacon, S. & Straub, B. 2014b. Pro Git: 2.3 Git Basics - Viewing the Commit History. 2. painos. Luettavissa: https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History
  * Karvinen, T. 2023. Blogiteksti. https://terokarvinen.com/2023/create-a-web-page-using-github/
  * Karvinen, T. 2024. Infra as Code - Palvelinten hallinta 2024 -kurssi. https://terokarvinen.com/2024/configuration-management-2024-spring/
