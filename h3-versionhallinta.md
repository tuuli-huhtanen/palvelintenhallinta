# Palvelinten hallinta - h3: Versionhallinta

Tämä harjoitus on osa Tero Karvisen pitämää Palvelinten hallinta ICT4TN022-3014 -kurssia 
Haaga-Helia ammattikorkeakoulussa syksyllä 2021.

Tein harjoituksen maanantaina 15.11.2021. Koneena oli Lenovo Yoga Slim 7 -kannettava 
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
'palvelinten-hallinta' repositorioon komennolla `git remote add origin git@github.com:tuuli-huhtanen/palvelintenhallinta.git`. 

Tämän jälkeen toin Githubissa olevan repositorion komennolla `git pull`, joka
toi hakemistoon Githubista löytyvät `LICENSE, README.md, h3-versionhallinta.md`
-tiedostot. Aloin kirjoittamaan raporttia nanolla komennolla: `nano h3-versionhallinta.md`.

**b) Pull first. Tee useita muutoksia git-varastoosi. Tee muutama muutos, 
jossa yksi commit koskee useampaa tiedostoa. Anna hyvä kuvaukset 
(commit message), yksi englanninkielinen lause imperatiivissa (määräysmuodossa) 
"Add top level menu to Foobar synchronizer"** klo. 19.00

Aluksi vedin Githubista Gitin ajantasalle komennolla `git pull`. 
Tämän jälkeen tein muutoksia tiedostoihin `README.md`ja `h3-palvelintenhallinta.md`. Lisäksi
loin hakemiston `~/palvelintenhallinta/screenshots`.

Muutosten jälkeen syötin komennon `git add --all`. Tarkistin komennolla `git status` miltä tilanne näytti 
Gitissä ennen committia:

![Image](https://raw.githubusercontent.com/tuuli-huhtanen/palvelintenhallinta/main/screenshots/h3_2_b.png)

Kuten kuvasta näkyy, muokkaukset tiedostoihin odottivat committia, mutta luotua hakemistoa ei näkynyt.
Päätin kuitenkin tehdä commitit muokatuille tiedostoille ja pushata ne Githubiin ja sen jälkeen selvittää, miksei hakemisto ilmestynyt status -tietoihin.

Komennolla `git commit` avautui nano, johon kirjoitin mitä olin muokannut. Komennolla 
`git push`pushasin muutokset Githubiin. Kommennolla `git log` tarkastelin edellisiä committeja:

![Image](https://raw.githubusercontent.com/tuuli-huhtanen/palvelintenhallinta/main/screenshots/h3_3_b.png)

Tiedostojen muokkaukset päivittyivät Githubiin, muttei luomani hakemisto. Löysin seuraavan
ohjeen: [Create Folder in GitHub: A Guide](https://careerkarma.com/blog/git-create-folder-in-github/), josta selvisi, että Git ei vie tyhjä hakemistoja kansioina Githubiin, vaan niissä täytyy olla sisältöä. Lisäsin screenshots-hakemistoon vielä tyhjän empty.md-tiedoston, additin, commitin ja pushashin tämän jälkeen hakemiston onnistuneesti Githubiin.

![Image](https://raw.githubusercontent.com/tuuli-huhtanen/palvelintenhallinta/main/screenshots/h3_5_b.png)
_
Kuten ylläolevasta kuvasta huomaa, tyhjän tiedoston lisäyksen jälkeen myös hakemisto ilmeistyi Gittiin työstettäväksi.

**b) Kaikki kirjataan. Näytä omalla git-varastollasi esimerkit komennoista ‘git log’, ‘git diff’ ja ‘git blame’. Selitä tulokset.**

Muokkasin tiedostoa `h3-palvelintenhallinta.md`, jonka jälkeen annoin komennon `git diff`. 
Komento näyttää viimeisimmät muutokset tehdyssä tiedostossa. Alla olevasta kuvasta näkee,
että olen korjannut toisen kappaleen kirjoitusvirheen (lisännyt puuttuva t-kirjaimen) ja lisännyt tekstiä lisää tekstiä alemmaksi:

![Image](https://raw.githubusercontent.com/tuuli-huhtanen/palvelintenhallinta/main/screenshots/h3_6_c.png)

Käytin jo ylemmässä harjoituksessa komentoa `git log`. Löysin samalla toteutuksella olevan [Tuomas Lintulan tekemästä harjoituksesta](https://github.com/tuomaslin/palvelintenhallinta/blob/main/MarkDown.md) komennon 
`git log --pretty=oneline`, jolla saa tehdyt commitit yhdelle riville "siivotummin", joten päätin kokeilla sitä:

![Image](https://raw.githubusercontent.com/tuuli-huhtanen/palvelintenhallinta/main/screenshots/h3_7_c.png)

Huomasin, että komennolla näkyy ilmeisesti vain commitin ensimmäinen rivi, joten toisena ylhäältä olevasta commitista jää puuttumaan toinen rivi, johon kirjoitin muutokset `h3-versionhallinta.md` -tiedostoon.

Tämän jälkeen kokeilin vielä komentoa `git blame`. Tutkin asiaa ja löysin seuraavalta sivulta erilaisia tapoja käyttää komentoa: [Git blame](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-blame).

Kokeilin komentoa `git blame -L 1,10 h3-versionhallinta.md`, joka tuo vain halutut rivit (tässä 1-10) näkyviin.
Komennolla saa selville tehdyt muokkaukset ja sen, kuka ne on tehnyt ja milloin.

![Image](https://raw.githubusercontent.com/tuuli-huhtanen/palvelintenhallinta/main/screenshots/h3_8_c.png)

**c) Huppis! Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia.**

Loin tiedoston tyhmatiedosto.md ja syötin komennon `git add --all` tämän jälkeen status näytti komennolla `git status` seuraavalta:
Statuksesta näin, että uusi tiedosto `tyhmatiedosto.md` odotti commitia. En kuitenkaan halunnut, että tiedosto viedään Gittiin, joten syötin komennon `git reset --hard`, joka tyhjentää kaikki tehdyt muutokset. Komennon jälkeen syöte kertoi, että HEAD -commit on edellinen tekemäni commit. Statusta tarkistaessa `tyhmatiedosto.md` oli myös poistunut odottamasta commitia:

![Image](https://raw.githubusercontent.com/tuuli-huhtanen/palvelintenhallinta/main/screenshots/h3_10_c.png)

**d) Formula. Tee uusi salt-tila (formula, moduli, infraa koodina). (Eli uusi tiedosto esim. /srv/salt/terontila/init.sls). Voit tehdä ihan yksinkertaisen parin funktion (pkg, file...) tilan, tai edistyneemmin asentaa ja konfiguroida minkä vain uuden ohjelman: demonin, työpöytäohjelman tai komentokehotteesta toimivan ohjelman. Käytä tarvittaessa ‘find -printf “%T+ %p\n”|sort’ löytääksesi uudet asetustiedostot.**


