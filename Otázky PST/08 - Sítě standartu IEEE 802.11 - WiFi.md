Standart IEEE 802.11 defunije bezdrátové sítě. Pro tyto sítě je specifické že jako své médium využívají vzduch. Pro připojení bezdrátové sítě do sítě normální potřebujeme AP (Access point), který převádí standarty [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Standart IEEE 802.3|IEEE 802.3]] na IEEE 802.11. Pro bezdrátové sítě používáme specifické termíny, které je se vhodné pro prochopení látky naučit.
###### Ad-hoc
Označnení pro přímé spojení dvou zařízení. Používá se taky název IBBS (Independent BBS)
###### BBS (Basic Service Set)
BBS je buňka bezdrátové sítě tvořená jedním AP a jeho zařízeními. AP v této síti řídí provoz v síti. AP má svoje BSSID, které odpovídá MAC AP
###### ESS (Extended Service Set)
Více BSS je propojených přes Distribuční systém (DS) - Sdílí stejný SSID
###### SSID (Jméno WiFi sítě)
Jméno bezdrátové sítě. AP může SSID buď aktiovně propagovat (SSID v beacon rámcích (Broadcast)), nebo ne (skryté *nezvyžuje bezpečnost, lze odposlechnout*). Jeho maximální délka je 32 znaků.
###### ESSID
Stejné SSID pro všechny AP v ESS. Klient vidí pouze jednou síť. SSID pro ESS

![[08 - Topologie sítí.png]]

---
### Standarty bezdrátových sítí
Bezdráové sítě mají stejně jako sítě drátové několik růzdných standartů. Ty jsou:

| Standart | WiFi č.   | Rok     | Pásmo       | Max. rychlost | Označení HT | Klíčové novinka                                       |
| -------- | --------- | ------- | ----------- | ------------- | ----------- | ----------------------------------------------------- |
| 802.11b  |           | 1999    | 2,4 GHz     | 11 Mbps       |             | První masové wifi, DSSS                               |
| 802.11a  |           | 1999    | 5 GHz       | 54 Mbps       |             | OFDM, méně rušení, kratší dosah                       |
| 802.11g  |           | 2003    | 2,4 GHz     | 54 Mbps       |             | OFDM na 2,4; zpětná kompatibilita s 11b               |
| 802.11n  | WiFi 4    | 2009    | 2,4;5 GHz   | 600 Mbps      | HT          | MIMO, kanály 40 MHz, HT (High-Throughput)             |
| 802.11ac | WiFi 5    | 2013    | 5 GHz       | 6,9 Gbps      | VHT         | MU-MIMO DL, kanály 80/160 MHz, 256-QAM, VHT           |
| 802.11ax | WiFi 6/6E | 2019/21 | 2,4;5;6 GHz | 9,6 Gbps      | HE          | OFDMA, MU-MIMO UL-DL, BBS, 1024-QAM, Beamforming, TWT |
| 802.11be | WiFi 7    | 2024    | 2,4;5;6 GHz | 46 Gbps       | EHT         | MLO, 320 MHz, 4096-QAM, Multi-RU, Preamble Puncturing |
| 802.11ad | WiGig     | 2012    | 6 GHz       | 7 Gbps        |             | Velmi krátký dosah, bez překážek                      |
Tyto standarty používají různé modulace a technologie

---
### Modulace a modulační technologie 802.11n
Modulace je proces kdy převádíme data (1 a 0) do signálů. Vrámci bezdrátových technologií se jedná o vlny přenéšené přes vzduch. V dnešních sítíćh se hlavně používá technologie XX-QAM.
###### QAM a jak funguje
QAM je amplitudová a fázová modulace která používá dvě fázově posunuté vlny k přenosu informací. Tyto vlny jsou posunuty o 90 stupňů, čímž tvoří takový sin a cos. Do jednotkové kružnice (konstalačního grafu) jsou pak zapsány hodnoty, které tato modulace přesouvá.

![[08 - QAM.png]]

Číslo XX u QAM značí kolik bodů v konstalačním grafu je a tudíž kolik typů hodnot může daná modulace zastávat. Bohužel platí to že čím více typů hodnot QAM přenáší, tím jednodušeji je rušitelná.
QAM u verzí:

| 16-QAM   | základ |
| -------- | ------ |
| 64-QAM   | WiFi 4 |
| 256-QAM  | WiFi 5 |
| 1024-QAM | WiFi 6 |
| 4096-QAM | WiFi 7 |
#### Modulační techniky
###### FHSS (Freqency Hopping Spread Spectrum)
Signál přeskakuje mezi frekvencemi v spektru, a to dle pseudonáhodného vzoru. Dělá tak komunikaci oddolnější vůči rušení a odposlechu jedné frekvence. Využívala původní Wifi
###### DSSS (Direct seqence sepread spectrum)
Signál se rozprostře přes celou šíři pásma. Přitom také je komunikace zašifrována PN kódem aby přenos splynul s okolím. 
###### OFDM (Ortogonal Freqency Division Multiplexing)
Kanál je rozprosřen do několika subnosných, která každá přenáší část dat. Jedná se vpostatě  o paralerizaci signálu v jednom kanálu. WiFi 6 přidal i možnost přiřadit více uživatelům subnosné (OFDMA)

### Kanály
Kanály jsou rozdělené pásma. 
**Pásmo 2,4 GHz** - Tvoří 14 kanálů (v ČR je 13), pro WiFi se používají 1,6,11 (Šířka wifi je 20 MHz takže se jako jediný nepřekrývají.) Šířka kanálu je 5 MHz.
**Pásmo 5 GHz** - Tvoří 19-25 kanálů. Šířka kanálu je 20 MHz. Kanály lze spojovat.
**Pásmo 2,4 GHz** - Tvoří 24 kanálů. O šířce 20 MHz.

### SU-MIMO vs. MU-MIMO
MIMO (Multiple Input Multiple Output) je technologie kdy několik antén obsluhuje několik zařízení současně. Dělí se na dvě verze SU-MIMO a MU-MIMO

|              | SU-MIMO | MU-MIMO       |
| ------------ | ------- | ------------- |
| Klienti      | 1       | více najednou |
| Standart     | Wifi 4  | Wifi 5        |
| Směr         | DL i UL | DL, DL+UL     |
| Max. Streams | 4x4     | 8x8           |
### Další termíny
###### Beamforming
AP soustředí signál k jednomu konkrétnímu klientovi, místo do všech stran.
###### BSS coloring 
Každá BSS dostane specifickou barvu. Klienti ignorují barvy kde je signál slabý


