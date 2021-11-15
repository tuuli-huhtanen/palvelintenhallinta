# Palvelinten hallinta - h3: Versionhallinta

Tämä harjoitus on osa Tero Karvisen pitämää Palvelinten hallinta ICT4TN022-3014 -kurssia 
Haaga-Helia ammattikorkeakoulussa syksyllä 2021.

ein harjoituksen maanantaina 15.11.2021. Koneena oli Lenovo Yoga Slim 7 -kannettava 
Windows 11 -käyttöjärjestelmällä ja koneelle asennetun Oracle VirtualBox (6.1) -kautta
Debian 11 “bullseye”.

Harjoitus perustuu Tero Karvisen antamaan ohjeistukseen, joka löytyy 
[kurssisivulta](https://terokarvinen.com/2021/configuration-management-systems-palvelinten-hallinta-ict4tn022-2021-autumn/).
Harjoituksen välivaiheiden tiedot perustuvat Teron Karvisen antamiin 
ohjeistuksiin, ellei lähdettä ole toisin merkitty.

## Tehtävät

z) Lue ja tiivistä artikkeli muutamalla ranskalaisella viivalla. 
Tässä z-alakohdassa ei tarvitse siis tehdä testejä tietokoneella.

* Commonmark contributors: [Markdown Reference](https://commonmark.org/help/) 
(huomaa ainakin otsikot risuaidoilla, kappalejako tyhjällä rivillä,
 sisennys (tab) koodia, lista, linkki, kuva.

Artikkeli käsittelee Markdownin perussyntakseja. Tässä koottuna tiivistetysti
usein käytetyt:

* Lista: `* Lista`
* **Paksunnettu** ja *kursivoitu*: `**Paksunnettu** *Kursivoitu*`
* Otsikot: `# Otsikko 1 ## Otsikko 2 ...`
* Linkki: `[Linkki](http://osoite)`
* Kuva: `![Image](http://kuvanosoite)`
* Sisennetty koodi:`- merkkien väliin.

**a)MarkDown. Tee tämän tehtävän raportti MarkDownina. Helpointa on tehdä raportti 
GitHub-varastoon, jolloin md-päätteiset tiedostot muotoillaan automaattisesti. 
Tyhjä rivi tekee kappalejaon, risuaita ‘#’ tekee otsikon, sisennys merkitsee 
koodinpätkän.**

Loin ensin repositorin 'palvelintenhallinta' Githubiin, johon loin h3-versionhallinta.md -tiedoston. 
Tämän jälkeen loin Debianissa hakemiston `~/palvelintenhallinta`. Ajoin hakemistossa 
komennon `git init`, jolla loin hakemistoon Git-versionhallinnan. 

Olin jo aiemmin asentanut SSH-avainparit, joten loin yhteyden Githubissa sijaitsevan
'palvelinten-hallinta' repositorioon komennolla `git remote add origin git@github.com:tuuli-huhtanen/palvelintenhallinta.git` 

Tämän jälkeen toin Githubissa olevan repositorion komennolla `git pull`, joka
toi hakemistoon Githubista löytyvät `LICENSE, README.md, h3-versionhallinta.md`
-tiedostot. Aloin kirjoittamaan raporttia nanolla komennolla: `nano h3-versionhallinta.md`.

**b) Pull first. Tee useita muutoksia git-varastoosi. Tee muutama muutos, 
jossa yksi commit koskee useampaa tiedostoa. Anna hyvä kuvaukset 
(commit message), yksi englanninkielinen lause imperatiivissa (määräysmuodossa) 
"Add top level menu to Foobar synchronizer"**

