# a) Hei maailma

## Tehtävän tavoite

Tehtävän tavoitteena on kaapata verkkoliikennettä Wireshark-työkalulla, erottaa yksittäinen TCP-yhteydenottopaketti (`HTTP GET` tai `TCP SYN/ACK`) ja identifioida sekä analysoida TCP/IP-pinon neljä eri kerrosta kyseisestä kehyksestä.


## Liikenteen sieppaus Wireshark:lla

Ennen kaappauksen aloittamista tarkistan järjestelmän verkkokortin nimen ja osoitteen `ip`-komennolla:

<details>
<summary>ip:</summary>

<img width="810" height="231" alt="image" src="https://github.com/user-attachments/assets/f4555f36-6202-405a-aca7-8ce93c4ff69d" />


</details>

> Tärkein tieto on aktiivinen verkkoliityntä `eth0` ja sen IPv4-osoite `10.0.2.15`.


<details>
<summary>Wireshark</summary>
  
<img width="1269" height="1000" alt="image" src="https://github.com/user-attachments/assets/73dbb917-07e0-4c91-b6f1-a1bf1ce9254c" />


</details>

> Käynnistin Wiresharkin suorittamalla: `Wireshark`-komennon. Liikenteen tarkkaillun saa aloitettua painamalla sinistä hainlevä kuvaketta vasemmassa ylänurkassa.

<details>
  
<summary>Wireshark-kaappaus</summary>

<img width="1291" height="1020" alt="image" src="https://github.com/user-attachments/assets/774588e1-2b13-42da-b231-600689125100" />

</details>

>Ping-komennon aiheuttama DNS- ja ICMP-liikenne Wiresharkissa.

### Verkkoliikenteen analyysi

#### 1. Nimipalvelukysely (DNS / UDP)
Ennen kuin `ping`-komento voi muodostaa yhteyttä kohteeseen, järjestelmän tulee selvittää isäntänimen `google.com` vastaava IP-osoite.
* Paketit 1-4 Kali virtuaalikone (`10.0.2.15`) lähettää nimipalvelukyselyn (DNS Query) paikalliselle yhdyskäytävälle/DNS-palvelimelle (`192.168.1.1`).
* Kyselyssä haetaan sekä IPv4-osoitetta (A-tietue) että IPv6-osoitetta (AAAA-tietue).
* Vastauspaketissa 3 nimipalvelin palauttaa IP-osoitteen `142.250.150.100`, johon `ping`-komento suuntaa varsinaisen liikenteen.
* Nimipalvelukysely käyttää kuljetuskerroksella UDP-protokollaa ja kohdeporttia 53.

#### 2. ICMP
Kun IP-osoite on selvinnyt, käynnistyy varsinainen viiveen mittaus.
* Paketit 5, 9, 11 ja 13 lähdeosoitteesta `10.0.2.15` lähetetään ICMP Echo Request -paketti kohteeseen `142.250.150.100`.
* Paketit 6, 10, 12 ja 14 kohdepalvelin vastaa välittömästi ICMP Echo Reply -paketilla.
* Kuvasta nähdään, että verkkoviive (RTT) asettuu välille ~11.9 ms – 15.5 ms, eikä pakettihävikkiä esiinny.

#### 3. Kapselointi ja L2-osoitteet

Wiresharkin Packet Details -paneelista nähdään valitun DNS-paketin rakenne:
* Lähde-MAC: `08:00:27:5a:87:bc` (OUI `08:00:27` identifioi laitteen Oracle VirtualBoxin virtuaaliseksi verkkokortiksi).
* Kohde-MAC: Reitittimen / virtuaalisen yhdyskäytävän osoite `52:54:00:xx:xx:xx`.

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
     

## Lähteet
* Karvinen, T. 2026: Verkkoon tunkeutuminen ja tiedustelu. Saatavilla: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/
* Wireshark Documentation: Packet Details Pane. Saatavilla: https://www.wireshark.org/docs/wsug_html_chunk/ChUsePacketDetailsPaneSection.html
  
