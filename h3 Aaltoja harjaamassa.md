## x) Lue ja tiivistä

### Hubacek 2019: Universal Radio Hacker SDR Tutorial

* Esittelee 433 MHz pistorasioiden (ASK/OOK) kaappaamisen ja analysoinnin URH-ohjelmalla.
* Kaapatusta aallonmuodosta määritetään kohinataso (noise threshold) ja demoduloidaan signaali raakabiteiksi.
* Demoduloiduista biteistä havainnoidaan toistuva kaava (preamble + datapaketti) sekä näppäinpainallusten väliset erot.


### Cornelius 2022: Decode 433.92 MHz weather station data

* Käsittelee sääaseman ulkoanturin lähettämän 433,92 MHz ASK/OOK-signaalin purkamista.
* Signaalista tunnistetaan pulssinleveysmodulaatio (PWM/PPM), jossa lyhyt pulssi vastaa nollaa ja pitkä pulssi ykköstä.
* Esittää protokollan purkamisen vaiheet käsin aina bittijonosta lämpötila- ja kosteusarvojen binäärimuunnokseen asti.


### Lohner 2019: Decoding ASK/OOK_PPM Signals

* Tarkastelee bittitason pulssipaikkamodulaation (PPM) purkamista rtl_433 joustavilla säännöillä (-X kytkin).
* Demoduloinnissa huomioidaan signaalin esitiedot (preamble) ja pulssien väliset ajastukset täsmällisen decoder-profiilin luomiseksi.


## b) rtl_433

Asensin `rtl_433`-työkalun ympäristöön lataamalla valmiin käännetyn binäärin suoraan viralliselta GitHub-releases-sivulta:

<details>
<summary>Asennus</summary>

<img width="894" height="369" alt="image" src="https://github.com/user-attachments/assets/bd8c705d-2710-4ebe-9885-2b85c1fc3ac5" />

</details>

```bash
# Riippuvuuksien asennus ja pöivitys

sudo apt-get update

sudo apt-get -y install atool wget libssl-dev libtool libusb-1.0-0-dev librtlsdr-dev rtl-sdr libsoapysdr-dev

```


```bash
# Paketin lataus

wget https://github.com/merbanan/rtl_433/releases/download/25.02/rtl_433-soapysdr-openssl3-Linux-amd64-25.02.zip

```

<details>
<summary>Testaus</summary>

<img width="2215" height="98" alt="image" src="https://github.com/user-attachments/assets/fab00304-8ec0-4e04-933d-03317c8fda97" />

</details>

### Mitä tietoja ajosta saadaan:

* Ohjelmaversio: Työkalusta on käytössä versio 25.02
* Tuetut syötteet ja kirjastot: Käännös tukee tiedostosyötteitä, `rtl_tcp`-verkkovälitystä, `RTL-SDR`-laitteita sekä `SoapySDR`-rajapintaa TLS-salauksella.
  

## c) Automaattinen analyysi: Converted_433.92M_2000k.cs8

Latasin tiedoston [Terokarvinen.com:sta](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/) ja ajoin automaattisen protokollanpurun syöttämällä kaapatun näytetiedoston `rtl_433`-työkalulle:

```bash

./rtl_433 -r Converted_433.92M_2000k.cs8

```

<details>
<summary>Testitulos</summary>

<img width="984" height="840" alt="image" src="https://github.com/user-attachments/assets/0ef34f98-f62c-44ca-ac52-cfe9bbab2e34" />

</details>

### Mitä näytteessä tapahtuu ja mitä tunnisteita löytyy?

* Signaalin toisto: Radiosignaalin lähetys toistuu tiedostossa 4 eri kertaa aikaleimoilla `@0.083284s`, `@0.0163125s`, `@0.242956s` ja `@0.383568s`.
* Monitasoinen yhteensopivuus: `rtl_433` purkaa saman koodin kolmella eri profiililla (KlikAanKlikUit-Switch, Proove-Security ja Nexa-Security). Tämä johtuu siitä, että kyseiset tuotemerkit käyttävät samaa yhteensopivaa 433,92 MHz:n RF-protokollaa
* Laitetunniste (id / House Code): `8785315`.
* Tapahtuma / Komento (Command / State): Off / OFF (Sammutuskomento).
* Kanava ja Yksikkö (Channel / Unit): Channel 3, Unit 3.
* Ryhmäkutsu (Group / Group Call): 0 / No


## d) Too complex 16? (complex16s -> rtl_433)

Tallennetut näytteet ovat `.complex16s`-muodossa, koska `rtl_433` ei lue tiedoston sisäisiä otsikkotietoja (headereitä), sille on ilmoitettava keskitaajuus ja näytteenottotaajuus suoraan tiedoston nimessä. [Merbanan](https://github.com/merbanan/rtl_433)

Kopioin `rtl_433`-ohjelman odottamaan muotoon:

```bash
cp Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s Recorded_433.92M_2000k.cu16

```

Ajan nyt uudelleen nimetyn tiedoston:

```bash
./rtl_433 -r Recorded_433.92M_2000k.cu16

```

<details>
<summary>Testitulos</summary>

<img width="746" height="80" alt="image" src="https://github.com/user-attachments/assets/3165097e-a778-4539-9533-d5c9c1a3ccb3" />

</details>

### Miksi mitään tulosta ei näy?

* `rtl_433` käsittelee tiedoston sisällön reaaliajassa sen simuroidun keston mukaan. Jos tiedosto on pitkä tai siinä on tyhjää kohinaa ennen varsinaista radiosignaalin pursketta, ruudulle ei tulostu mitään ennen kuin ohjelma löytää ja kykenee purkamaan tunnistettavan paketin.
* Tuloste on täysin normaali. Ohjelma toimii halutulla tavalla ja lukee tiedostoa offline-tilassa. Mikäli tiedostossa on mukana tuettu protokolla ja radiosignaali, dekoodatut tiedot ilmestyvät ruudulle ohjelman edetessä. 


## e) Ultimate

Ultimate Radio Hacker on työkalu radiosignaalien sieppaukseen, demodulointiin ja analysointiin. Yritin asentaa perinteisesti apt install avulla mutta pakettia ei löydetty. Ihmettelin hetken ja löysinkin vinkeistä, että kannattaa asentaa `pipx` avulla. [Terokarvinen.com](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/)

Ajoin seuraavat komennot:

```bash
sudo apt update
sudo apt install -y pipx
pipx install urh
pipx ensurepath

```

<details>
<summary>Asennus</summary>

<img width="1087" height="749" alt="image" src="https://github.com/user-attachments/assets/3f784ac1-d456-40f2-9c11-df13af55c99c" />

</details>

## f) Yleiskuva näytteestä

Tiedostonimi `1-on-on-on-HackRF-20250412_113805-433_912MHz-2MSps-2MHz.complex16s` paljastaa suoraan tarvittavat tekniset metatiedot:

* Keskitaajuus: 433,912 MHz
* Näytteenottotaajuus: 2 MSps (2 000 000 näytettä sekunnissa)
* Tallennusaika: 12.4.2025 klo 11:38:05


## g) Bittistä

 Latasin ` 1-on-on-on-HackRF-20250412_113805-433_912MHz-2MSps-2MHz.complex16s.` tiedoston [Terokarvinen.com](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/). 


<details>
<summary>URH tulos</summary>

<img width="2546" height="796" alt="image" src="https://github.com/user-attachments/assets/51868726-08a0-4ead-95f7-5d62ee138da6" />

</details>

URH-ohjelmassa (`Interpretation`-näkymä) suoritin signaalin demoduloinnin ja bittien tulkinnan:

* Oikea modulaatio: Signaalille valitsin ASK (Amplitude Shift Keying / OOK).
* Ajoitus: Näytteenottotaajuudella 2 MSps (2 000 000 näytettä sekunnissa) ja asetuksella `Samples/Symbol = 500` yhden raakabitin kesto ajassa lasketaan seuraavasti:

Aika = 500 näytettä / 2 000 000 näytettä/s = 0,00025 s = 250 µs

* Yhden raakabitin kesto: Yhden raakabitin pituus on tarkalleen 250 µs.
* 250 mikrosekuntia on neljäsosa millisekunnista. Esimerkiksi ihmisen silmänräpäys kestää tyypillisesti noin 100–400 millisekuntia – kaukosäätimen yksittäinen raakabitti on siis noin tuhat kertaa nopeampi kuin yksi silmänräpäys.

Lähde:

Tehtävän tekoon käytin [hubmartin](https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s) loistavaa videoa.


Katsoin [Nurminen 2025](https://github.com/nurminenkasper/Verkkoon-tunkeutuminen-ja-tiedustelu/blob/main/h3/h3-Aaltoja-harjaamassa.md) raporttia.



## Lähteet

Karvinen, Tero (2026): Kurssin materiaalit, harjoitustehtävänanto ja vinkit. Saatavilla: https://terokarvinen.com.


Pohl, Johannes: Ultimate Radio Hacker (URH) – Investigate, Analyze, Modify and Create Radio Frequencies. GitHub-repositorio ja virallinen dokumentaatio. Saatavilla: https://github.com/jopohl/urh


Merbanan et al.: rtl_433: Program to decode radio transmissions from devices like remote controls using a RTL2832U. GitHub-repositorio. Saatavilla: https://github.com/merbanan/rtl_433


Hubmartin video: Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs. Katsottavissa: https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s


Cornelius 2022: Decode 433.92 MHz weather station data: Luettavissa: https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html

Lohner 2019: Decoding ASK/OOK_PPM Signals with URH and rtl_433: Luettavissa: https://github.karllohner.com/SDR/Decoding/Example_2019-01-24/



