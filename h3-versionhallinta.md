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

<h3_2_b.png>

Kuten kuvasta näkyy, muokkaukset tiedostoihin odottivat committia, mutta luotua hakemistoa ei näkynyt.
Päätin kuitenkin tehdä commitit muokatuille tiedostoille ja pushata ne Githubiin ja sen jälkeen selvittää, miksei hakemisto ilmestynyt status -tietoihin.

Komennolla `git commit` avautui nano, johon kirjoitin mitä olin muokannut. Komennolla 
`git push`pushasin muutokset Githubiin. Kommennolla `git log` tarkastelin edellisiä committeja:

<h3_3_b.png>

Tiedostojen muokkaukset päivittyivät Githubiin, muttei luomani hakemisto. Löysin seuraavan
ohjeen: [Create Folder in GitHub: A Guide](https://careerkarma.com/blog/git-create-folder-in-github/), josta selvisi, että Git ei vie tyhjä hakemistoja kansioina Githubiin, vaan niissä täytyy olla sisältöä. Lisäsin screenshots-hakemistoon vielä tyhjän empty.md-tiedoston, additin, commitin ja pushashin tämän jälkeen hakemiston onnistuneesti Githubiin.


**b) Kaikki kirjataan. Näytä omalla git-varastollasi esimerkit komennoista ‘git log’, ‘git diff’ ja ‘git blame’. Selitä tulokset.**

**c) Huppis! Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia.**

**d) Formula. Tee uusi salt-tila (formula, moduli, infraa koodina). (Eli uusi tiedosto esim. /srv/salt/terontila/init.sls). Voit tehdä ihan yksinkertaisen parin funktion (pkg, file...) tilan, tai edistyneemmin asentaa ja konfiguroida minkä vain uuden ohjelman: demonin, työpöytäohjelman tai komentokehotteesta toimivan ohjelman. Käytä tarvittaessa ‘find -printf “%T+ %p\n”|sort’ löytääksesi uudet asetustiedostot.**
