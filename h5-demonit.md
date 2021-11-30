# Palvelinten hallinta - h5: Demonit

Tämä harjoitus on osa Tero Karvisen pitämää Palvelinten hallinta ICT4TN022-3014 -kurssia Haaga-Helia ammattikorkeakoulussa syksyllä 2021.

Tein harjoituksen tiistaina 30.11.2021. Koneena oli Lenovo Yoga Slim 7 -kannettava Windows 11 -käyttöjärjestelmällä ja koneelle asennetun Oracle VirtualBox (6.1) -kautta Debian 11 “bullseye”.

Harjoitus perustuu Tero Karvisen antamaan ohjeistukseen, joka löytyy [kurssisivulta](https://terokarvinen.com/2021/configuration-management-systems-palvelinten-hallinta-ict4tn022-2021-autumn/). Harjoituksen välivaiheiden tiedot perustuvat Teron Karvisen antamiin ohjeistuksiin, ellei lähdettä ole toisin merkitty.

* **Master -koneena seuraavissa harjoituksissa on VirtualBoxissa pyörivä Debian 11 (debian1)**
* **Minion -koneita on 2: yksi Debian 11 (debian2) ja yksi Ubuntu server 20.40.03 LTS (ubuntus1)**

**a) Watch it! Asenna Saltilla SSHD asetuksineen. Lisää ssh-demonille ylimääräinen portti. Osoita testein, että uusi portti toimii. Vaihda porttia masterilla olevasta asetustiedoston mallista (/srv/salt/sshd/sshd_config tms), aja tila orjille, ja osoita testein, että palvelin käynnistyy jos ja vain jos asetustiedoston malli masterilla on muuttunut. Vinkki: Karvinen 2018: Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port**

Aloitin harjoituksen luomalla `/srv/salt/ssh` -hakemiston master -koneelle, eli debian1:seen. Loin hakemistoon `init.sls` tiedoston, johon loin seuraavanlaisen ohjelmoinnin:

```
$sudoedit /srv/salt/ssh/init.sls
  
 1 openssh-server:
 2   pkg.installed
 3
 4 /etc/ssh/sshd_config:
 5   file.managed:
 6     - source: salt://ssh/sshd_config
 7
 8 sshd:
 9   service.running:
10     - enable: True
11     - watch:
12       - file: /etc/ssh/sshd_config

```

* Rivit 1-2: asennetaan openssh-server demoni
* Rivit 4-6: rivillä 4 on miniolle tarkoitettu polku tiedostolle, joka kopioidaan polusta, joka on rivillä 6. Rivillä 6 oleva polku tiedostolle `sshd_config`, joka  sijaitsee `/srv/salt/ssh/`-hakemistossa, eli Saltin "juuressa". Eli käytännössä luodaan konfiguraatiotiedosto sshd:lle, joka sijoitetaan Saltin juureen, josta se kopioidaan ajossa halutuille minioneille. 
* Rivit 8-12: Varmistetaan, että demoni on päällä. Rivi 8 sshd tarkoittaa ssh-demonia.Rivillä 10 `service.running`, rivillä 10 `enable: True` varmistaa, että demoni on päällä. Rivillä 11 `watch` tarkoittaa, että se seuraava rivi 12 `file:`seuraa rivillä 4 olevaa komentoa, eli tässä tapauksessa sitä, missä Saltin sshd:lle tarkoitettu konfiguraatiotiedosto sijaitsee.

Minulla oli openssh jo asennettuna masterille, joten testasin sitä komennolla `ssh tuuli@localhost` ja sain vastaukseksi:

```
tuuli@debian1:~$ ssh tuuli@localhost
tuuli@localhost's password: 
Linux debian1 5.10.0-9-amd64 #1 SMP Debian 5.10.70-1 (2021-09-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Nov 30 10:14:19 2021 from ::1
```

Eli ssh toimi master -koneessa. Seuraavaksi hain masterilta ssh:n asetustiedoston polusta `/etc/ssh/sshd_config`. En ollut tehnyt siihen vielä muutoksia. Kopioin master -koneella olevan asetustiedoston sellaisenaan Saltin juureen komennolla `sudo cp /etc/ssh/sshd_config /srv/salt/ssh/`. Seuraavaksi avasin kopioidun tiedoston `/srv/salt/ssh/sshd_config` ja vaihdoin sshd:n portin oletusportista 22 porttiin 4578:

![Img](screenshots/H5_1.png)

Tämän jälkeen tallensin `/srv/salt/ssh/sshd_config` -tiedoston. Tässä vaiheessa kopioin master -koneella olevan `/etc/ssh/sshd_config` -tiedoston backupiksi, sillä seuraavaksi kokeilin ajaa Saltin ssh-moduulin ensin masterilla katsoakseni, miten se toimii ennen minioneille ajoa. Eli tein seuraavan komennon: `cp /etc/ssh/sshd_config /etc/ssh/sshd_config_backup` ja tarkistin, että kopio löytyy halutusta kohdehakemistosta. Tämän jälkeen ajoin Salt-tilan paikallisesti pelkästään masterilla komennolla:

```
tuuli@debian1:~$ sudo salt-call --local state.apply ssh
```

![Img](screenshots/H5_2.png)

Syötteestä näin, että `Succeeded: 3 (changed=2)` eli ajo meni onnistuneesti läpi ja muutoksia tuli kaksi. Tila ei asentanut openssh-serveriä, sillä se oli jo asennettuna masterilla. Sen sijaa se oli poistanut portin 22 ja lisännyt portin 4578 tiedostoon `/etc/ssh/sshd_config`. Lisäksi demoni oli käynnistetty uudelleen `ID: sshd`-kohdassa. 

Seuraavaksi kokeilin toimivuutta vielä järjestelmässä. Tein ensin masterilla testin uudelleen, jossa hain ilman spesifioitua porttia ssh-yhtyettä localhostiin: 

```
tuuli@debian1:~$ ssh tuuli@localhost
ssh: connect to host localhost port 22: Connection refused
```

Eli oletusporttiin 22 ei saatu yhteyttä. Seuraavaksi kokeilin äsken asettamallani uudella portilla:

```
tuuli@debian1:~$ ssh tuuli@localhost -p 4578
tuuli@localhost's password: 
Linux debian1 5.10.0-9-amd64 #1 SMP Debian 5.10.70-1 (2021-09-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Nov 30 10:37:24 2021 from ::1
```

Eli porttiasetukset olivat menneet läpi. Halusin kuitenkin, että master -kone käyttää oletusporttia, joten kopioin backup -tiedoston takaisin: `sudo mv /etc/ssh/sshd_config_backup /etc/ssh/sshd_config`. Käynnistin ssh:n uudelleen järjestelmästä ja testasin jälleen uutta ssh:n porttia ja nyt portti oli jälleen oletus 22.

Seuraavaksi ajoin tilan minioneille. Minionit ubuntus1 ja debian2 oli "tyhjiä", joten niille en ollut asentanut muuta kuin salt-minionin ja luonut yhteyden masteriin. Ensin testasin yhteyden masterilta minioneille:

```
tuuli@debian1:~$ sudo salt '*' test.ping
debian2:
    True
ubuntus1:
    True
```

Eli yhteys oli voimassa. Seuraavaksi ajoin tilan kaikille minioneille komennolla:

```
tuuli@debian1:~$ sudo salt '*' state.apply ssh --state-output=terse
```

Kohta `--state-output=terse` tiivistää komennosta tulevaa syötettä.

![Img](screenshots/H5_3.png)

Syötteestä näin, että puhuin palturia aikaisemmin. Ilmeisesti ubuntus1:sessä olikin openssh-server asennettuna, sillä muutoksia tuli vain kaksi (olisi pitänyt tulla kolme, eli myös asentaa ssh). Debian2:seen sen sijaan tuli kolme muutosta, eli kaikki kohdat suoritettiin. Seuraavaksi testasin yhteyttä masterilta. Ensin hain minioneiden IP-osoitteet:

```
tuuli@debian1:~$ sudo salt '*' cmd.run 'hostname -I'
debian2:
    192.168.1.190 fda0:d807:620f:f800:abea:f895:8599:6455 2001:14bb:440:2c0:d403:755e:96e2:b4fe
ubuntus1:
    192.168.1.186 fda0:d807:620f:f800:a00:27ff:fe7a:1b2b 2001:14bb:440:2c0:a00:27ff:fe7a:1b2b
```

Tämän jälkeen kokeilin suoraan muutettuun porttiin ja IP-osoitteeseen ssh-yhteyksiä

```
tuuli@debian1:~$ ssh tuuli@192.168.1.190 -p 4578
tuuli@debian1:~$ ssh tuuli@192.168.1.186 -p 4578
```

Ei onnistunut. Nopeasti tajusin, että kyse on todennäköisesti palomuurista. Olin asentanut palomuurit minioneihin päälle alkukonfiguraatiossa. Ajattelin harjoituksen vuoksi kokeilla, saisinko suoraan masterilta muutettua minioneiden palomuuriasetuksia menemättä minioneille avaamaan ssh:lle asettamaani uutta porttia. Löysin [Teemu Karhusen tekemän harjoituksen](https://teemukarhunencom.wordpress.com/2020/12/14/h7-oma-moduli/), jota käytin pohjana seuraavaan.

Tarkistin, miltä masterin porttitilanne näytti:

```
tuuli@debian1:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                  
4505                       ALLOW       Anywhere                  
4506                       ALLOW       Anywhere                  
22/tcp (v6)                ALLOW       Anywhere (v6)             
4505 (v6)                  ALLOW       Anywhere (v6)             
4506 (v6)                  ALLOW       Anywhere (v6) 
```

22/tcp on ssh:n oletusportti ja portit 4505-4506 ovat Saltin käytössä. Päätin avata myös hetkellisesti masterilla portin 4578, jotta saisin asetustiedostot kopioitua uuteen Salt -tilaan. Palomuuriasetuksien tiedostot ja mitä muokataan:

```
/etc/ufw/ufw.conf # Määrittää, onko palomuuri päällä vai ei
/etc/ufw/user.rules # IPv4 palomuurisäännöt
/etc/ufw/user6.rules #IPv6 palomuurisäännöt
```

Koska olin muokkaamassa sääntöjä, otin backupkopioit tiedostoista `/etc/ufw/user.rules`ja `/etc/ufw/user6.rules`. Tämän jälkeen poistin masterilla Saltin käyttämät portit ja 22/tcp:n ja hyväksyin portin 4578:

![Image](screenshots/H5_4.PNG)

Ensin tarkistin porttitilanteen, jonka jälkeen resetoin koko palomuurin. Ilmeisesti järjestelmä tekee itsekin varmuuskopiot tässä vaiheessa (ylläolevassa kuvassa). Tämän jälkeen tarkistin, ettei portteja ollut ja järjestelmä ilmoittaa, ettei tulimuuri ole päällä. Tämän jälkeen päivitin portin 4578/tcp ja laitoin palomuurin päälle, jonka jälkeen tutkin tilannetta. Nyt asetukset ovat sellaiset, mitkä haluaisin ajaa minioneille. Käytin apuna tässä kohtaa ohjetta: [How to Delete rules in UFW Ubuntu Firewall using ufw delete command](https://www.configserverfirewall.com/ufw-ubuntu-firewall/ufw-delete-rule/). 

Seuraavaksi teen Salt-tilan polkuun `/srv/salt/ufw/` ja kopioin alla olevat tiedostot. Lisäksi päivitin backup-tiedostot masterille ja tarkistin, että vanhat porttiasetukset tulivat voimaan takaisin:

```
tuuli@debian1:~$ sudo mkdir -p /srv/salt/ufw
tuuli@debian1:~$ sudo cp /etc/ufw/ufw.conf /srv/salt/ufw/
tuuli@debian1:~$ sudo cp /etc/ufw/user.rules /srv/salt/ufw/
tuuli@debian1:~$ sudo cp /etc/ufw/user6.rules /srv/salt/ufw/
tuuli@debian1:~$ sudo cp /etc/ufw/user.rules_backup /etc/ufw/user.rules
tuuli@debian1:~$ sudo cp /etc/ufw/user6.rules_backup /etc/ufw/user6.rules
tuuli@debian1:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere                  
4505                       ALLOW       Anywhere                  
4506                       ALLOW       Anywhere                  
22/tcp (v6)                ALLOW       Anywhere (v6)             
4505 (v6)                  ALLOW       Anywhere (v6)             
4506 (v6)                  ALLOW       Anywhere (v6)    
```

Lopuksi kirjoitin `/srv/salt/init.sls` tiedoston: (Ohjelmoinnin lähde: [Teemu Karhusen tekemää moduuli](https://teemukarhunencom.wordpress.com/2020/12/14/h7-oma-moduli/), ohjelmointi on sama, sillä siinä on kaikki mitä tarvitsen tässä harjotuksessa).

```
 1 /etc/ufw/ufw.conf:
 2   file.managed:
 3     - soruce: salt://ufw/ufw.conf
 4
 5 /etc/ufw/user.rules:
 6   file.managed:
 7     - source: salt://ufw/user.rules
 8
 9 /etc/ufw/user6.rules:
10   file.managed:     
11     - source: salt://ufw/user6.rules
12
13 ufwservice:
14   service.running:
15     - name: ufw
16     - watch: 
17       - file: /etc/ufw/ufw.conf
18       - file: /etc/ufw/user.rules
19       - file: /etc/ufw/user8.rules
```

* Rivi 1: Lukee tiedoston, josta palomuuri laitetaan päälle
* Rivi 5: Lukee tiedoston, jossa on palomuurisäännöt IPv4
* Rivi 9: Lukee tiedoston, jossa on palomuurisäännöt IPv6
* Rivi 13: Katsoo, että palomuuri on päällä ja tarkkailee ID:tä riviltä 1, 5 ja 9.

Ensin ajoin paikallisesti:

![Image](screenshots/H5_6.png)

Yksi muutos ei mennyt läpi. `Name: ufw - Function: service.running - Result: Failed Started`, eli tila ei saanut käynnistettyä palomuuria. Lisäksi alussa on varoitus: `[WARNING ] State for file: /etc/ufw/ufw.conf - Neither 'source' nor 'contents' nor 'contents_pillar' nor 'contents_grains' was defined, yet 'replace' was set to 'True'. As there is no source to replace the file with, 'replace' has been set to 'False' to avoid reading the file unnecessarily.` Hetken tarkasteltuani huomaan kirjoitusvirheen rivillä 3 = `- soruce: salt://ufw/ufw.conf`. Korjasin kirjoitusvirheen ja mietin, mikä ongelma service.running:issa voisi olla. Nyt init.sls -tiedosto ei esim. asenna palomuuria, vaan päivittää vain säännöt. Lisäksi `sudo ufw status` komennolla saa vastauksen `inactive`, jos palomuuri ei ole päällä. Koska tämä harjoitus alkoi paisua vähän muualle, päätin jättää asian tulevaisuudessa tutkittavaksi ja poistin service.running -kohdan ja jätin tiedoston niin, että se vain päivittää säännöt, jotta pääsin harjotuksessa eteenpäin. Lopullinen asetustiedosto:

```
 1 /etc/ufw/ufw.conf:
 2   file.managed:
 3     - source: salt://ufw/ufw.conf
 4
 5 /etc/ufw/user.rules:
 6   file.managed:
 7     - source: salt://ufw/user.rules
 8
 9 /etc/ufw/user6.rules:
10   file.managed:
11     - source: salt://ufw/user6.rules
```

Ajoin tilan paikallisesti ja kaikki ok. Tarkistin vielä masterilla:

```
tuuli@debian1:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
4578/tcp                   ALLOW       Anywhere                  
4578/tcp (v6)              ALLOW       Anywhere (v6)             

```

Tässä välissä palautin jälleen backupit masterille, tarkistin tilanteen ja seuraavaksi ajoin minioneille:

![Image](H5_7.png)

Ei mennyt läpi. Enää ei ollut ollenkaan yhteyttä minioneihin muutenkaan, testasin komennolla `salt '*' test.ping`. 

Päätin käsin mennä debian2:selle, konfiguroida portit ja kokeilla ssh-yhteyttä, jotta saisin loputkin harjoitukset tehtyä:

![Image](screenshots/H5_8.png)

Tämän jälkeen sain ssh-yhteyden debian2:seen:

```
tuuli@debian1:~$ ssh tuuli@192.168.1.190 -p 4578
tuuli@192.168.1.190's password: 
Linux debian2 5.10.0-9-amd64 #1 SMP Debian 5.10.70-1 (2021-09-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Nov 29 21:44:24 2021 from 192.168.1.189
tuuli@debian2:~$ 
```

**b) Securerer shell. Tee Saltin kautta jokin muu sshd:n asetus ja osoita, että se toimii. Siivoa sshd_configista turhat kommenttirivit. Laita tiedostoon kommentti, jossa varoitetaan, että tiedosto ylikirjoittuu automaattisesti. Liitä siivottu sshd_config vastaukseesi.**

Tässä vaiheessa harjoitusta en saanut enää yhteyttä minioneihi, joten päätin palata harjoituksen alussa olevaan master-koneeseen ja minion-koneeseen debian2 (en ollut epähuomiossa ottanut ubuntus1- snapshottia, joten jätin sen pois jatkosta).



**c) Maailman suosituin. Tee Salt-tila (eli formula, siis omaa infraa koodina), joka asentaa Apache-weppipalvelimen. Korvaa testisivu /var/www/html/index.html sivulla, jossa lukee vain "Hello".**

**d) Minä ja kissani. Lisää Apache-reseptiisi (siihen Saltilla kirjoittamaasi) tuki käyttäjien kotisivuille. Voit laittaa kotisivut päälle 'a2enmod userdir', ottaa /etc/-tiedostoista aikajanan ja tehdä tarvittavat symlinkit file.symlink.**

**e) Valmiiseen pöytään. Tee käyttäjille valmiit esimerkkikotisivut siten, että esimerkkikotisivu syntyy käyttäjää luodessa. Katso, että sivuille tulee oikea omistaja. Vinkki: /etc/skel/ kopioidaan luoduille käyttäjille. Kotisivuja etsitään osoitteesta /home/tero/public_html/index.html.**

**f) Mootorix. Tee Salt-tila, joka asentaa Nginx-weppipalvelimen ja tekee sille jonkin asetuksen. Vinkki: ensin käsin, vasta sitten automaattisesti. Tässä tehtävässä osa haastetta on selvittää, miten nginx otetaan käyttöön.**
