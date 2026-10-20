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
  
<summary>Wireshark skanni</summary>

<img width="1291" height="1020" alt="image" src="https://github.com/user-attachments/assets/774588e1-2b13-42da-b231-600689125100" />


</details>

### Verkkoliikenteen analyysi

#### 1. Nimipalvelukysely (DNS / UDP)
Ennen kuin `ping`-komento voi muodostaa yhteyttä kohteeseen, järjestelmän tulee selvittää isäntänimen `google.com` vastaava IP-osoite.
* Paketit 1-4 Kali virtuaalikone (`10.0.2.15`) lähettää nimipalvelukyselyn (DNS Query) paikalliselle yhdyskäytävälle/DNS-palvelimelle (`192.168.1.1`).
* Kyselyssä haetaan sekä IPv4-osoitetta (A-tietue) että IPv6-osoitetta (AAAA-tietue).
* Vastauspaketissa 3 nimipalvelin palauttaa IP-osoitteen `142.250.150.100`, johon `ping`-komento suuntaa varsinaisen liikenteen.
* Nimipalvelukysely käyttää kuljetuskerroksella UDP-protokollaa ja kohdeporttia 53.

#### 2. ICMP
