Když potřebujeme aby mezi sebou dvě komponenty nějak komunikovali, tak používáme nějaké rozhraní komunikačního protokolu. Tyto rozhraní se liší podle toho zda komunikace probíhá paralerně či sériově. 
- **Sériově**: Data se posílají za sebou přes jeden vodič. Tato jednoduchost umožňuje komuikovat při vyšší frekvenci => rychlejší. Také zapojení je jednoduší, kvůli zapojení jen jednoho drátu. Nevýhodou je nemožnost použití paralerizace
- **Paralerní**: Data se posílají přes několik drátů najednou. To je obrovská výhoda. Problém přichází v synchronizaci a složitosti. Dnes se už moc nepoužívá.
V této kapitole se budeme bavit hlavně o linkách sériových, přesněji o:
- UART
	- RS 232
	- RS 485
- SPI
- I2C
- CAN
---
### UART, RSxxx
###### UART
Nejdenoduší komunikaci kterou v mikrokontrolerch máme je UART. Slouží pro komunikaci mezi zařízeními na vzdálenost jednotek centimetrů. Jejich propojení může vypadat nějak takto.

![[05 - UART.png|471]]

UART je docela volný standart a má dosti volné parametry, avšak pro úspěšnou komunikaci musí parametry na obou koncích stejné. Těmito parametry jsou:

- **Typ komunikace:** Jestli je komunikace full-duplex, half-duplex nebo simplex (na obrázku je full duplex)
- **Napěťové úrovně:**  V UARTu je logická 1 definována jako Ucc mikrokontroleru na nula jako 0V. Pokud se Ucc mezi MCU liší, tak komunikace nebude úspěšná
- **Bauderate:** Frekvence komunikace, nejčastějších hodnot kterých nabývá je 4800, 9600, 19200, 57600 a 115200.
- **Paritní bit:** Detekce a oprava chyb. Buď sudá, lichá nebo žádná (nejčastější).
- **Počet stop bitů:** Kolik se pošle jedniček na konci kominkace. Hodnoty jsou 1, 1.5 a 2
- **Počet datových bitů:** Kolik bitů se pošle během této komunikace. Buď 5, 7 , 8 bitů
- **Big Endian/Small Endian:** Rozdíl jestli se posílá od MSB nebo od LSB (UART je LSB)

Samotná komunikace funguje tak že se spustí tzv. start bitem *(změna na nulu)*. Potom se přenášejí datové bity s paritou na konci, zakončené stop bity (změna a udržení na jedničce).

![[05 - Průběh UART.png]]
###### RS232
 Jak jsme řekli UART komunikuje na velmi krátou vzdálenost (jednotky cm). Pokud chceme komunikovat na větší vzdálenosti, tak musíme využít dalších komunikačních standardů. RS232 je právě jedním z nich, protože nám umožňuje komunikovat na vzdálenost 15m.

Jedná se o jeden z historicky nejstarších standartů, který můžeme znát i pod názvem sériová linka. Jeho maximální rychlost přenosu se pohybuje kolem 20 kB/s. Komunikuje přes konektor DB-5. Jedná se o asymetrickou komunikaci.

Od UARTu se také liší napěťovíma urovněma:

|     | Vstup                   | Výstup     |
| --- | ----------------------- | ---------- |
| 1   | -5 až -15V typicky -12V | -3 až -15V |
| 0   | 5 až 15V typicky 12V    | 3 až 15V   |
Jinak se od něj nijak něliší. Pro to aby jsem mohly používat RS232 tak potřebujeme rozhraní, které převede UART na RS232, zapojení pak vypadá nejak takto:

![[05 - RS232.png]]

Dnes se už typycky tyto standarty převádějí buď z UARTu nebo z RS232 na USB. Například toto dělá FTDI.
###### RS422
Znova velmi podobný styl přenosu RS232. Jedná se o symetrický přenos. Dva vodiče (+ a -). Napěťové úrovně jsou na výstupu +-6V a na vstupu +-200mV až +-10V. Přenosová rychlost se pak liší dle délky přenosové sběrnice. Do délky12M je komunikační rychlost 10Mb/s a do 1,2km 100kB.

###### RS485
Má stejný základ jako RS232, liší se tak že "nepoužívá" napěťové úrovně ale spíš rozdíl mezi nimi. S tím že binární 1 zančí když je rozdíl menší než 200mV a nula když je větší než 200mV. Délka sběrnice je 1.2 km

###### Doplněk: IrDA
Infračervená komunikace funguje na stejném základu jako UART, pouze potom prochází speciální modulací (níže na obrázku), která se pak vysílá.

![[05 - IrDA.png]]

---
### Sériově periférní sběrnice (SPI, I2C)
Tyto sběrnice slouží k komunikaci mezi MCU a jednotlivími perifériemi (Display, paměť atd.). Šetří piny MCU, slouží pro komunikaci na krátkou vzdálenost (např. na základové desce.) Tady probereme dva typy:
- SPI *motorola*
- I2C *Phlilips*
###### SPI
Jedná se o master/slave komunikaci. Je to standartní typ co se týče komunikace mezi FLASH pamětí. Což znamená že master řídí to jak daná komunikace bude probíhat. Zapojení vypadá takto:

![[05 - SPI zapojení.png|436]]

Všechny periférie jsou k Masterovy připojeni 4 vodiči, dva z nich jsou společné sběrnice MOSI *(Master Output Slave Input)* a MISO (Master Input Slave Output). Z jejich názvu přímo vyplívá k čemu slouží. Pak jsou také připojení na společný SCLK, kam master generuje hodinový cyklus komunikace. Jako posledí, ke každému slaveu vede SS *(Slave Select)*, kterým si master vybírá s kým komunikuje. Toto zapojení znamená že pro zapojení N nodů je potřeba 3 + N vodičů.

Zapojení mezi dvěma, mezi sebou komunikujícíma registry, by mohla vypadat takto. Pokud master pošle osm taktů, tak si tyto registry mezi sebou vymění obsahy.

![[05 - SPI registry.png]]
###### I2C
I2C je narozdíl od SPI multi-master komunikace. To znamená že komunikaci může inicializovat více periferií najednou. V dnešní době se používá v PC vrámci SMBus v PnP.
Každé zařízení má na sběrnici pro idenifikaci přiřazenou adresu. (7 bitů). Zapojení vypadá takto.

![[05 - I2C zapojení.png]]

Napěťové hodnoty fungují tak že pro log. 0 uzemním sběrnici, zatím co pro log. 1 sběrnici nechám být. (Pull up 5V). Samotná komunikace potom probíhá tak že master pošle start bit (0) spoječně s adresou příjemcem a R/W bitem (1/0). Po potvrzení od příjemce se začnou posílat data. Vždy než se pošle další blok tak musí být potvrzené přijetí od příjemce. Jakmile jsou data poslána, tak je komunikace ukončena final bitem (1).

![[05 - Průběh I2C.png]]

Protože všechny masteři mají společnou sběrnici, tak se o ní musí dělit. První zásada je ta že pokud zaznamenám start bit, tak čekám na stop bit než komunikace skončí. Ale co když vysílám najednou. Proto paltí jednoduchá poučka, jakmile vysílají dva masteři, tak stále čtou stav sběrnice. Pokud si všinou že vysílají 1, ale komunikace je uzaměná, tak vědí že došlo ke kolizi a od komunikace odstoupí. Tímto pravidlem má prioritu vždy zařízení, které vysílá menší adresu.

---
### CAN
CAN je speciální sériová sběrnice, sloužící halvně pro propojení sériových jednotek v autech. Jako I2C se jedná o vícebodovou komunikaci (Jeden kabel více zařízení). Jeho cílem bylo nahradit UART v autech. Dokáže zařídit komunikaci pro distribuované řízení v reálné čase. Zaručuje vyskou úrovně bezpečnosti a spolehivosti. Dosahuje propustnosti 1Mb/s. Poměrně levné.

**Vlastnosti:**
- Priorita zpráv - stejné jako u I2C, rozdíl je v tom že zpráva na začátku nemá adresu ale arbitrační pole (obsahuje ID obsahu zprávy)
- Garantované zpoždění - CAN zajišťuje že se zpráva doručí do určitého časového limitu. Také zajišťuje časovou synchronizaci mezi řídícími jednotkami. Skvělé pro RTOS systémy
- Multi-master - Více zařízení může zahájit komunikaci
- Detekce a signalizace chyb - Pokud jednotka detekuje chybu, tak se od komunikace odpojí.

CAN na fyzické vrsvě komunikuje přes kroucenou symetrickou dvojlinu. (ISO 11898). Detekce 1 a 0 funguje podobně jako u RS485

![[05 - CAN datový rámec.png]]
