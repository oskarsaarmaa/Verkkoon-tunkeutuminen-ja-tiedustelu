## a) Omat RFID-tuotteet ja suojautuminen urkinnalta

### Käsitteet:

* RFID (Radio Frequency Identification): Radiotaajuinen etätunnistustekniikka, jossa lukulaite lukee sähkömagneettisten aaltojen avulla dataa fyysisestä luottokortista, tarrasta tai avaimenperästä. RFID-laitteet luokitellaan passiivisiin (saavat virtansa lukulaitteen sähkömagneettisesta kentästä) ja aktiivisiin (oma paristo/virtalähde).
* NFC (Near Field Communication): Lyhyen kantaman (tyypillisesti alle 4 cm) langaton tiedonsiirtotekniikka, joka perustuu 13,56 MHz:n taajuudella toimivaan HF-RFID-tekniikkaan (High Frequency RFID). NFC mahdollistaa kaksisuuntaisen viestinnän laitteiden välillä.
* APDU (Application Protocol Data Unit): ISO/IEC 7816-4 -standardin mukainen tietoyksikkö eli viestimuoto, jota lukulaite ja älykortti (kuten luottokortti, henkilökortti tai e-passi) käyttävät keskenään viestimiseen.
* Skimming / Urkinta: Luvaton datan lukeminen tai etäkloonaus etälukukortista tai RFID-tunnisteesta ilman kortinhaltijan tietoa tai hyväksyntää, kun lukulaite tuodaan kortin lähietäisyydelle.
* Faradayn häkki / RFID-suojaus: Sähköjohdepinta (kuten alumiini tai kupari), joka estää sähkömagneettisten kenttien ja radioaaltojen pääsyn kortin sirulle, estäen siten etäluvun.

### Käytössäni olevat RFID/NFC-tuotteet:

1. Pankkikortti (Lähimaksu): Toimii 13,56 MHz taajuudella (NFC/EMV-standardi).
2. Kuntosalin kulkulätkä: Passiivisia High/Low-Frequency RFID-tunnisteita ovenavaukseen ja leimaukseen.
3. Henkilökortti: Henkilökortti sisältää etäluettavan sirun.

### Suojautumisen taso

* Fyysinen suojaus: Säilytän maksukortteja ja henkiläkorttia lompakossa. Teoriassa 13,56 MHz lähimaksu on luettavissa muutaman senttimetrin etäisyydeltä sopivalla hyökkääjän lukulaitteella tai tehokkaammalla antennilla eli voin olla skimming hyökkäyksen uhrina.
* Pankkikortit: Pelkkä kortin lukeminen etänä ei yleensä riitä suoraan rahan varastamiseen välimieshyökkäyksellä, sillä nykyaikaiset EMV-lähimaksut generoivat jokaiselle maksutapahtumalle yksilöllisen kerta-avaimen (kryptografisen kryptogrammin).
* Henkilökortti: Biometrisen passin ja henkilökortin siru on suojattu BAC- (Basic Access Control) ja EAC- (Extended Access Control) -menetelmillä. BAC estää luvattoman etäluvun suojaten langattoman radiotien salakuuntelulta: lukulaite ei saa luettua sirulta mitään tietoja ilman, että se optisesti lukee (tai muutoin tietää) kortin tietosivun MRZ-koodin (Machine Readable Zone), josta johdetaan lukulaitteen ja sirun välisen viestinnän symmetriset salausavaimet.EAC puolestaan vaatii lukulaitteelta viranomaiskohtaisen digitaalisen sertifikaatin (Terminal Authentication) ja varmistaa sirun aitouden (Chip Authentication), mikä estää sirun kopioinnin sekä sulkee arkaluonteiset biometriset tiedot (kuten sormenjäljet) tavallisilta lukijoilta.

> Oma arvio: Suojaukseni on tasoltaan kohtalainen/riittävä. Koska riski satunnaiselle urkinnalle taskun läpi on matala, enkä jätä korttejani/lompakkoa ilman valvontaa.



## b) APDU-komentojen rakenne

Älykorttien (Smart Card) ja NFC-lukijoiden välinen viestintä perustuu ISO/IEC 7816-4 -standardin määrittämiin APDU-kehyksiin (Application Protocol Data Unit). Viestintä toimii Master-Slave (Master/Replier) -mallilla: lukulaite lähettää komennon (**Command APDU**) ja kortti vastaa siihen (**Response APDU**).

#### 1. Command APDU (Komentokehys: Lukulaite -> Kortti)

| Kenttä | Koko | Kuvaus |
| :--- | :--- | :--- |
| **CLA** (Class) | 1 tavu | Komentoluokka (määrittelee komennon tyypin, esim. standardi ISO tai valmistajakohtainen). |
| **INS** (Instruction) | 1 tavu | Varsinainen komento (esim. `0xA4` tiedoston valintaan, `0xB0` lukuun). |
| **P1, P2** (Parameters) | 2 tavua | Komennon lisäparametrit (esim. muistiosoite tai valintatapa). |
| **Lc** (Length Command) | 0–3 tavua | `Data`-kentän pituus tavuina. |
| **Data** | Vaihteleva | Lähetettävä hyötykuorma (esim. PIN-koodi tai kryptografinen haaste). |
| **Le** (Length Expected) | 0–3 tavua | Odotettavan vastauksen maksimipituus tavuina. |

*Huom: Kentät CLA, INS, P1 ja P2 muodostavat yhdessä pakollisen **Header**-osan, ja Lc, Data sekä Le muodostavat valinnaisen **Body**-osan.*

---

#### 2. Response APDU (Vastauskehys: Kortti -> Lukulaite)

| Kenttä | Koko | Kuvaus |
| :--- | :--- | :--- |
| **Data** | Vaihteleva | Kortin palauttama pyydetty data (esim. tiedoston sisältö tai sertifikaatti). Valinnainen. |
| **SW1, SW2** (Status Words) | 2 tavua | Komennon suorituksen tilakoodi (vastaa HTTP-statuskoodeja):<br>• `0x90 0x00`: Success (Operaatio OK)<br>• `0x63 0xC2`: Virheellinen PIN-koodi / todennus epäonnistunut |


## c) Mielenkiintoiset RFID- ja e-Passi-hakkerointiuutiset sekä haavoittuvuudet

Tutustuin tuoreempiin älykorttiarkkitehtuurien turvallisuustutkimuksiin ja RFID-haavoittuvuuksiin. Erityisen mielenkiintoinen kokonaisuus liittyy ICAO 9303 -standardin mukaisten biometristen passien ja sirukorttien APDU-tason tietoturvatutkimuksiin (kuten Radboud Universiteetin ja Ca' Foscarin turvallisuustutkimuksiin)[cite: 1].

### Havaitut keskeiset hyökkäysvektorit ja tutkimustulokset:

1. **Passien sormenjälkitunnistus (Fingerprinting APDU-vastausten perusteella):** 
   * Vaikka passin siru on suojattu kryptografisesti, eri maiden myöntämät e-passit ja varmenteet vastaavat virheellisiin, puutteellisiin tai tuntemattomiin APDU-komentoihin eri tavoin (palauttaen poikkeavia `SW1-SW2` -statuskoodeja tai virhevirtoja). 
   * Hyökkääjä voi etänä lähettää passille systemaattisia APDU-kyselyitä ja tunnistaa passinhaltijan kansallisuuden tai passityypin pelkän radiotien vasteen perusteella ilman, että itse henkilötietoja tarvitsee purkaa.
2. **Korttien ohjelmistotason PIN- ja avainvuodot (APDU Clear-text leaks):**
   * Joissakin älykorttitoteutuksissa ja varhaisemmissa kryptografisissa middleware-ohjelmistoissa on havaittu haavoittuvuuksia, joissa arkaluonteisia komentoja tai suojaamattomia avaimenvaihtoja on tehty ilman asianmukaista suojakanavaa (*Secure Messaging*). 
   * Mikäli toteutuksessa on virheitä, hyökkääjä voi väliin tulemisen (MitM) tai lähialueen kuuntelun kautta päästä käsiksi väylällä liikkuvaan dataan.

> Infratason pohdinta: Tämä osoittaa sen, että pelkkä radiotietason fyysinen suojaus tai perussalaus (kuten BAC) ei riitä, jos älykortin sisäinen käyttöjärjestelmä ja APDU-komennot vuotavat tila- tai virhekoodeissaan liikaa järjestelmätason tietoa. Turvallisuus vaatii tiukkaa standardien noudattamista ja virheenkäsittelyä, joka ei paljasta kortin sisäistä logiikkaa ulkopuolisille.

## Lähteet:
* Tehtävä ja vinkit: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/
* Radboud Universiteit (ISO/IEC 7816 & e-Passport Research):
  Tutkimusmateriaali ja raportit biometristen passien (e-Passport) ja älykorttien rajapintaturvallisuudesta, mukaan lukien APDU-komentojen sormenjälkitunnistus (*Fingerprinting Passports*). 
  Saatavilla: [Radboud University Digital Security Group](https://cs.ru.nl/E.Poll/papers/nluug.pdf)
* Verkkouutiset (2026): Osa matkailijoista käärii passinsa folioon – tästä syystä. Uutinen passien suojauksesta ja viranomaisohjeistuksesta. Saatavilla: [Verkkouutiset](https://www.verkkouutiset.fi/a/osa-matkailijoista-kaarii-passinsa-folioon-tasta-syysta/)
* Secgroup: APDU-level attacks on crypto tokens. Tutkimus APDU-tason hyökkäyksistä ja PIN/avain-vuodoista älykorteilla. Saatavilla: [Secgroup](https://secgroup.dais.unive.it/projects/apduattacks/)
