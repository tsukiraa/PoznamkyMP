Internet věcí se dá definovat tak, že na internet připojujeme jednoduché fyzické zařízení, plné senzorů a softwaru, které potom připojíme na síť.
Pod IoT zařízením si můžeme představit chytré senzory CO2 *(Důlnictví)*, nebo automatické zavlažovače, či po chytrý zubní kartáček.

Zařízení IoT stojí na 4 základních pilířích (nebo vrstvách). Těmi jsou:

- **Věci (Fyzická vrstva)**: To co zařízení je, skládá se z použitých mikrokontrolerů, snezorů, akčních členů atd.
- **Konektivita (Komunikační vrstva)**: Čím komunikujeme a jak jsou zařízení porpojená. Do této kategorie spadá Ethernet, Wi-Fi, BT, BLE, RFID a pak i specifické pro IoT jako LoRa a MQTT
- **Data a analytika (Cloudová vrstva)**: Tam kam se data ukádají a postupně zpracovávají. Nejčastěji se jedná o server nebo datové centrum.
- **Lidé a procesy (Servisní vrstva)**: Vyzobraování získaných dat uživateli. Sem spadají všechny HMI (Human-Mashine Interface).

![[06 - Pilíře IoT.png]]

---

### Hardware IoT (Fyzická vrstva)
V IoT rozdělujeme hardware do tří tříd. Malá, střední a vysoká třída. 

- **Malá zařízení** - Povětšinou se jedná o senzory a malé akční prvky (chytrá žárovka), tyto zařízení mají max, 16 bitový CPU, nízký výkon, cenu a spotřebu. Neběží na nich žádný OS, pouze 1 C-like script.
- **Střední zařízení** - Už větší, trošku komplikovanější zařízení. Jejich procesory jsou 16-32 bit. Povětšinou běží RTOS operační systémy a C/C++ aplikace.
- **Vysoká třída** - Povětšinou už plnohodnotné počítače s 32-64 bitovími procesory, běží na nich velmi koplexní aplikace. Běží na nich plně standartní systém (Linux)
Pro porovnání ještě přikládám tabulku.

![[06 - Tabulka tříd zařízení.png]]

Takže jaké parametry je důležité při stvbě IoT zvážit?
- CPU
	- Velikost možné adresovatelné paměti
	- Cena
	- Velikost
	- Odolnost vůči požkození/spolehlivost
	- Schopnost připojení prefiférií
	- Certifikace
- Architektura
	- ARM: nízká spotřeba, spousta konfigurací
	- RISC-V: open-source
	- CISCx64: vysoký výkon, spotřeba i cena
	- Harward/Von Neumann
		- V IoT povětšinou Von Neumann
- ISA s tím že definuje
	- Instrukce
	- Registry
	- Adresní režimy
	- atd.

---

### Bezdrátové sítě (Komunikační vrstva)
Z samotného principu IoT je příjemější a efektivější připojovat zařízení pomocí bezdrátových sítí. V bezdrátovích sítí se jako médium používá vzduch.

![[06 - WKomunikace.png]]

Bezdárový signál má svoje prametry. Těmi jsou:
- **RSSI**:Recived Singal Strength Indication: síla signálu přijata vysílačem
- **Citlivost**: Jaký signál jsem schpen zachytit
- **SNR**: Signal Noise Ratio
###### LoRa
LoRa řeší problém, které má Wi-Fi připojení a to je krátká vzdálenost, což u IoT tvoří problémy. LoRa narozdíl od Wi-Fi je schopná komunikovat i přes jednotky Km. Taky LoRa spotřabovává méně energie než stadartní Wi-Fi. Také je velmi spolehlivá díky své robustní modulaci. 

LoRa se dá rozlišit do několika vrstev kdy jsou rozdělený do několika vrstev, s tím že aplikace zabírají L7, LoRa MAC L6-L2 a LoRa modualce a žlutá vrstva L1.

![[06 - Vrstvy LoRa.png]]

Ale co jsou přesněji ty třídy komunikací. Každě IoT zařízení může přes LoRu komunikovat různě, podle toho kolik dat chce odesílat a kolik energie na tom chce spotřebovat.

- **Třída A**: Třída s nejnižší spotřebou energie. Zařízení odešle zprávu a pak čeká 2 sloty na odpověď. Potom komunikační rozhraní uspí do další komunikace

![[06 - Třída A.png|440]]

- **Třída B**: Zařízení funguje podobně jako na třídě A, ale v časových intervalech server posílá BEACON frame pro synchronizaci, díky němu může sám v určitých časových oknech komunikovat s zařízením bez nutnosti zahájením z jaho strany.  Sice toto způsobuje vyšší spotřebu, ale i nižší oodezvu koncového zařízení.

![[06 - Třída B.png|465]]

- **Třída C**: Zařízení trvale komunikuje a zárověň komunikuje. Největší spotřeba baterie ale nejrychlejší odezva.

![[06 - Třída C.png|493]]

**LoRa WAN**
LoRa má vašak ještě jeden problém, kdy se přes LoRu dá komunikovat pouze v LoRa síti. Pokud chceme data vynést ven na síť, tak potřebujeme použít LoRa WAN. Ta funguje tak že zařízení přes LoRa WAN gateway forwardují data ven na LoRaWAN server. Ten je už schopný data buď použít, nebo přeposlat přes normální síť.

![[06 - GatewayLoRa.png|431]]

###### MQTT
MQTT je lightweight protokol sloužící pro machine to machine komunikaci. Tyto stroje si mezi sebou posílají získanou telemetrii (výsledky měření). Jedná se o open-source protokol. 

MQTT funguje na principu publish/subscribe mechanismu. Kdy klient zásobuje (publish) a nebo získává (subcribe) data od brokera. Celkově to vypadá nějak takto:

1. **Klient** publikuje **zprávy** s daty **brokerovy** a při tom **odebírá** **zprávy** od něho z různých **témat**.
2. **Broker** čte a ukádá přijaté zprávy
3. **Zprávy** řadíme do různých **témat**

![[06 - MQTT odběr.png]]

Další nastavitelné vlastnosti MQTT:
- Retained Messages - Po odpojení klienta se zprávy na brokerovy zachovají
- Clean session - Po odpojení klienta se zapomene jaké témata odebíral
- Durable connection - Po odpojení klienta se nezapomene jaké témata odebíral
- Will - Zpráva kerá má být publikována, při ztrátě spojení
- Topics Trees - Témata jsou uspořádány v stromových strukturách
- Bridge - Spojení mezi brokery

MQTT má také režimy QoS (priority)

- Úroveň 0: Odešlu data. Nečekám na potvrzení
- Úroveň 1: Odešlu data, čekám na protvrzení, když nepříjde, tak pošlu znovu
- Úroveň 2: Odešlu data, čekám na potvrzení, potom pošlu požadavek na ukončení, pokud je potvrzen tak končím komunikaci.

---
### Bezpečnost
V IoT se bezpečnost moc neřeší. Proto vznikl starý vtip:"the "S" in IoT stands for "Security"". Jako vždy se snažíme zajistit CIA triádu. Ale u IoT nám vzniká další problém.

###### IoT botnets
Protože IoT častokrát nemá vhodné zabezpečení, tak je jednoduhé pro útočníky získat přístup k velkému množství zařízení. Tyto zařízení se pak dají velmi jednoduše zneužít pro korigivané DDoS útoky.

###### Fyzický přístup
Kvůli tomu že jsou IoT zařízení častokrát na nezabezpečených místech tak jde na ně praktikovat fyzické útoky jako je napříkald fyzická sonda, kdy čtu data z vodičů, nebo útoky postraními kanály. Těmito technikami lze získat klíče a prolomit šifrování komunikace.