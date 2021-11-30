# Palvelinten hallinta - h5: Demonit

Tämä harjoitus on osa Tero Karvisen pitämää Palvelinten hallinta ICT4TN022-3014 -kurssia Haaga-Helia ammattikorkeakoulussa syksyllä 2021.

Tein harjoituksen maanantaina 29.11.2021. Koneena oli Lenovo Yoga Slim 7 -kannettava Windows 11 -käyttöjärjestelmällä ja koneelle asennetun Oracle VirtualBox (6.1) -kautta Debian 11 “bullseye”.

Harjoitus perustuu Tero Karvisen antamaan ohjeistukseen, joka löytyy [kurssisivulta](https://terokarvinen.com/2021/configuration-management-systems-palvelinten-hallinta-ict4tn022-2021-autumn/). Harjoituksen välivaiheiden tiedot perustuvat Teron Karvisen antamiin ohjeistuksiin, ellei lähdettä ole toisin merkitty.


**a) Watch it! Asenna Saltilla SSHD asetuksineen. Lisää ssh-demonille ylimääräinen portti. Osoita testein, että uusi portti toimii. Vaihda porttia masterilla olevasta asetustiedoston mallista (/srv/salt/sshd/sshd_config tms), aja tila orjille, ja osoita testein, että palvelin käynnistyy jos ja vain jos asetustiedoston malli masterilla on muuttunut. Vinkki: Karvinen 2018: Pkg-File-Service – Control Daemons with Salt – Change SSH Server Port**

ALoitin harjoituksen luomalla `/srv/salt/ssh` -hakemiston. Loin hakemistoon `init.sls` tiedoston, johon loin seuraavanlaisen ohjelmoinnin:

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

Minulla oli openssh jo asennettuna koneelle, joten testasin sitä komennolla `ssh tuuli@localhost` ja sain vastaukseksi:



**b) Securerer shell. Tee Saltin kautta jokin muu sshd:n asetus ja osoita, että se toimii. Siivoa sshd_configista turhat kommenttirivit. Laita tiedostoon kommentti, jossa varoitetaan, että tiedosto ylikirjoittuu automaattisesti. Liitä siivottu sshd_config vastaukseesi.**

**c) Maailman suosituin. Tee Salt-tila (eli formula, siis omaa infraa koodina), joka asentaa Apache-weppipalvelimen. Korvaa testisivu /var/www/html/index.html sivulla, jossa lukee vain "Hello".**

**d) Minä ja kissani. Lisää Apache-reseptiisi (siihen Saltilla kirjoittamaasi) tuki käyttäjien kotisivuille. Voit laittaa kotisivut päälle 'a2enmod userdir', ottaa /etc/-tiedostoista aikajanan ja tehdä tarvittavat symlinkit file.symlink.**

**e) Valmiiseen pöytään. Tee käyttäjille valmiit esimerkkikotisivut siten, että esimerkkikotisivu syntyy käyttäjää luodessa. Katso, että sivuille tulee oikea omistaja. Vi

nkki: /etc/skel/ kopioidaan luoduille käyttäjille. Kotisivuja etsitään osoitteesta /home/tero/public_html/index.html.**

**f) Mootorix. Tee Salt-tila, joka asentaa Nginx-weppipalvelimen ja tekee sille jonkin asetuksen. Vinkki: ensin käsin, vasta sitten automaattisesti. Tässä tehtävässä osa haastetta on selvittää, miten nginx otetaan käyttöön.**
