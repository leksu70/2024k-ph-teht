# Testiajo: tehtävä 7 - oma miniprojekti

## Vagrant

1. Avataan komentotulkki. Itse käytän Gitin tarjoamaa bash-komentotulkkia.

2. Luodaan hakemisto Vagrantille suorittamalla komento `mkdir /c/leksa/vagrant/Palautus` ja siirrytään sinne komennolla `cd /c/leksa/vagrant/Palautus`.

3. Ladataan Vagrantfile Githubista linkistä https://github.com/leksu70/2024k-ph-teht/blob/master/Vagrantfile ja maalaamalla hiirellä koodiosio ja otetaan siitä `CTRL-c`-näppäinyhdistelmällä.

4. Avataan editori ja pastetaan aikaisemmin kopioidut tiedot editoriin `CTRL-v`-näppäinyhdistelmällä.

5. Talletetaan tiedosto (`File` --> `Save As`) nimellä `/c/leksa/vagrant/Palautus/Vagrantfile`. Siirrytään takaisin komentotulkkiin ja tarkastetaan onko siellä `Vagrantfile`-tiedosto komennolla `ls -l Vagrantfile`.

```
leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/Palautus
$ ls -l Vagrantfile
-rw-r--r-- 1 leksa 197610 4331 May 14 00:41 Vagrantfile

leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/Palautus
$
```

Koska tiedosto on oikeassa hakemistossa, niin voimme jatkaa eteenpäin ja käynnistää virtuaalikoneet.

6. Käynnistetään Vagrant komentotulkista komennolla `vagrant up`. Käynnistys on nähtävissä [20240515-Palautus1.md-linkistä](https://github.com/leksu70/2024k-ph-teht/blob/master/20240515-Palautus1.md).

7. Kirjaudutaan toisella komentotulkilla ja siirrytään oikeaan kansioon komennolla `cd /c/leksa/vagrant/Palautus` ja kirjaudutaan `master`-koneeseen komennolla `vagrant ssh master`.
```shell
leksa@LEKSULA-PC MINGW64 /
$ cd /c/leksa/vagrant/Palautus

leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/Palautus
$ vagrant ssh master
Linux master 5.10.0-28-amd64 #1 SMP Debian 5.10.209-2 (2024-01-31) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed May 15 09:07:22 2024 from 10.0.2.2
vagrant@master:~$
```