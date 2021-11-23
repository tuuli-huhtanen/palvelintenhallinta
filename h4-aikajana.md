# Palvelinten hallinta - h4: Aikajana

Tämä harjoitus on osa Tero Karvisen pitämää Palvelinten hallinta ICT4TN022-3014 -kurssia
Haaga-Helia ammattikorkeakoulussa syksyllä 2021.

Tein harjoituksen tiistaina 23.11.2021. Koneena oli Lenovo Yoga Slim 7 -kannettava
Windows 11 -käyttöjärjestelmällä ja koneelle asennetun Oracle VirtualBox (6.1) -kautta
Debian 11 “bullseye”.

Harjoitus perustuu Tero Karvisen antamaan ohjeistukseen, joka löytyy
[kurssisivulta](https://terokarvinen.com/2021/configuration-management-systems-palvelinten-hal>
Harjoituksen välivaiheiden tiedot perustuvat Teron Karvisen antamiin
ohjeistuksiin, ellei lähdettä ole toisin merkitty.

## Tehtävät

**a) Captain obvious. Linuxissa on paketinhallinta, joten ohjelmien asentaminen on yksinkertaista. Tee tila, joka asentaa 10 suosikkiohjelmaasi paketinhallinnasta. Tässä a-kohdassa voit jättää ohjelmat oletusasetuksille.**

Harjoituksen alkaessa koneelle oli asennettu Salt, master ja minion. Loin `/srv/salt/kymmenenohjelmanmoduuli/init.sls ` -tiedoston, johon tein seuraavalaisen ohjelmoinnin:

```	
default_10_pkgs:
  pkg.installed:
    - pkgs:
      - thunderbird
      - git
      - pandoc
      - synaptic
      - libreoffice
      - gedit
      - newsboat
      - vlc
      - filezilla
      - calibre
```

Thunderbird on sähköpostiohjelma, Git versionhallinta, Pandocilla voi 
konvertoida tiedostoja eri muotoihin, Synaptic on ohjelma graafisena käyttöliittymänä, jolla voi hallita paketinhallintaa,
Libreoffice on tekstinkäsittelyohjelmisto, Gedit on tekstieditori, Newsboat on uutissyöteohjelma, 
VLC on mediasoitin, Filezilla on tiedonsiirtoon tarkoitettu ohjelma ja Calibre e-kirjojen lukuohjelma.

Ajoin tilan komennolla `sudo salt '*' state.apply kymmenenohjelmanmoduuli` eli kaikille (tässä tapauksessa yhdelle) minioneille.

[!Image](/screenshots/h4_2.png)

Ajo meni onnistuneesti läpi:  `Comment: 10 targeted packages were installed/updated`. Muutoksista näkee yksitellen jokainen asennettu ohjelma ja asennetun ohjelman versio.

**b) CSI Pasila. Tiedostoista saa aikajanan 'cd /etc/; sudo find -printf '%T+ %p\n'|sort|tail'.**

* **Anna esimerkki aikajanasta**

[!Image](screenshots/h4_3.png)

Tulosteesta nähdään: 
* Päivämäärä ja aika, jolloin muutos on tehty
* En ole varma numerosarjasta, onko se muutokselle annettu tunniste (numerosarja)?
* Kohde, jota on muokattu

* **Selitä jokainen kohta komennosta, jolla aikajana tehdään. Vinkki: '%T+' löytyy 'man find' kohdasta printf.**

* `cd /etc/;` siirrytään /etc -hakemistoon, josta löytyy usein ohjelmistojen asennustiedostot
* `sudo` tarvitaan sudo-oikeudet, jotta pystytään tarkastelemaan aikajanaa
* `find -printf` find on komento, jolla etsitään ja -printf on tulostusformaatti, jossa tuloste halutaan
* `'%T+%p\n'` %T tulostaa ajan ja päivämäärän, jolloin tiedostoa on muokattu, %p tulostaa tiedostonimen ja \n tarkoittaa välilyöntiä
* `|sort|tail` sort järjestää päivämäärän mukaan listan niin, että uusin tulee alimmaiseksi ja tail näyttää listauksen 10 viimeisintä riviä

* **Aja jokin komento, joka muuttaa järjestelmän yhteisiä asetustiedostoja**
* **Ota uusi aikajana ja etsi muutos sieltä**
* **Onko samalla hetkellä muutettu yhtä vai useampaa tiedostoa? **

Muutin `/etc/skel/.bashrc` tiedostoa lisäten loppuun `echo "Tervetuloa"`. Lisäyksen on tarkoitus tulostaa "Tervetuloa" -teksti, kun käyttäjä kirjautuu koneelle.

Tämän jälkeen ajoin /etc/ -hakemistossa `sudo find -printf '%T+ %p\n'|sort|tail`ja kun alla olevaa tulostetta vertaa edelliseen esimerkkikuvaan komennon antamasta syötteestä, loppuun ilmestyi tieto tekemästäni muutoksesta.

[!Image](./screenshots/h4_4.png)

Tulosteesta myös näkee, että yhtä tiedostoa on vain muutettu.

**c) Tiedän mitä teit viime kesän^H^H^H komennolla. Säädä jotain ohjelmaa ja etsi sen muuttamat tiedostot aikajanasta. Tee sitten tästä oma Saltin tila.**

Päädyin tekemään muutoksia Nano -tekstieditoriin. Olin aikaisemmin pohtinut, miten saisin wrapattua tekstin ja rivinumerot näkyviin. Löysin seuraavan ohjeen: [Configuring Nano Text Editor with nanorc](https://linuxhint.com/configure_nano_text_editor_nanorc/). Ohjeen mukaisesti muutin tekstin wrappausta, laitoin rivinumerot näkyviin ja lisänä löysin kohdan, jolla tabulaattorin matkan pystyi muuttamaan neljästä välilyönnistä kahteen, jota ajattelin kokeilla kokeilemisen ilosta.

Muutin tiedostoa `/etc/nanorc` johon tein seuraavat muutokset:

``` 
set linenumbers
set softwrap
set tabsize 2
```

Hain komennolla `sudo find -printf '%T+ %p\n'|sort|tail` aikajanan, jonka lopusta huomaa, että tein muutoksia nnanorc -tiedostoon:

[!Image](h4_5.png)

Tämän jälkeen tein muutoksista Salt -tilan. Loin hakemiston `/srv/salt/nanomoduuli`, johon loin `init.sls` -tiedoston, jossa määräsin hakemaan Nanon asetustiedoston `nanorc` Saltista, eli tästä nano -moduulista:

```
 1 /etc/nanorc:
 2   file.managed:
 3     - source: salt://nanomoduuli/nanorc
```

Tämän jälkeen kopioin `/etc/nanorc` tiedoston `/srv/salt/nanomoduuli` -hakemistoon. Koska Nanon asetustiedostooli tällä hetkellä identtinen sekä saltin tilassa, että koneessa, koska kone oli sekä master, että minion, ei tilan ajo näyttäisi mitään muutoksia. Siksi ennen ajoa menin tekemään `/etc/nanorc` tiedostoon muutoksen, ja poistin kohdan `set tabsize 2`. Tämän jälkeen ajoin komennon `sudo salt '*' state.apply nanomoduuli`. 

[!Image](h4_6.png)

Tulosteesta näkee, että muutoksia `/etc/nanorc` -tiedostoon on tehty: `+set tabsize 2`. Eli äsken koneelta poistamani asetus ajettiin takaisin, sillä se löytyi Salt-tilasta.  



**d) Asenna jokin toinen ohjelma asetuksineen.**


