Zabezpečení WLAN se snaží zajistit [[02 - CIA triáda, McCumber cube#CIA triáda|CIA triádu]] v přenosu vzduchem. To znamená, že ověřujeme kdo co je, chráníme data před požkozením a snažíme se o dostupnost naší sítě.

V autentizaci se používají hlavně dva standarty. OSA a SKA.
###### OSA (Open System Authentication)
I když má slovo authentikace v názvu, tak se o žádnou authentikaci nejedná. Po žádosti pro authentikaci jsou všichni hned přijati a asociováni (dohoda na technických parametrech). I přes to je to dnes nejvyužívanější typ authentikace, využívaný standarty typu WPA, WPA2 a WPA3. V těchto standartech slouží OSA jako enrollment nových zařízení. Samotnou authentifikaci má na starost potom PSK nebo SAE. Průběh níže:

| Klient              | AP                          | Průběh |
| ------------------- | --------------------------- | ------ |
| Auth Request        |                             | ↓      |
|                     | Auth Success (vždy nastane) | ↓      |
| Association request |                             | ↓      |
|                     | Association response        | ↓      |
###### SKA (Shared Key Authentication)
Narozdíl od OSA zda authentikace probíhá a to za ověření sdíleného klíče. Jediný standart který SKA používal byl WEP. Dnes se nepoužívá hlavně kvůli chybě v zabezpečení, kdy hacker dokáže z provozu získat klíč a dešifrovat provoz.

| Klient                | AP                             | Průběh |
| --------------------- | ------------------------------ | ------ |
| Auth Request          |                                | ↓      |
|                       | Challenge (náhodný text)       | ↓      |
| Zašifrovaný challenge |                                | ↓      |
|                       | Ověření + Auth Success/Failure | ↓      |

---
### Historie zabezpečení WLAN
Níže rozepíšu standardy zabezpečení tak jak postupně byly tvořeny a jejich rozdíly.

|             | WEP     | WPA          | WPA2         | WPA3         | Poznámky                                                              |
| ----------- | ------- | ------------ | ------------ | ------------ | --------------------------------------------------------------------- |
| Rok         | 1999    | 2003         | 2004         | 2018         |                                                                       |
| Šifrování   | RC4     | TKIP         | AES-CCMP     | AES-GCMP     | RC4 je prolomený                                                      |
| Klíč        | 40/104b | 128b         | 128b         | 128/256b     | Delší klíč, lepší klíč                                                |
| Integrita   | CRC-32  | MIC          | CBC-MAC      | GMAC         | CRC-32 lze snadno změnit bez detekce, nověší standarty tomu zabraňují |
| Bezpečnost  | Zlomena | Slabá        | Standart     | Nejlepší     | WEP nepoužíváme                                                       |
| Autentizace | OSA/SKA | PSK / 802.1X | PSK / 802.1X | SAE / 801.1X | SAE odstraůje dictonary útoky                                         |

---
### Používané šifrovací protokoly
Jak můžeme vidět v tabulce s historií, tak v sítích používáme relativně velké množství šifrovacích protokolů. Tím pádem jsou dole rozepsané.
###### PSK (Pre-Shared Key)
Pro připojení je nutné znát heslo. Tento typ ověření a šifrování se používá hlavně v domácích sítí a SOHO. Největší slabinou jsou slovníkové útoky na heslo. Klíč se nikdy přes síť nesdílí. Ověřuje se 4-cestným handsakem. Ten vypadá takhle:

| Klient                                                    | Komunikace                     | Authentikátor                                             |
| --------------------------------------------------------- | ------------------------------ | --------------------------------------------------------- |
| Gernerování PMK = PBKDF2(heslo,SSID)                      |                                | Gernerování PMK = PBKDF2(heslo,SSID)                      |
|                                                           | ANonce (náhodné číslo AP)      |                                                           |
|                                                           | <------------------------      |                                                           |
| Výpočet PTK = PRF(PMK, Anonce, Snonce, MACap, MAC klient) | SNonce (náhodné číslo Klienta) |                                                           |
|                                                           | ------------------------>      |                                                           |
|                                                           |                                | Výpočet PTK = PRF(PMK, Anonce, Snonce, MACap, MAC klient) |
| -------------------------------------------               | Šifrováno PTK                  | --------------------------------------------              |
|                                                           | Poslání GTK (GTK + MIC)        |                                                           |
|                                                           | <------------------------      |                                                           |
|                                                           | Potvrzení GTK (ACK + MIC)      |                                                           |
|                                                           | ------------------------>      |                                                           |
S tím že jednotlivé zkratky znamenají:
- **PMK** - Pairwise Master Key: Je vypočítán z hesla a SSID za pomoci algoritmu PBKDF2
- **PTK** - Pairwise Transient Key: odvozen z PMK, Anonce (náhodné číslo poslané AP), Snonce (náhodné číslo poslané klientem) a MAC adresy obobu dvou. Je unikátní pro každé spojení mezi AP a klientem.
- **GTK** - Group Temporal Key: Klíč využívaný pro šifrování multicast a broadcast komunikace. Stejný pro všechny klinety BSS.
- **MIC** - Ověřuje a zajšťuje integritu zpráv v handshakeu, Zabraňuje manipulaci.

*Zjednosdušeně celé ověření PSK spočívá v tom že pokud oba konce mají stejný heslo a SSID, tak se schodnou na symetrické šifře kterou budou používat (PTK)*
###### TKIP
Temporal Key Integrity Protocol: Byl vytvořen aby nahradil prolomený WEP s RC4. Používá 512 bitový klíč. Také ǵeneruje nový klíč pro každý paket (per-paket key), pro integritu používá MIC.
###### AES
Advanced Encryption Standart - symetfická bloková šifra, využívá klíče 128,192,256b a šifruje 128b bloky dat. Velmi bezpečná, hardwareově akcelerovaná. Slouží jako zákald WPA2 a 3.
###### CCMP
Counter mode with Cipher block chaining MAC Protocol je šifrovací protokol využívaný WPA2. Používá AES v CCM módu, to zajišťuje šifrování (CTR), ale i integritu (CBC-MAC). Nahrazuje TKIP v WPA 2
###### GCMP
Galois/Counter Mode Protocol je šifrovacím protoklem v WPA 3. Používá AES 128/256 v GCM módu. Je rychlejší než CCMP, stejně bezpečný.

---
### 802.1X, EAP, RADIUS, AAA (Podnikové zabezpečení)
Pokud potřebujeme zabezpečit WLAN síť v podnikovém prostředí, tak používáme protokoly WPA2/3 Enterprise. Tyto systémy se liší v tom že se pro ověření nepoužívá sdílené heslo wifi, ale individuální autentizací pro každého uživatele (unikátní PMK pro každéhou uživatele) přes servery RADIUS/TAKASH. Během této autentizace probíhá komunikace nejenom mezi klientem a AP, ale i mezi AP a serverem. Pro jednotlivé typy komunikace využívají různé protokoly. Jaké používají je napsané níže:

| Klient | EAP    | AP       | RADIUS     | Server   |
| ------ | ------ | -------- | ---------- | -------- |
| (PC)   | <----> | (router) | <--------> | (radius) |
*Víc přesně je komunikace popsaná na tomto obrázku. Rozepisovat to ale nebudu.*

![[13 - 802.X.png]]

Serverů jako RADIUS se častokrát říká i AAA servery a to kvůli tomu že zajišťují [[02 - CIA triáda, McCumber cube#AAA|AAA]] v síti.

###### RADIUS
RADIUS jak jsme řekli je název serveru a protokolu, který zajišťuje AAA služby v síťovém provou. Pracuje na UDP portech 1812 (auth) a 1813 (accounting). Komunikuje mezi AP a RADIUS serverem  v šifrované komunikaci zašifrované pomocí RADIUS seceret. Jeho nástupcem má být DIAMETER.
###### EAP
EAP slouží jako bezpečný komunikátor mezi klientem a RADIUS serverem. Ovlivňuje čím se klient ověřuje. Má několik verzí:

| Typ      | Tunel | Autentizace                                                            |
| -------- | ----- | ---------------------------------------------------------------------- |
| EAP-MD5  | ne    | Heslo. Slabé be šif. tunelu                                            |
| EAP-TLS  | TLS   | Certifikát klienta + serveru. Nejbezpečnější *Ověřuje se certifikátem* |
| PEAP     | TLS   | Cerfikát jen serveru, heslo v tunelu *Ověřuje heslo a jméno*           |
| EAP-TTLS | TLS   | Certifikát jen serveru, flexibilí autentizace *Možné oboje*            |
| EAP-FAST | PAC   | Cisco; Bez certifikátu, PAC token *Ověřuje heslo a jméno*              |

---
### Vylepšené protokoly
###### SAE 
SAE vylepšuje PSK tím že šifruje přes diffe-hellman výměnu Anonce a Snonce, čímž znemožňují offline slovníkové útoky. Každé připojené využívánový klíč.
###### OWE
OWE šifruje provoz i na otevřené síti. Znova využívá diffe-hellman pro výměnu klíčů. Nikoho neautentizuje ale šifruje
###### DPP
Bezpečná náhrada WPS (Wi-Fi Protected Setup). Zařízení se připojuje k routeru za pomoci QR kódu/NFC/Bluetooth. Používá veřejné klíče místo hesla. Tím se eliminuje slabost WPS pinu (8 míst), který šel jednoduše prolomit.

---
### MAC filtrování a skrytí SSID *(pseudo zabezpečení)*
MAC filtrování a skrytí SSID sice vypadají jako relativně silná opatření zabezpečení, ale ve skutečnosti žádně zabezpečení nanabízí:

- **MAC filtering** - AP obsahuje blacklist/whitelist MAC adres. Nepovolí asociaci jakémukoliv zařízení co je/není na seznamu. Je nefunkční hlavně díky MAC spoofingu. (MAC lze jednosuše změnit) 
- **skryté SSID** - Skryté SSID funguje na principu toho že AP nevysílá tzv. beacon rámce. Ochrana spočívá v tom že útočník nebude útočit na síť o které neví že existuje. Nefunkční protože se SSID dá dovědět i z jiné síťové komunikace. (Probe request, Probe response a Association request). 