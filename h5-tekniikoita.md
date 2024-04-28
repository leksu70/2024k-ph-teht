# Viikkotehtävä h5 - tekniikoita
Viikkotehvät ovat [Tero Karvisen (2024) Infra as Code - Palvelinten hallinta 2024](https://terokarvinen.com/2024/configuration-management-2024-spring/) kurssilta.

Tehtävät suoritetaan HP EliteBook 850 G3 Windows -koneella, jossa on 32 GB muistia ja Intel Core i5-6200U -prosessori.

Tehtävien teko on aloitettu 28.4.2024 klo 14.00.
## Tehtävä x ON KESKEN XXXXXXXXXXXXXXXXXXXXXXXXXXX!!!!!
## Tehtävä x - aiemman vuoden kotitehtäväraportti

Tehtävä tehty 28.4.2024 klo 16.00-XXXX.

Tehtävänä on vapaavalintainen aiemman vuoden kotitehtäväraportti Saltin käytöstä Windowsilla. Raportteja voi etsiä halukoneen avainsanoilla "salt windows karvinen".

### Salt minionin uudelleen asennus Windowsissa
Poistetaan aikaisemmin asennettu salt-minion Windowsista käyttäen `Windowsin Settings --> Apps & Features` kautta.

### Salt masterin lisäys minionille
Lisätään Salt masterin IP-osoite minionille tiedostoon `C:\ProgramData\Salt Project\Salt\conf` [Salt Project (2024a)]( https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html#install-directory-locations) ohjeen mukaisesti ja lisätään tiedostoon `master: 10.1.0.11`-rivi.

#### Salt-minion-palvelu
Käynnistetään Windowsin Salt-minion-palvelu manuaalisesti PowerShellissä Administrator-käyttäjänä komennolla `Restart-Service Salt-minion -Force`.

#### Salt-minion lisäys masterille
Lisätään uusi Salt-minion (LEKSULA-PC) Salt-serverille komennolla `sudo salt-key -A`.
![Salt-serveri hyväksyy uuden minionin.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-x-salt-key-a.png "Salt-serveri hyväksyy uuden minionin.")

#### Uuden minionin testaus
Testataan, toimiiko uusi minion odotetusti komennolla 
#### Tarkastetaan "Hello World!"

## Tehtävä a - Windows
Tehtävänä on asentaa Salt Windowsille. Salt on asennettu aikaisemmin, joten tarkastetaan asennus komennolla `salt-call --local`.
![Windows Salt local-testi.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-a-salt-call-local.png "Windows Salt local -testi.")
Windowsin Salt toimii normaalisti.

## Tehtävä b - grains.items
Testataan Windows Saltia keräämällä paikalliselta koneelta käytettävän PC:n tuotetiedot ja käyttöjärjestelmä versio.
`salt-call --local grains.item productname osfullname`-komennolla.
![Windows Salt grains-testi.](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h5-b-win-salt-grains.png "Windows Salt grans -testi.")


# Lähteet
Karvinen, T. 2024. Infra as Code - Palvelinten hallinta 2024. https://terokarvinen.com/2024/configuration-management-2024-spring/.

Salt Project. 2024a. Windows, Install directory locations. https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/windows.html#install-directory-locations.