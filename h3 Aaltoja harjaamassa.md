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


## d) Too compex 16?

