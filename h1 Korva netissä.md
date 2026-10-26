## x) Spektrini

Sähkömagneettinen spektri kattaa kaikki sähkömagneettisen säteilyn taajuudet ja aallonpituudet. Kaavio havainnollistaa spektrin rakennetta ja keskeisten ominaisuuksien suuntia taajuuden kasvaessa (vasemmalta oikealle: matalasta taajuudesta korkeaan taajuuteen).

### Spektrin yleiskaavio ja ominaisuuksien suunnat

```text
 Taajuus (f)        : Matala -------------------------------------------------> Korkea
 Aallonpituus (λ)   : Pitkä   <------------------------------------------------- Lyhyt
 Antennin koko      : Suuri   <------------------------------------------------- Pieni
 Läpäisevyys        : Heikko  -------------------------------------------------> Vahva (tai riippuu väliaineesta/taajuudesta)
 Tiedonsiirtokyky   : Pieni   -------------------------------------------------> Suuri (laajempi kaista)
-----------------------------------------------------------------------------------------
 Spektrin alueet    : VLF/LF/HF -------- VHF/UHF -------- Mikroaallot ------ Valo / X-säteet

```
### Kiinnostavia sovelluksia

1. FM-yleisradiolaatuiset radiolähetykset (VHF-alue, n. 88–108 MHz)
* Käyttökohde: Jokapäiväinen radiomusiikin ja uutisten kuuntelu autossa tai kotona.
* Ominaisuudet: Käyttää taajuusmodulaatiota (FM), joka tarjoaa hyvän äänenlaadun ja sietää hyvin kohinaa.
  
2. Wi-Fi ja Langattomat verkot (ISM-alueet, 2.4 GHz ja 5 GHz)
* Käyttökohde: Kodin ja yritysten langattomat lähiverkot.
* Ominaisuudet: Korkea taajuus mahdollistaa suuren tiedonsiirtokapasiteetin (megabiteistä gigabitteihin sekunnissa), mutta kantama on lyhyt ja esteet (seinät) vaimentavat signaalia voimakkaasti.

3. Merenkulun ja ilmailun VHF-radiopuhelimet (VHF Marine / Airband, n. 118–137 MHz ja 156–174 MHz)
* Käyttökohde: Lentäjien, lennonjohdon ja alusten välinen puheliikenne.
* Ominaisuudet: Perustuu näköyhteysfysiikkaan (line-of-sight). Signaali ei taivu maapallon kaarevuuden taakse, joten lentokoneilla yhteys kantaa satojen kilometrien päähän korkeuden ansiosta.


Lähde:

[Sähkömagneettinen spektri](https://fi.wikipedia.org/wiki/S%C3%A4hk%C3%B6magneettinen_spektri) (käsitteet, taajuusalueet VLF–EHF ja sovellukset).


## a) WebSDR – Etäkäyttöinen ohjelmaradio

### Tehtävän toteutus

Käytin etäkäyttöistä WebSDR-ohjelmaradiota (Twenten yliopiston [WebSDR-palvelua](http://websdr.ewi.utwente.nl:8901/) Alankomaissa). 

<details>
<summary>Radioliikenne:</summary>

<img width="1120" height="591" alt="image" src="https://github.com/user-attachments/assets/7c9a61f2-da9b-4747-b5d3-06572093fc18" />

</details>

Viritin vastaanottimen lyhytaaltojen (HF) 19 metrin broadcast-alueelle taajuudelle 15.39 MHz (15392.60 kHz). Valitsin modulaatiotavaksi AM (Amplitude Modulation), joka on kyseisen taajuusalueen ja yleisradiolähetysten standardi.

### Havainnot

Sivuston vesiputousnäytöltä erottui selvä energiahuipouma, ja signaalin voimakkuudeksi (S-meter) mitattiin noin -70.3 dBm. Koska lähetin sijaitsi kaukana Alankomaissa, signaali kantautui perille tyypillisenä ionosfäärin kautta kulkevana avaruusaaltona (skywave). Kun ääni kytkettiin päälle, taajuudella kuulunut yleisradiolähetys kuului selkeästi selaimen kaiuttimista.

## b) Moduwhat? 

### Tehtävän toteutus

Edellisessä kohdassa (a) käytin AM (Amplitude Modulation) -modulaatiota yleisradiolähetyksen kuunteluun. Vertailun vuoksi vaihdoin modulaatiotavaksi USB (Upper Sideband) -tilan, joka on erikoistunut SSB-modulaatiomuoto (Single Sideband).

Kuuntelin signaalia samalla WebSDR-vastaanottimella vaihtaen modulaatiopainikkeen AM-tilasta USB-tilaan.


<details>
<summary>Testitulos</summary>

<img width="1216" height="451" alt="image" src="https://github.com/user-attachments/assets/8eaf1518-990e-4c5e-8c79-4af54e32ebd5" />


</details>

### Havainnot

* Löysin / Kuuntelin CHN China Radio int.  
* Kuunteluhavainto USB-tilassa: Koska USB (Single Sideband) on tarkoitettu kapeammalle kaistalle ja vaatii tarkan taajuussovituksen, puhe- tai musiikkilähetys kuulosti vääristyneeltä, ikään kuin "kuristuneelta" tai metalliselta verrattuna perus-AM-lähetykseen.


## c) ADS-B – Lentoliikenteen seuranta

### Käsitteet:

* ADS-B (Automatic Dependent Surveillance-Broadcast): Lentokoneiden automaattinen ja avoin seurantajärjestelmä. Lentokone lähettää jatkuvasti ulos radiolähettimellään omaa GPS-sijaintiaan, barometrista lentokorkeuttaan, nopeuttaan, suuntaansa sekä yksilöllistä ICAO-koodiaan avoimella 1090 MHz radiotaajuudella.
* Miten tiedot saadaan: Kuka tahansa voi vastaanottaa näitä radiosanomia pystyttämällä edullisen RTL-SDR-vastaanotintikun ja sopivan antennin (tai hyödyntämällä yhteisöjen ylläpitömiä vastaanottajaverkkoja, kuten ADS-B Exchange). Palvelut keräävät hajautetusta verkojärjestelmästä tulevan datan ja piirtävät sen reaaliaikaiseen verkkokarttaan ilman kaupallisia suodattimia tai piilotuksia.

### Tehtävän toteutus ja havainnot

Avasin selaimessa [ADS-B Exchange](https://www.adsbexchange.com/) -palvelun ja valitsin tarkasteluun suomalaisen matkustajakoneen.

* Valittu kohde: Finnairin käyttämä potkuriturbiinikone, rekisteritunnukseltaan OH-ATI (malli ATR-72-500, ICAO-tyyppikoodi `AT75`, Hex-koodi `4601F6`).
* Havainnot tiedoista ja kartalta:
   * Koneen tiedot: Palvelun kautta näkyivät koneen tarkat tekniset tiedot, kuten 89 jalan pituus ja siipiväli.
   * Reitti ja liike: Karttanäytöltä ja seurannasta pystyi reaaliaikaisesti havainnoimaan koneen lentorataa, sen käyttämää radiotaajuutta (1090 MHz) sekä lennon aikaista nopeutta ja korkeutta, jotka kone lähettää automaattisesti transponderillaan suoraan ilmaan.


## Lähteet

[Sähkömagneettinen spektri](https://fi.wikipedia.org/wiki/S%C3%A4hk%C3%B6magneettinen_spektri) 

[WebSDR](http://websdr.org/)

[Lentokoneliikenne](https://www.adsbexchange.com/)

[Tero Karvinen kurssisivu](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/)
