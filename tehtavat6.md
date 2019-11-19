---
layout: page
title: Viikko 6
inheader: no
permalink: /tehtavat6/
---

## Viikko 6

<div class="important">
  DRAFT: Erittäin pahasti kesken...
</div>

**HUOM**: [Kurssikoe](https://courses.helsinki.fi/fi/TKT20006/133010615) maanantaina 16.12. 9-12 salissa A111. Kokeeseen tulee ilmoittautua viimeistään 10 päivää ennen kokeen alkua. 

*Alla olevien tehtävien deadline on maanantaina 9.12. klo 23:59*

Apua tehtävien tekoon kurssin [Telegram](https://telegram.me/ohjelmistotuotanto)-kanavalla sekä pajassa
- ma 14-16 B221 
- ke 14-16 B221

Muista myös tämän viikon [monivalintatehtävät](https://study.cs.helsinki.fi/stats/courses/ohtu2019/quiz/6), joiden deadline on sunnuntaina 8.12. klo 23:59:00.  

### Typoja tai epäselvyyksiä tehtävissä?

Tee [korjausehdotus](/osa0#typoja-materiaalissa) editoimalla [tätä](https://github.com/ohjelmistotuotanto-hy/ohjelmistotuotanto-hy.github.io/blob/master/tehtavat6.md) tiedostoa GitHubissa.

### Tehtävien palauttaminen

Tehtävät palautetaan GitHubiin, sekä merkitsemällä tehdyt tehtävät palautussovellukseen <https://study.cs.helsinki.fi/stats/courses/ohtu2019>

Katso tarkempi ohje palautusrepositorioita koskien [täältä](/tehtavat1#teht%C3%A4vien-palautusrepositoriot).

### 1. git: stash [versionhallinta]

_Tätä tehtävää ei palauteta mihinkään!_

Lue [http://git-scm.com/book/en/Git-Tools-Stashing](http://git-scm.com/book/en/Git-Tools-Stashing) kohtaan Un-applying a Stash asti.

Oletetaan että olet repositoriossa, jossa on ainakin kaksi branchia: master ja joku toinen (kutsutaan sitä tässä nimellä __toinen__).

* ollessasi master-branchissa tee branchissa oleviin tiedostoihin muutoksia, joita lisäät staging-alueelle ja joitain muutoksia joita et vielä "äddää", komennon _git status_ tuloksen tulee näyttää siis suunilleen seuraavalta

```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   README.md
    modified:   src/main/java/Main.java

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   src/main/java/Olutvarasto.java              
```
 
* pomosi käskee sinua välittömästi tekemään pari muutosta branchiin __toinen__. Et kuitenkaan halua vielä comittoida masterissa olevia muutoksia
* jos siirryt branchiin __toinen__ tekemättä comittia, tulee hirveä sotku, sillä muutokset pysyvät muutoksina toisessakin branchissa
* **git stash** pelastaa tästä tilanteesta, eli stashaa masterissa olevat muutoset
  * kokeile ennen ja jälkeen stash-komennon komentoa <code>git status</code>
* siirry branchiin toinen, tee sinne joku muutos jonka committaat
* palaa jälleen masteriin
* palauta stashatyt muutokset komennolla <code>git stash apply</code>
  * varmista että muutokset palasivat
  * kuten huomaat, staging-alueelle jo lisätty muutos ei palaa staging-alueelle, vaan joudut lisäämään sen uudelleen
  * jos edellisessä komento olisi annettu muodossa <code>git stash apply --index</code>, olisi tilanne palautunut täysin ennalleen


### 2. Kyselykieli NHLStatistics-ohjelmaan

[Kurssirepositorion](https://github.com/mluukkai/ohjelmistotuotanto8) hakemistosta [koodi/viikko6/QueryLanguage](https://github.com/mluukkai/ohjelmistotuotanto2018/tree/master/koodi/viikko6/QueryLanguage) löytyy jälleen yksi versio tutusta NHL-tilastoja lukevasta ohjelmasta.


Tällä kertaa olemme kiinnostuneita tekemään hieman monimutkaisempia "kyselyjä" pelaajatietoihin, esim. __listaa kaikki joukkueen PHI pelaajat joilla on vähintään 5 maalia ja vähintään 10 syöttöä__.

Koodin onkin luotu hieman valmista kalustoa josta pääset liikkeelle. Edelläolevan kyselyn voi suorittaa seuraavasti:


``` java
public static void main(String[] args) {
    Statistics stats = new Statistics(new PlayerReaderImpl("http://nhlstats-2013-14.herokuapp.com/players.txt"));
 
    Matcher m = new And( new HasAtLeast(5, "goals"),
                         new HasAtLeast(10, "assists"),
                         new PlaysIn("PHI")
    );
 
    for (Player player : stats.matches(m)) {
        System.out.println( player );
    }
}
```

Luokalle __Statistics__ on tehty metodi __matches__, joka palauttaa listan niistä pelaajista, joille parametrina annettu __Matcher__-rajapinnan toteuttava olio palauttaa __true__

Tutustu ohjelman rakenteeseen

* huomioi miten __HasAtLeast__ käyttää Javan ns. reflektio-ominaisuutta kutsuessaan merkkijonoparametria vastaavaa metodia
* toinen huomioinarvoinen piirre on __And__-luokan konstruktorissa käytetty vaihtuvamittainen parametrilista, eli "vararg", ks. lisää esim: https://www.javatpoint.com/varargs

Tee rajapinnan __Matcher__ toteuttavat luokat, joiden avulla voit tehdä operaatiot

* all (tosi kaikille pelaajille)
* not (parameetrina olevan ehdon negaatio)
* or (tosi jollekin ehdolle)
* HasFewerThan (HasAtLeast-komennon negaatio eli, esim. on vähemmän kuin 25 maalia)

Tee erilaisia kyselyjä, ja varmista että uudetkin operaatiot toimivat

Kaikille pelaajille tosi ehto _all_ ei ole vielä tämän tehtävän kannalta kovin mielenkiintoinen, sitä pystyy kuitenkin hyödyntämään seuraavassa tehtävässä.

Seuraavassa muutama esimerkki:

Kyselyn

```java
Matcher m = new Not( new HasAtLeast(1, "goals") );

for (Player player : stats.matches(m)) {
    System.out.println( player );
}
```

vastauksena pitäisi olla vain ne pelaajat (yht 18), joilla ei ole vähintään yht maalia, eli *0 maalia tehneet*. 

Kyselyn

```java
Matcher m = new Or( new HasAtLeast(40, "goals"),
                    new HasAtLeast(60, "assists"),
                    new HasAtLeast(85, "points")
);   
```

tulisi palauttaa seuraava lista

```
Sidney Crosby        PIT          36 + 68 = 104
Ryan Getzlaf         ANA          31 + 56 = 87
Claude Giroux        PHI          28 + 58 = 86
Corey Perry          ANA          43 + 39 = 82
Alex Ovechkin        WSH          51 + 28 = 79
Joe Pavelski         SJS          41 + 38 = 79
Nicklas Backstrom    WSH          18 + 61 = 79
Joe Thornton         SJS          11 + 65 = 76
```

näille siis vähintään yksi _or_-kyselyn kriteereistä on tosi.

Kyselyn

```java
Matcher m = new HasFewerThan(1, "goals");  
```

tulisi palauttaa täsmälleen sama lista kuin ylempänä _not_-matcherin avulla toteutettu kysely niistä pelaajista jotka eivät ole tehneet yhtään maalia.

Kyselyt perustuvat rakenteeltaan __decorator__-suunnittelumalliin, vastaavasti kuten itseopiskelumateriaalin [dekoroitu pino](https://github.com/mluukkai/ohjelmistotuotanto2018/blob/master/web/oliosuunnittelu.md#dekoroitu-pino). __And__- ja __OR__-muotoiset kyseltyt on muodostettu [composite-suunnittelumallin](https://github.com/mluukkai/ohjelmistotuotanto2018/blob/master/web/oliosuunnittelu.md#komposiitti) hengessä, ne ovat __Matcher__-rajapinnan toteuttavia olioita, jotka sisältävät itse monta __Matcher__-olioa. Niiden käyttäjä ei kuitenkaan tiedä sisäisestä rakenteesta mitään.

### 3. Parannettu kyselykieli, osa 1

Matcher-olioiden avulla tehtyä kyselykieltä vaivaa se, että kyselyjen rakentaminen on hieman ikävää, sillä jokaista kyselyn osaa kohti on luotava new-komennolla uusi olio. Tee itseopiskelumateriaalin [pinorakentajan](https://github.com/mluukkai/ohjelmistotuotanto2018/blob/master/web/oliosuunnittelu.md#pinorakentaja) hengessä *kyselyrakentaja*, jonka avulla voit luoda Matcher-olioita.

Rakentaja voi toimia esim. seuraavaan tapaan.

Ensin kysely, joka palauttaa jokaisen pelaajan:

``` java
public static void main(String[] args) {
    Statistics stats = new Statistics(new PlayerReaderImpl("http://nhlstats-2013-14.herokuapp.com/players.txt"));
    
    QueryBuilder query = new QueryBuilder();
    Matcher m = query.build();

    for (Player player : stats.matches(m)) {
        System.out.println( player );
    }
}
```     

Tässä kyselyssä voi ja kannattaa hyödyntää edellisen tehtävän _All_-matcheria.

Seuraavaksi kysely, missä tulostetaan pelaajat, joiden joukkue on NYR

``` java
public static void main(String[] args) {
    Statistics stats = new Statistics(new PlayerReaderImpl("http://nhlstats-2013-14.herokuapp.com/players.txt"));
 
    QueryBuilder query = new QueryBuilder();
 
    Matcher m = query.playsIn("NYR").build();
 
    for (Player player : stats.matches(m)) {
        System.out.println( player );
    }
}
```

Seuraavaksi kysely, missä tulostetaan pelaajat joiden joukkue on NYR, joilla on vähintään 10 ja vähemmän kuin 25 maalia:

``` java
public static void main(String[] args) {
    Statistics stats = new Statistics(new PlayerReaderImpl("http://nhlstats-2013-14.herokuapp.com/players.txt"));
 
    QueryBuilder query = new QueryBuilder();
 
    Matcher m = query.playsIn("NYR")
                     .hasAtLeast(10, "goals")
                     .hasFewerThan(25, "goals").build();
 
    for (Player player : stats.matches(m)) {
        System.out.println( player );
    }
}
```

Peräkkäin ketjutetut ehdot siis toimivat "and"-periaatteella. 

Tässä tehtävässä riittää, että kyselyrakentajasi osaa muodostaa _and_-periaatteella yhdistettyjä ehtoja.

### 4. Parannettu kyselykieli, osa 2

Laajennetaan kyselyrakentajaa siten, että sen avulla voi muodostaa myös _or_-ehdolla muodostettuja kyselyjä. Or-ehdon sisältävä kysely voi olla muodostettu esim. seuraavasti:

``` java
Matcher m1 = query.playsIn("PHI")
                  .hasAtLeast(10, "goals")
                  .hasFewerThan(20, "assists").build();
 
Matcher m2 = query.playsIn("EDM")
                  .hasAtLeast(60, "points").build();
 
Matcher m = query.oneOf(m1, m2).build();
```

Pelaajalistan tulisi olla:

<pre>
Taylor Hall          EDM          27 + 53 = 80
Jordan Eberle        EDM          28 + 37 = 65
Matt Read            PHI          22 + 18 = 40
Vincent Lecavalier   PHI          20 + 17 = 37
</pre>

Tai kaikki sama ilman apumuuttujia:

``` java

Matcher m = query.oneOf(
                        query.playsIn("PHI")
                             .hasAtLeast(10, "goals")
                             .hasFewerThan(20, "assists").build(),
 
                        query.playsIn("EDM")
                             .hasAtLeast(60, "points").build()
                       ).build();
```

Rakentajasi ei ole pakko toimia metodikutsujen syntaksin osalta samalla tavalla. Riittää, että sillä voi jollain tavalla muodostaa _and_- ja _or_-muotoisia kyselyjä.

### 5. Pull request ja refaktorointia (tätä tehtävää ei lasketa versionhallintatehtäväksi)

Isoa projektia on vaikea ylläpitää yksin ja vielä vaikeampaa on löytää oikeat ratkaisut jokaiseen ongelmaan, kun osa-alueitakin rupeaa jo kertymään useita. On vaikeaa olla joka paikan höylä ja jotkin osa-alueet eivät välttämättä edes miellytä ja niihin on siksi vaikea paneutua. Saatat löytää itsesi ajattelemasta vaikkapa: "Lukisipa joku tietorakenteiden asiantuntija tämän osuuden läpi ja tsekkaisi, että HashSet on nyt varmasti se tehokkain ratkaisu...". Ehkäpä et edes ajatellut asiaa, mutta joku silti osoittaa, että puurakenne olisi tässä tehokkaampi ratkaisu. Mokoma tekee vielä korjauksetkin puolestasi lähdekoodiin ja pistää pullrequestin. Onneksi julkaisit projektisi Open Sourcena!

Kontribuutiotasi kaivataan! GitHub on täynnä Open Source -projekteja, jotka kaipaavat panostasi. Mikäs sen kivempaa, kuin käyttää muutama tunti suosikki repositioriosi lähdekoodin parissa ja korvata sieltä huomaamasi tehoton algoritmi mielestäsi paremmalla ratkaisulla. Useilla repositorioilla on valmiit ohjeet contribuuttamiseen Contributing.md:ssä repositorion juuressa. Tässä esimerkiksi bluebird.js:än [CONTRIBUTING.md](https://github.com/petkaantonov/bluebird/blob/master/CONTRIBUTING.md).

Tehtävänäsi on harjoitella contribuuttamista ja vieraan koodin refaktorointia. 
* Valitse yksi ryhmä [miniprojektien](https://studies.cs.helsinki.fi/courses/ohtu2018/projects/repositories) joukosta
* Forkkaa sellaisen ryhmän repositorio, jolla ei ole jo viittä pull requestia. Jos ryhmällä on jo viisi pullrequestia, valitse jokin toinen ryhmä
* Tee uusi branch nimellä "muutoksia"
* Tee luomaasi branchiin "tyhjä" pull request: Eli lisää vaikka yksi tyhjä rivi README.md:hen, pushaa uusi branch GitHubiin ja tee branchista pull request. Tyhjän pullrequestin tarkoituksena on varata sinulle paikka kyseisen repositorion contribuuttajien joukosta. Haluamme, että kaikki ryhmät saavat tasaisesti pullrequesteja ja siksi olemme rajanneet maksimimäärän viiteen. Jos kaikki ryhmät ovat saaneet jo viisi pullrequestia niin valitse joukosta mieleisesi
* Etsi ryhmän lähdekoodista jotain refaktoroitavaa
  * kyseessä ei tarvitse olla iso muutos 
* Refaktoroi ja committaa
* Käy katsomassa tekemääsi tyhjää pullrequestia. Mitä tapahtui?
* [Rebase](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) luomasi branch paikalliseen master branchin päälle. Pushaa. Tapahtuiko pull requestissa muutoksia?
* Otsikoi tekemäsi pullrequest niin, että se kuvaa tekemiäsi muutoksia. Tarkenna otsikon alle mitä teit ja miksi.
* Jos ryhmä pyytää sinua tekemään muutoksia pullrequestiisi, tee tarvittavat muutokset ja committaa. Päivittyikö pullrequest?
* Kun ryhmä on hyväksynyt muutoksesi, voit poistaa luomasi branchin

Laita palautusrepositorioosi tiedosto PULL.md ja sen sisällöksi linkki pullrequestiin.


### Tehtävien palautus

Pushaa kaikki tekemäsi tehtävät GitHubiin ja merkkaa tekemäsi tehtävät palautussovellukseen <https://study.cs.helsinki.fi/stats/courses/ohtu2019>