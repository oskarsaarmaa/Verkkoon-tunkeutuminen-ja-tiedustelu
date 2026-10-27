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
