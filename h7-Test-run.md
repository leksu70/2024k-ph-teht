# Testiajo: tehtävä 7 - oma miniprojekti

## 1 Vagrant-asennus

1.1 Avataan komentotulkki. Itse käytän Gitin tarjoamaa bash-komentotulkkia.

1.2 Luodaan hakemisto Vagrantille suorittamalla komento `mkdir /c/leksa/vagrant/Palautus` ja siirrytään sinne komennolla `cd /c/leksa/vagrant/Palautus`.

1.3 Ladataan Vagrantfile Githubista linkistä https://github.com/leksu70/2024k-ph-teht/blob/master/Vagrantfile ja maalaamalla hiirellä koodiosio ja otetaan siitä `CTRL-c`-näppäinyhdistelmällä.

1.4 Avataan editori ja pastetaan aikaisemmin kopioidut tiedot editoriin `CTRL-v`-näppäinyhdistelmällä.

1.5 Talletetaan tiedosto (`File` --> `Save As`) nimellä `/c/leksa/vagrant/Palautus/Vagrantfile`. Siirrytään takaisin komentotulkkiin ja tarkastetaan onko siellä `Vagrantfile`-tiedosto komennolla `ls -l Vagrantfile`.

```shell
leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/Palautus
$ ls -l Vagrantfile
-rw-r--r-- 1 leksa 197610 4331 May 14 00:41 Vagrantfile

leksa@LEKSULA-PC MINGW64 /c/leksa/vagrant/Palautus
$
```

Koska tiedosto on oikeassa hakemistossa, niin voimme jatkaa eteenpäin ja käynnistää virtuaalikoneet.

1.6. Käynnistetään Vagrant komentotulkista komennolla `vagrant up`. Käynnistys on nähtävissä [20240515-Palautus1.md-linkistä](https://github.com/leksu70/2024k-ph-teht/blob/master/20240515-Palautus1.md).


## 2 Salt-asennuksen tarkastus
2.1 Kirjaudutaan toisella komentotulkilla ja siirrytään oikeaan kansioon komennolla `cd /c/leksa/vagrant/Palautus` ja kirjaudutaan `master`-koneeseen komennolla `vagrant ssh master`.
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

2.2 Tarkastetaan Saltin tila, sillä Vagrantin provisiointiskriptit asensivat valmiiksi `salt-master` ja `salt-minion` paketit. Ajetaan komento `sudo salt-key -L`, jotta nähdään ovatko minionit ottaneet yhteyttä masteriin.
```shell
vagrant@master:~$ sudo salt-key -L
Accepted Keys:
Denied Keys:
Unaccepted Keys:
minion
win10
Rejected Keys:
vagrant@master:~$
```
Salt-minionit ovat yrittäneet ottaa yhteyttä masteriin.

2.3 Hyväksytään minioneiden rekisteröinnit komennolla `sudo salt-key -A` ja vastataan `y` hyväksymiskysymykseen.
```sell
vagrant@master:~$ sudo salt-key -A
The following keys are going to be accepted:
Unaccepted Keys:
minion
win10
Proceed? [n/Y] y
Key for minion minion accepted.
Key for minion win10 accepted.
vagrant@master:~$
```

Tarkastetaan vielä, mitkä järjestelmät kuuluvat masterin hallintaan.
```shell
vagrant@master:~$ sudo salt '*' grains.get id
minion:
    minion
win10:
    win10
vagrant@master:~$ sudo salt '*' grains.get osfinger
minion:
    Debian-11
win10:
    Windows-10
vagrant@master:~$
```

## 3 Saltin konfigurointi

3.1 Otetaan käyttöön valmiit konfiguroinnit [Githubista](https://github.com/leksu70/2024k-ph-teht-salt) kloonaamalla sen `git clone https://github.com/leksu70/2024k-ph-teht-salt.git'-komennon avulla ja tarkastetaan, että tiedostot ovat syntyneet oikeaan hakemistoon. Siirrytään git-hakemistoon komennolla `cd 2024k-ph-teht-salt/salt/`.
```shell
vagrant@master:~$ git clone https://github.com/leksu70/2024k-ph-teht-salt.git
Cloning into '2024k-ph-teht-salt'...
remote: Enumerating objects: 36, done.
remote: Counting objects: 100% (36/36), done.
remote: Compressing objects: 100% (23/23), done.
remote: Total 36 (delta 8), reused 29 (delta 4), pack-reused 0
Receiving objects: 100% (36/36), 17.26 KiB | 491.00 KiB/s, done.
Resolving deltas: 100% (8/8), done.
vagrant@master:~$ cd 2024k-ph-teht-salt
vagrant@master:~/2024k-ph-teht-salt$ ll salt
total 24
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 add-users
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 git
-rw-r--r-- 1 vagrant vagrant   90 May 15 11:25 top.sls
-rw-r--r-- 1 vagrant vagrant 1693 May 15 11:25 vscode.sls
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 win-python3_x64
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 win-vscode
vagrant@master:~/2024k-ph-teht-salt$
```
3.2 Poistetaan ensin `/srv/salt`-kansio `sudo rmdir /srv/salt`-komennolla ja linkataan sen haettuun gittiin komennolla `sudo ln -s /home/vagrant/2024k-ph-teht-salt
/salt /srv/salt` ja tarkastetaan, että se näkyy oikein komennolla `ll /srv`.
```shell
vagrant@master:~/2024k-ph-teht-salt$ sudo ln -s /home/vagrant/2024k-ph-teht-salt
/salt /srv/salt
vagrant@master:~/2024k-ph-teht-salt$ ll /srv
total 0
lrwxrwxrwx 1 root root 37 May 15 11:37 salt -> /home/vagrant/2024k-ph-teht-salt/salt
vagrant@master:~/2024k-ph-teht-salt$
```

3.3 Luodaan `/srv/salt/win`-kansio master-koneelle ja kansion ryhmäksi `salt`, sekä `salt`-käyttäjäryhmälle oikeudet kansioon.
```shell
vagrant@master:~/2024k-ph-teht-salt$ mkdir /srv/salt/win
vagrant@master:~/2024k-ph-teht-salt$ sudo chgrp salt /srv/salt/win
vagrant@master:~/2024k-ph-teht-salt$ chmod 775 /srv/salt/win
vagrant@master:~/2024k-ph-teht-salt$ sudo ls -ld /srv/salt/win
drwxrwxr-x 2 vagrant salt 4096 May 15 11:43 /srv/salt/win
vagrant@master:~/2024k-ph-teht-salt$
vagrant@master:~/2024k-ph-teht-salt$ ll /srv/salt/
total 28
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 add-users
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 git
-rw-r--r-- 1 vagrant vagrant   90 May 15 11:25 top.sls
-rw-r--r-- 1 vagrant vagrant 1693 May 15 11:25 vscode.sls
drwxrwxr-x 2 root    salt    4096 May 15 11:40 win
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 win-python3_x64
drwxr-xr-x 2 vagrant vagrant 4096 May 15 11:25 win-vscode
vagrant@master:~/2024k-ph-teht-salt$
```

3.4 Asennetaan `repo`- ja `repo-ng`-repositorit `master`-koneelle komennolloilla `sudo salt-run winrepo.update_git_repos`:
```bash
vagrant@master:~/2024k-ph-teht-salt$ sudo salt-run winrepo.update_git_repos
[WARNING ] Attempt to run a shell command with what may be an invalid shell! Check to ensure that the shell </usr/sbin/nologin> is valid for this user.
...
[WARNING ] Attempt to run a shell command with what may be an invalid shell! Check to ensure that the shell </usr/sbin/nologin> is valid for this user.
https://github.com/saltstack/salt-winrepo-ng.git:
    True
https://github.com/saltstack/salt-winrepo.git:
    True
vagrant@master:~/2024k-ph-teht-salt$
```

3.5 Tarkastetaan löytyykö `vscode.sls`-tiedosto komennolla `ll /srv/salt/win/repo-ng/salt-winrepo-ng/_/vscode.sls` ja korvataan se uudella päivitetyllä versiolla kopioimalla `/srv/salt/vscode.sls` tiedosto `salt-winrepo-ng/_`-kansioon.
```shell
vagrant@master:~/2024k-ph-teht-salt$ ll /srv/salt/win/repo-ng/salt-winrepo-ng/_/vscode.sls
-rw-r--r-- 1 salt salt 1629 May 15 11:48 /srv/salt/win/repo-ng/salt-winrepo-ng/_/vscode.sls
vagrant@master:~/2024k-ph-teht-salt$ sudo cp salt/vscode.sls /srv/salt/win/repo-ng/salt-winrepo-ng/_/vscode.sls
vagrant@master:~/2024k-ph-teht-salt$
```

3.6 Päivitetään Saltin tietokanta (`salt-winrepo-ng`) Windows-minioneille komennolla `sudo salt -G 'os:windows' pkg.refresh_db`.
```shell
vagrant@master:~/2024k-ph-teht-salt$ sudo salt -G 'os:windows' pkg.refresh_db
win10:
    ----------
    failed:
        0
    success:
        312
    total:
        312
vagrant@master:~/2024k-ph-teht-salt$
```

4 Asennetaan hallittavat tilat kaikille minioneille komennolla `sudo salt '*' state.highstate`.

![Salt highstate 1/6](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-highstate-1.png "Salt highstate 1/6")
![Salt highstate 2/6](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-highstate-2.png "Salt highstate 2/6")
![Salt highstate 3/6](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-highstate-3.png "Salt highstate 3/6")
![Salt highstate 4/6](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-highstate-4.png "Salt highstate 4/6")
![Salt highstate 5/6](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-highstate-5.png "Salt highstate 5/6")
![Salt highstate 6/6](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-highstate-6.png "Salt highstate 6/6")

```shell
vagrant@master:~/2024k-ph-teht-salt$ sudo salt '*' state.highstate
minion:
----------
          ID: leos
    Function: group.present
      Result: True
     Comment: Group leos is present and up to date
     Started: 12:13:55.897204
    Duration: 12.169 ms
     Changes:
----------
          ID: leos
    Function: user.present
      Result: True
     Comment: User leos is present and up to date
     Started: 12:13:55.911702
    Duration: 76.154 ms
     Changes:
----------
          ID: git
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 12:13:58.447490
    Duration: 85.832 ms
     Changes:

Summary for minion
------------
Succeeded: 3
Failed:    0
------------
Total states run:     3
Total run time: 174.155 ms
win10:
----------
          ID: leos
    Function: user.present
      Result: True
     Comment: User leos is present and up to date
     Started: 15:14:01.764119
    Duration: 5218.813 ms
     Changes:
----------
          ID: git
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 15:14:07.029386
    Duration: 2234.654 ms
     Changes:

Summary for win10
------------
Succeeded: 2
Failed:    0
------------
Total states run:     2
Total run time:   7.453 s
vagrant@master:~/2024k-ph-teht-salt$
```

Komennon uudelleen suorittaminen ei muuta tiloja, joten tila on idempotentti.

4.2 Asennetaan win10-tila komennolla `sudo salt 'win10' state.highstate`.
```shell
vagrant@master:~/2024k-ph-teht-salt/salt$ sudo salt 'win10' state.highstate
win10:
----------
          ID: leos
    Function: user.present
      Result: True
     Comment: User leos is present and up to date
     Started: 16:45:44.791748
    Duration: 5703.581 ms
     Changes:
----------
          ID: git
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 16:45:50.557468
    Duration: 21968.768 ms
     Changes:
----------
          ID: windows_pkgs
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 16:46:12.526236
    Duration: 155.297 ms
     Changes:
----------
          ID: windows_pkgs_cmds
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 16:46:12.681533
    Duration: 140.664 ms
     Changes:

Summary for win10
------------
Succeeded: 4
Failed:    0
------------
Total states run:     4
Total run time:  27.968 s
vagrant@master:~/2024k-ph-teht-salt/salt$
```

Tila ei muutu, joten tilan on idempotentti.

5 Tarkastus Windows-koneelta.

5.1 Avataan VirtualBox-ikkuna.

![VirtualBox](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-01-vb.png "VirtualBox")

5.2 Valitaan "Palautus_win10..."-virtuaalikone ja painetaa `Show`-ikonia.

![Windows](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-02-win.png "Windows")

5.3 Avataan ja tarkastetaan, että Windowsin Start-valikosta löytyy **Visual Studio Code** ja **Python3**.

![Windows Start-valikko](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-win10-start.png "Windows Start-valikko")

5.4 Käynnistetään vielä Visual Studio Code Start-valikosta.

![VS Code toimii](https://github.com/leksu70/2024k-ph-teht/blob/master/kuvat/h7-test-win-vscode-works.png "VS Code toimii")

6 Sammutetaan virtuaalikoneet oman Windows-koneen komentotulkista komennolla `vagrant halt` ja poistetaan asennus komennolla `vagrant destroy`.