## x) Lue ja tiivistä

### Wireshark - Getting Started [Karvinen 2025](https://terokarvinen.com/wireshark-getting-started/):
* Wireshark on tehokas graafinen pakettianysaattori (packet analyzer), jolla voidaan kaapata ja tarkastella verkkoliikennettä pakettitasolla.
* Ohjelman käyttö vaatii usein pääkäyttäjän oikeudet (tai sopivat ryhmäoikeudet), jotta verkkokorttia voidaan käyttää promiscuous-tilassa kaiken verkkoliikenteen kuunteluun.
* Peruskäytössä suodattimet (Display Filters, esim. `http` tai `ip.addr == 1.1.1.1`) ovat välttämättömiä, jotta valtavasta tietomäärästä saadaan suodatettua esiin vain olennainen liike.

### Network Interface Names on Linux [Karvinen 2025](https://terokarvinen.com/network-interface-linux/):
* Modernit Linux-jakelut käyttävät ennakoivia verkkorajapintojen nimiä (Predictable Network Interface Names), jotka perustuvat laitteen fyysiseen sijaintiin tai tyyppiin (esim. `enp0s3` tai `eth0`).
* Rajapintojen tilaa ja IP-osoitteita hallitaan nykyään ensisijaisesti `ip`-komennolla (`ip a`, `ip link set ...`), vanhojen `ifconfig`-komentojen sijaan.
* Aktiivisen verkkorajapinnan tunnistaminen on ensimmäinen askel missä tahansa verkon vianmäärityksessä tai liikennetarkastelussa.

## a) Linux
Ei ongelmia, käytän Kali Linuxia.

## b) Ei voi kalastaa

### 1. Verkkorajapinnan tunnistaminen

Ennen rajapinnan sulkemista selvitin aktiivisen verkkorajapinnan nimen `ip`-komennolla:

```bash
$ ip a
```

<details>
<summary>ip a:</summary>

<img width="837" height="422" alt="image" src="https://github.com/user-attachments/assets/67891b3a-aeb0-4a5e-932b-df282745c2d8" />

</details>

Tulosteesta tunnistin tarvittavat rajapinnat:

* `lo`: Paikallinen looppirajapinta (`127.0.0.1`).
* `eth0`: Pääasiallinen ulkoverkon rajapinta, jolle oli dynaamisesti jaettu IP-osoite `10.0.2.15/24`.
* `eth1`: Toinen aktiivinen rajapinta (`192.168.56.101/24`).
  
### Yhteyden katkaiseminen

Ajoin seuraavan komennon rajapinnan deaktivoimiseksi:

```bash
$ sudo ip link set eth0 down

```
> Komennon jälkeen verkkokortti asetettiin ohjelmallisesti pois päältä. Testasin yhteyden toimivuutta ping-komennolla. Saatu virheilmoitus Network is unreachable vahvistaa, että paketteja ei voi enää reitittää ulkoverkkoon.

<details>
<summary>sudo ip link set eth0 down:</summary>

<img width="334" height="99" alt="image" src="https://github.com/user-attachments/assets/33038468-6327-40a1-919b-bfc8f2c25810" />

</details>


### Yhteyden palautaminen 

Palauttaakseni verkkoyhteyden takaisin toimintaan ajoin komennon:

```bash
$ sudo ip link set eth0 up

```
> Varmistin vielä uudella ping-kutsulla, että verkkopaketit kulkevat taas normaalisti ja internet-yhteys on palautunut ennalleen.

<details>
<summary>$ sudo ip link set eth0 up:</summary>

<img width="505" height="252" alt="image" src="https://github.com/user-attachments/assets/c2a9252c-c4c7-4358-a1a7-b35c395432d4" />

</details>

## c) Wireshark

Koska käytössä on turvallisuustestaukseen tarkoitettu Kali Linux -virtuaalikone, Wireshark löytyy järjestelmästä valmiiksi asennettuna. Varmistin ohjelman toimivuuden ja version terminaalista:

```bash
$ wireshark --version
```
<details>
<summary>Testitulos</summary>

<img width="678" height="826" alt="image" src="https://github.com/user-attachments/assets/bc24c9cc-d0fd-4f5f-b50e-fe0aeabbfc91" />

</details>



### Verkkorajapinnan valinta ja liikenteen sieppaus

* Käynnistin Wiresharkin järjestelmän komennolla:

```bash
$ wireshark 

```

* Valitsin sieppaukseen oikean verkkorajapinnan (`eth0`).
* Generoin kaappaukseen liikennettä ajamalla päätteessä verkkotestin: `ping -c4 google.com`.

<details>
<summary>Wireshark-sieppaus</summary>

<img width="1291" height="1020" alt="image" src="https://github.com/user-attachments/assets/778e10d4-a714-411a-9b67-7379721ccec4" />

</details>

## d) Oikeesti TCP/IP

<details>
<summary>TCP/IP kerrokset:</summary>

<img width="1845" height="1078" alt="image" src="https://github.com/user-attachments/assets/2a3a3b1e-0a40-4496-adbf-578e99548f5b" />

</details>

### TCP/IP kerrokset

1. Fyysinen kehys (Physical / Frame 1) — [Punainen]:
   * `Frame 1: 70 bytes on wire...`
   * Wiresharkin oma metadata-rivi, joka kertoo verkkokortin sieppaaman raakadatakehyksen koon (70 tavua) ja aika-leiman.

2. Linkkikerros (Link Layer / Ethernet II) — [Sininen]:
   * `Ethernet II, Src: PCSystemtec_5a:87:bc (08:00:27:5a:87:bc), Dst: 52:55:0a:00:02:02`
   * Siirtoyhteyden L2-kehys. Lähde-MAC-osoite kuuluu virtuaalikoneen virtuaaliselle verkkokortille (Oracle VirtualBox OUI `08:00:27`) ja kohde-MAC-osoite oletusyhdyskäytävälle/reitittimelle.

3. Internet-kerros (Internet Layer / IP) — [Vihreä]:
   * `Internet Protocol Version 4, Src: 10.0.2.15, Dst: 192.168.1.1`
   * L3 IP-paketti. Määrittelee loogiset osoitteet: lähde-IP on virtuaalikoneemme `10.0.2.15` ja kohde-IP paikallinen nimipalvelin/reititin `192.168.1.1`.
     
4. Kuljetuskerros (Transport Layer / UDP) — [Oranssi]:
   * `User Datagram Protocol, Src Port: 46529, Dst Port: 53`
   * L4-segmentti/datagrammi. Prosessien välinen liikennöinti tapahtuu porttien avulla. Lähdeportti `46529` on satunnainen dynaminen yläportti (ephemeral port) ja kohdeportti `53` on standardi DNS-palveluportti[cite: 13]. Kuljetusmuotona on UDP-protokolla sen pienemmän viiveen vuoksi (TCP-kättelyä ei tarvita ennen kyselyä).

5. Sovelluskerros (Application Layer / DNS) — [Keltainen]:
   * `Domain Name System (query)`
   * L7-sovelluskerroksen varsinainen hyötykuorma (payload). Sisältää kyselyn tekstimuotoisen rakenteen (`A google.com`), jolla pyydetään `google.com`-isäntänimen IPv4-osoitetta.


## e) Mitäs tuli surffattua?
Avasin tiedoston Wiresharkissa (`cntrl + O`) ja valitsin `surfing-secure.pcap`-tiedoston. Seuraavaksi menin ylävalikosta kohtaan `Statistics -> Capture File Properties`.

<details>
<summary>surfing-secure.pcap</summary>

<img width="1687" height="813" alt="image" src="https://github.com/user-attachments/assets/3b239710-4ef8-425a-b843-67135e543fbe" />

</details>

### Kaappauksen yleistiedot (Capture File Properties)
* Tiedoston nimi ja sijainti: `/home/kali/Downloads/surfing-secure.pcap`.
* Tiedostokoko: 126 kB.
* Pakettien kokonaismäärä 283 pakettia
* Kaappauksen kesto (Time span): 7.536 sekuntia.
* Keskimääräinen pakettikoko: 433 tavua.

### Liikenteen yleiskuvaus
* Kaappaus on kooltaan melko pieni ja kestoltaan lyhyt (reilu 7 sekuntia), ja se sisältää yhteensä 283 pakettia.
* Liikenteen keskimääräinen siirtonopeus on ollut noin 16 kB/s (129 kb/s).
* Kyseessä on tyypillinen lyhyt verkkoselailutilanne tai yksittäisen sivuston lataustapahtuma, jossa datasiirto on tapahtunut Ethernet-enkapsuloinnilla.

## f) 
