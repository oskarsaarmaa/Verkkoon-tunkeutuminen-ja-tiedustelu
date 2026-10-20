# a) Hei maailma

## Tehtävän tavoite

Tehtävän tavoitteena on kaapata verkkoliikennettä Wireshark-työkalulla, erottaa yksittäinen TCP-yhteydenottopaketti (`HTTP GET` tai `TCP SYN/ACK`) ja identifioida sekä analysoida TCP/IP-pinon neljä eri kerrosta kyseisestä kehyksestä.


## Liikenteen sieppaus Wireshark:lla

Ennen kaappauksen aloittamista tarkistan järjestelmän verkkokortin nimen ja osoitteen `ip`-komennolla:

<details>
<summary>ip:</summary>

<img width="810" height="231" alt="image" src="https://github.com/user-attachments/assets/f4555f36-6202-405a-aca7-8ce93c4ff69d" />


</details>

> Tärkein tieto on aktiivinen verkkoliityntä `eth0` ja sen IPv4-osoite `10.0.2.15`

