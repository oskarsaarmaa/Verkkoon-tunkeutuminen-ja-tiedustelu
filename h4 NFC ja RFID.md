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



### b) APDU-komentojen rakenne
