# h3 Toimiva versio

## Tehtävä x)
Tehtävänä on lukea ja tiivistää [Chaconin ja Straubin esittely Git:stä](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F):
* Git käsittelee tietoja kuin se käsittelisi pienen tiedostojärjestelmän snapshottien sarjoja. Jotta Git olisi tehokas, se ei tallenna kopiota muuttumattomista tiedoista vaan luo linkin edelliseen aikaisemmin tallennettuun tietoon. Gitin tiedot ovat kuin snapshottien sarja.
* Gitin etuihin kuuluu mm. se, että melkein kaikki gitin tapahtumat ovat paikallisia eli ne tapahtuvat omassa koneessa eikä gitin palvelin puolen järjestelmässä (remote). Tämän vuoksi git ei rasita verkkoa eikä palvelimen päätä. Esimerkkinä git-projektin historian selaaminen: Git hakee tarvittavat tiedot paikallisesta gitin tietokannasta eikä tähän tarvita muita tietokoneita (esim. palvelinta).
* Jotta git toimisi kunnolla tarvittaessa myös muiden projektin jäsenten kanssa, oman koneen tulee olla yhteydessä Internettiin, jotta git pystyy päivittämään yhteisen tietovaraston. Tämä ei kuitenkaan estä omassa koneessa tehtäviä muokkauksia ja päivittämään tietovaraston myöhemmin ajantasalle.
* Git pitää tiedon eheänä, sillä kaikista tiedostoista laskettaa tarkistussummat (checksum, SHA-1 hash, 40 merkkiä pitkä hexadesimaaliluku) ja näihin tiedostoihin viitataan käyttämällä tarkistussummaa. Tämä takaa tiedon tiedon eheyden siirtoyhteydellä ja git huomaa aina siihen tehdyt muutokset ja tiedoston korruptoimisen.
* Tiedostoja voi lisätä gitin hallintaa `git add .`-komennolla.
* Gitin hallitsemat tiedostot ja hakemistot voivat olla kolmessa eri tilassa:
  * Modified: tiedostoa on muokattu, mutta `git commit`-komentoa ei ole ajettu eikä se ole siirtynyt paikalliseen gitin tietokantaan.
  * Staged: tiedosto on commitoitu ja siitä on nykyinen versio odottamassa
  * Commited: 


## Lähteet
  * Chacon, S. & Straub, B. 2014. Pro Git: 1.3 Getting Started - What is Git?. 2. painos. Luettavissa: https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F
