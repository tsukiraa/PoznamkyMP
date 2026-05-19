*To co prokeš touhle otázkou míní je 1. a 2. vrstva OSI/ISO, nejdříve začneme tím co znamenají standarty IEEE 802.2 a IEEE 802.3*

### Standart IEEE 802.3

Zjednodušeně sandart IEEE 802.3 definuje to jak se mají posílat data v lokální síti (mimo Wireless LAN) a to od typu kabelu, přes modulaci až po MAC adresy. To všechno řeší právě tento standart. V podstatě pokud chceme posílat jen velmi jednoduché zprávy v LAN síti, tak nám stačí pouze toto.

### Standart IEEE 802.2

Standart IEEE 802.2 defunuje takzvané LLC. To zjednodušeně předává dál informace o tom jak by s daty mělo být zacházeno na vyšší vrstvě (jestli to je IPv4 nebo IPv6 packet). LLC spadá do druhé vrstvy a u wireless sítí je povinnou součástí.

---
*Avšak pro zjednodušení to tady budu popisovat po vrstvách protože v standarty jsou komplikované. Jenom si pamatujte co kam patří. Začneme u vrstvy první a pak půjdeme postupně na druhou.*

### Co potřebujeme k komunikaci

V sítích je naším cílem propojit dvě zařízení, aby mezi sebou komunikovali. Pro jednoduší pochopení toho co jednotlivé pojmy znamenají dodávám popsanou [kresbu](obsidian://open?vault=KYB&file=Ot%C3%A1zky%20PST%2FObr%C3%A1zky%2F01%20-%20%20Model%20komunikace%201.%20vrstvy.png) mezi dvěma počítači.

![[01 -  Model komunikace 1. vrstvy.png]]

K komunikaci potřebujeme:
- Stejný jazyk (Modulace a média)
- Rychlost a načasování (Perambule a SFD)
- Potvrzení příjmu *(později na 4. vrstvě TCP)*
- Identifikovatelný přijímač a vysílač (MAC adresy)
---
### Modulace, média a křížení

Stejným jazykem se míní to že je dle standartů stanovený typ média a to jak se data budou na dané médium budou dávat. Tyto standarty se značí IEEE 802.3*xx* kde *xx* jsou jakákoliv písmena.

##### Média a jejich šířky pásem
Médiem se v sítích myslí kabel, na který se data dávají. Mezi starší patří dial-up a coax a pak jsou tu novější jako nejrůznější kategorie Ethernetu. (CAT 5,6,7 atd.). U Ethernetu šířka pásma znamená max. rychlost šíření dat. Jako běžný konektor se používá RJ-45 s osmi páry. Jako přístup k médiu se volí CSMA/CD (Collision detection)

| **Médium / Technologie**      | **Max. rychlost** | **Standard**      | **Poznámka**                                      |
| ----------------------------- | ----------------- | ----------------- | ------------------------------------------------- |
| **Dial-up** (Modem)           | **56 kbps**       | ITU-T V.90 / V.92 | Využívá běžnou telefonní linku a analogový modem. |
| **ISDN**                      | **128 kbps**      | ITU-T I.430       | Digitální telefonní linka (2 kanály po 64 kbps).  |
| **Koaxiální kabel** (10Base2) | **10 Mbps**       | IEEE 802.3a       | "Tenký" Ethernet (RG-58), zapojení do sběrnice.   |
| **Cat 3** (UTP)               | **10 Mbps**       | IEEE 802.3i       | První kroucená dvojlinka pro Ethernet.            |
| **Cat 5** (UTP)               | **100 Mbps**      | IEEE 802.3u       | Fast Ethernet, dnes již zastaralý.                |
| **Cat 5e** (UTP)              | **1 Gbps**        | IEEE 802.3ab      | Naprostý standard pro Gigabitové sítě.            |
| **Cat 6** (UTP/STP)           | **1 Gbps** (10G*) | IEEE 802.3ab/an   | Lepší izolace, 10 Gbps jen na krátké vzdálenosti. |
| **Cat 6a** (STP)              | **10 Gbps**       | IEEE 802.3an      | Standard pro moderní 10G rozvody.                 |
| **Cat 7** (S/FTP)             | **10 Gbps**       | ISO/IEC 11801     | Každý pár stíněný zvlášť, velmi odolné.           |
| **Cat 8** (S/FTP)             | **25/40 Gbps**    | IEEE 802.3bq      | Pro datová centra (velmi krátké kabely).          |
V tabulce si také u kabelů můžeme všimnou popisů typu "S/FTP". Tyto písmena značí ochranu kabele před rušením a odposlechem. "S" před lomítkem značí že je kabel chráněn síťkou a "F" za lomítkem zančí že jednotlivé páry jsou obaleny ve fólii.

###### Křížení
Dúležité je také zmínit že pro propojení určitých zařízení jsou potřeba křížené kabely. V dnešní době toto už není takovím problémem díky funkcím autocrossing,MDI,MDIX atd.

![[01 - Křížení.png|172]]
###### Optická média
Optická média plní stejnou roli jako kabel, akorát místo elektrických signálů posílají a komunikují pomocí světla. Dělí se na dvě kategorie SMF/MMF. SMF je celkově lepší než MMF. (viz. tabulka níže), ale je celkově dražší, proto se používá jen na místech kde je potřeba (podmořské kabely). MMF jsou takový levnější kompromis. Používají se v páteřních linkách datacenter a kancelářích. Jako konektory se běžně používají LC (clip), v data centrech MTP/MPO (zásuvný 12 vláken) a v telekomunikacích FC (Šroubovací).

| Vlastnost           | **Single-Mode (SMF)** | **Multi-Mode (MMF)**                       |
| ------------------- | --------------------- | ------------------------------------------ |
| **Průměr jádra**    | ~ 9 µm (velmi tenké)  | 50 µm nebo 62.5 µm (širší)                 |
| **Typický dosah**   | 10 km až 100 km       | 300 m až 550 m                             |
| **Zdroj signálu**   | Laser                 | LED / VCSEL                                |
| **Klíčový problém** | Cena                  | **Modální disperze** (paprsky se rozmažou) |
| **Barva kabelu**    | Žlutá                 | Oranžová, tyrkysová                        |
###### Bezdrátové sítě
Bezdrátové sítě jsou definovány standartem IEEE 802.11. Jako médium přenosu používají vzduch. K připojení na bedrátovou síť se v dnešní době nejčasteji používa AP (Access point). V dnešní době se používají hlavně 3 kanály. 2,4 GHz 5,GHz a 6 GHz. Tyto kanály se dají i rozprostřít do spektra, aby po kanálu mohlo komunikovat více zařízení. Jako přístup k médiu se u bezdrátových komunikacích používá CSMA/CA (Collision Avoidence)
##### Modulace
Šířku pásem neovlivňuje jenom kolik kabelů a jaký kabely pro komunikaci používáme. Ovlivňuje ji i modulace. Modulace je proces, kdy 1 a 0 překládáme do signálů, které dáváme na médium. Různé technologie využívají různé modulace viz tabulka.

| **Médium / Technologie**      | **Typická modulace / Kódování**                                                            | **Princip (zjednodušeně)**                                                                                          |
| ----------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| **Dial-up** (Modem)           | [**QAM / V.90**](https://cs.wikipedia.org/wiki/Kvadraturn%C3%AD_amplitudov%C3%A1_modulace) | **Kvadraturní amplitudová modulace.** Mění amplitudu a fázi zvukového signálu.                                      |
| **Koaxiální kabel** (10Base2) | [**Manchester**](https://cs.wikipedia.org/wiki/K%C3%B3dov%C3%A1n%C3%AD_Manchester)         | **Kódování hranou.** 0 a 1 jsou určeny směrem přechodu napětí (nahoru/dolů) uprostřed taktu.                        |
| **Cat 3 / 5** (10/100 Mbps)   | [**MLT-3 / 4B5B**](https://en.wikipedia.org/wiki/MLT-3_encoding)                           | **Tříúrovňové napětí.** Střídá stavy +1V, 0V a -1V, aby se snížilo elektromagnetické vyzařování.                    |
| **Cat 5e** (1 Gbps)           | **PAM-5**                                                                                  | **Pulsní amplitudová modulace (5 úrovní).** Přenáší 2 bity naráz pomocí pěti různých úrovní napětí.                 |
| **Cat 6a / 7** (10 Gbps)      | **DSQ128 (PAM-16)**                                                                        | **16 úrovní napětí.** Extrémně husté kódování, které vyžaduje velmi kvalitní stínění kabelu.                        |
| **Optické vlákno**            | **NRZ / PAM-4**                                                                            | **NRZ (Non-Return to Zero):** Světlo svítí (1) nebo nesvítí (0). U 400G+ se používá PAM-4 (čtyři intenzity světla). |
| **Bezdrátové sítě**           | **4PSK/8PSK/QAMXX**                                                                        | --                                                                                                                  |

---
### Identifikace vysílače a příjemce

##### MAC Vrstva
Každé rozhraní vysílače nebo příjemce je identifikováno svojí MAC adresou. Adresa se skládá z 6ti dvojic hexadecimálních čísel. Není přiřazená, takže lze jednoduše změnit. Každý zařízení má tvz. ARP cache kam si ukádá zjištěné IP adresy. Ty zjištuje pomocí ARP protokolu.

###### Typy adresace

| **Unicast**   | 1 => 1               | Normální komunikace                                    |
| ------------- | -------------------- | ------------------------------------------------------ |
| **Broadcast** | 1=> všichni          | Přijmou všichni v LAN. MAC adresa: FF-FF-FF-FF-FF-FF   |
| **Multicast** | 1 => vybraná skupina | Přijmou specifičtí hosté (přihlášení) MAC: 01-xx-xx... |

###### ARP
Častokrát když vysíláme tak známe pouze IP adresu (vrstva 3). Proto, aby jsme získali z ní MAC adresu slouží protokol ARP. Funguje dle znázornění na obrázku:

![[01 - Address-Resolution-Protocol.png|564]]

Pro upřesnění PC1 se všech (broadcast) zeptá kdo má tuto IP adresu. PC2 odpoví (unicast) že on ji má. Po přijětí odpovědi si ji PC1 uloží do ARP cache. Vyjímka platí pro IP adresy které jsou mimo LAN. Potom se ptá na MAC adresu výchozí brány. 

##### PPP (Point-to-Point Protocol)
PPP se používá pokud chceme zajistit komunikaci mezi dvěma přímo spojenými zařízeními. Používá se např. při spojení s modemem, DSL a nebo sériovou linkou.

Vlastnosti připojení:

- Umožňuje autentizaci: PAP (heslo), CHAP (Výzva-Odpověď)
- Detekce chyb: pomocí FCS (podobná funkčnost TCP)
- Enkapsulace vyších vrstev: Nepotřebuje LLC
- Neschopné zprovoznit komunikaci přes switch, hub, nebo router

### Rámce
Rámec ethernet (MAC + LLC). Pokud je menší než 86B, tak je to runt. Pokud větší než 1500, tak giant. Oba dva se neposílají. Vnímáno jako chyba komunikace. Není konsenzus zda do rámce patří preamble a SFD. *Avšak dle [Milana Keršlágra](https://www.pslib.cz/milan.kerslager/Ethernetov%C3%BD_r%C3%A1mec) ano, takže jsem je sem připsal.*

| Preamble         | SFD        | Cíl. MAC      | Zdroj. MAC      | Type                 | Data     | FCS      |
| ---------------- | ---------- | ------------- | --------------- | -------------------- | -------- | -------- |
| Synchnizace (7B) | Start (1B) | Příjemce (6B) | Odesílatel (6B) | LLC (IPv6/IPv4) (2B) | 46-1500B | CRC (4B) |
