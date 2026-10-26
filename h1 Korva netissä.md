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
