# Viikkotehtävä h5 - tekniikoita
Viikkotehvät ovat [Tero Karvisen (2024) Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/) kurssilta.

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori.

Tehtävien teko on aloitettu 28.4.2024 klo 14.00.

## Tehtävä x - aiemman vuoden kotitehtäväraportti

Tehtävä tehty 28.4.2024 klo 16.00-16.30 ja sitä on jatkettu ongelmaselvityksen jälkeen 29.4.2024 klo 20.15-23.10. 

Tehtävänä on vapaavalintainen aiemman vuoden kotitehtäväraportti Saltin käytöstä Windowsilla. Raportteja voi etsiä halukoneen avainsanoilla "salt windows karvinen".

Tämä tehtävä perustuu [Toni Sepän (2019) h5-tehtävään](https://salthomework.wordpress.com/h5/).

### Salt minionin uudelleen asennus Windowsissa
Poistetaan aikaisemmin asennettu salt-minion Windowsista käyttäen `Windowsin Settings --> Apps & Features` kautta.

### Ladataan vanha Salt-Minion versio
Jotta Salt toimisi oikein, tulee Salt-Minionin olla sama tai aikaisempi versio Salt-Masterista. Masterin versio `sudo salt --version`-komennon mukaan on `salt 3002.6`. Ladataan vastaava [Minionin versio Salt Projektin archivesta](https://archive.repo.saltproject.io/windows/Salt-Minion-3002.6-Py3-x86-Setup.exe) ja asennetaan se normaalisti. Asennusvaiheessa annetaan Salt Masterin IP-osoite `10.1.0.11` ja Minionin nimi `leksulapc`.

Asennus sujuu normaalisti ja palvelu käynnistyy.

### Salt-minion lisäys masterille
Lisätään uusi Salt-minion (leksulapc) Salt-serverille komennolla `sudo salt-key -A` sekä tarkastetaan minionieiden tila `sudo salt test.ping`-komennolla.
![Salt-serveri hyväksyy uuden minionin ja testataan yhteys minionieihin.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-x-salt-key-a.png "Salt-serveri hyväksyy uuden minionin ja testataan yhteys minionieihin.")

### Uuden minionin testaus
Testataan, toimiiko uusi minion odotetusti komennolla `sudo salt 'leksulapc' grains.item osfullname`.
![Testataan yhteys salt-komennolla.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-x-salt-test-new.png "Testataan yhteys salt-komennolla.")

Yhteys Salt-Masterin ja Salt-Minionin välillä toimii normaalisti.

### Huomioita asennuksesta
Aluksi käytin uusinta Salt-Minionin versiota, mutta sen liittäminen vanhempaan versioiseen Salt-Masteriin ei onnistunut. Sain virheestä kiinni, kun käytin loggauksessa debug-tilaa masterilla ja minionilla.

#### Debug-tilat
Sammuta salt-minion-palvelu Windowsista ja käynnistä minioni manuaalisesti PowerShellistä `salt-minion -l debug`. Täältä löytyi ERROR-tason viesti, joka kertoi minionin public key:n käyttämisen epäonnistumisesta.

Tee vastaava operaatio salt-master-palvelulle Linuxissa sammuttamalla palvelu `sudo systemctl stop salt-master` ja käynnistämällä se manuaalisesti `sudo salt-master -l debug`-komennolla.

### Kommentti
Windowsin liittäminen Salt-Masterin alaiseksi ei mennyt ihan helposti, sillä en löytänyt oikeata Salt-Minionin pakettia.

## Tehtävä a - Windows
Tehtävä tehty 28.4.2024 klo 21.00-21.10.
Tehtävänä on asentaa Salt Windowsille. Salt on asennettu aikaisemmin, joten tarkastetaan asennus komennolla `salt-call --local`.
![Windows Salt local-testi.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-a-salt-call-local.png "Windows Salt local -testi.")

Windowsin Salt toimii normaalisti.

## Tehtävä b - grains.items
Tehtävä tehty 28.4.2024 klo 21.10-21.15.
Testataan Windows Saltia keräämällä paikalliselta koneelta tietoja.
`salt-call --local grains.item productname osfullname`-komennolla.
![Windows Salt grains-testi.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-b-win-salt-grains.png "Windows Salt grans -testi.")

Keräsin käytettävän PC:n tuotetiedot ja käyttöjärjestelmä version onnistuneesti.

## Tehtävä c - file-toiminto
Tehtävä tehty 28.4.2024 klo 21.30-22.00.
Tehtävässä pyydetään kokeilla Saltin file -toimintoa Windowsilla.

Luodaan kansio `c:\srv\salt\hellosaltfile`, lisätään sinne `init.sls`-tiedosto käyttäen editoria ja tarkastetaan `init.sls`-tiedoston sisältö.
![Windows Salt file -testi.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-c-salt-file.png "Windows Salt file -testi.")

Ajetaan `salt-call --local state.apply hellosaltfile --file-root c:\srv\salt`-komento.
![Ajetaan saltin tila hellosaltfilelle.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-c-salt-state-apply.png "Ajetaan saltin tila hellosaltfilelle.")

Tarkastettu vielä tiedoston sisältö.
![Luodun tiedoston sisältö.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-c-salt-file-contains.png "Luodun tiedoston sisältö.")

Saltin tila suoritettiin onnistuneesti. Komennon uudelleen ajaminen ei muuta tilaa, joten tämä tila on idempotentti. 

## Tehtävä d - CSI Kerava
Tehtävä tehty 29.4.2024 klo 23.30-00.05.

Tehtävässä tulee näyttää `find`-komennon avulla viimeisimmäksi muokatut tiedostot `/etc/`-hakemistosta ja kotihakemistostasi. Selitä kaikki käyttämäsi parametrit ja format string 'man find' avulla.

### Haetaan viimeksi muokatut tiedostot
Haetaan viimeksi muokatut tiedostot ja hakemistot eri paikoista (`/etc` ja `~/`).

Ajetaan komento `find /etc ~/ -printf '%T+ %p\n' |sort`, jonka tulostus on tämä.
![Find-komennon tulostus.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-d-find.png "Find-komennon tulostus.")

### Find-komento
`find`-komennolla voidaan etsiä tiedostojärjestelmästä eri tietoja. Edellinen komento rakentuu eri osista, joita ovat hakemistopolut (`/etc` eli yksittäinen hakemisto ja `~/` eli kotihakemisto), optio `-printf` ja sen ohjeet, sekä lopuksi aakkostetaan löydetyt polut `sort`-komennon avulla.

Käytettävien `find`-komennon ohjeet löytyy `man find`-komennolla.
![Find-komennon manuaalisivu.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-d-man-find.png "Find-komennon manuaalisivu.")

Komennon ohjeet olivat `%T+ %p\n`, jotka tarkoittavat
- `%T` tiedoston viimeisin muokkauksen ajankohta
- `+` lisätään `+`-merkki päiväyksen ja kellonajan väliin
- `%p` tiedoston koko nimi polun kanssa
- `\n` tehdään rivinvaihto eli erotellaan tiedot yksittäisille riveille.




# Lähteet
Karvinen, T. 2024. Infra as Code - Palvelinten hallinta 2024. https://terokarvinen.com/2024/configuration-management-2024-spring/.

Salt Project. 2024a. Windows, Install directory locations. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html#install-directory-locations.

Seppä, T. 2019. h5. https://salthomework.wordpress.com/h5/.