# h3 Toimiva versio

## Tehtävä x)
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

## Lähteet
  * Chacon, S. & Straub, B. 2014a. Pro Git: 1.3 Getting Started - What is Git?. 2. painos. Luettavissa: https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F
  * Chacon, S. & Straub, B. 2014b. Pro Git: 2.3 Git Basics - Viewing the Commit History. 2. painos. Luettavissa: https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History
