Při připojování periferiích používáme vstupní a výstupní porty. Pro to aby jsme pochopili jak perfirefie připojujeme, tak je nejdříve potřeba pochopit jak fungují jednotlivé porty.
Porty dělíme na dva typy, vstupní a výstupní a to podle toho jestli data přijímají nebo odesílají. 
Porty na mikrokontrolerech jsou ještě speciální tím že dokážeme rozhodovat za chceme aby daný port byl zaplý. To se dělá díky bufferu (Budič). Můžeme ho vidět na obrázku, kde jeho pin S ovládá zda hodnotu přijímá či ne.

![[02 - Buffer.png|275]]
###### Typycký výstupní port
Typycký výsupní port vypadá nějak takto:

![[02 - Typycký výstup.png|255]]

Jak můžeme vidět tak součástí tohoto výstupu je filp-flop obvod typu D. Do něho zapisujeme hodnotu kterou chceme odeslat. S tím že tuto hodnotu můžeme zpátky přečíst na sběrnici.
###### Typický vstupní pin
Vstupní pin je ještě jednoduší. Ten se skládá jen z jednoho bufferu, u kterého ovládáme zda-li chceme číst, či ne. Ten vypadá nějak takto:

![[02 - Typycký vstup.png|324]]

Ale v dnešních mikrokontrolerech se tyto typy obvodů spíše nepoužívají. Dnes se spíše volí porty které mohou sloužit i jako vstup, tak jako výstup.
###### Typický port AVR
Takovéto porty mají mikrokontrolery AVR. Kde hodnota v registru ovlivňuje zda, je port vstupní nebo výstupní. I když na první pohled může vypadat složitě, tak můžeme vidět že se jedná pouze o trochu složitější kombinaci obou dvou přechozích obvodů.

![[02 - Typycký AVR port (VstupVýstup).png]]

Tyto porty také mohou mít další vlastnosti:
- Možnost aktivace vntřního pull-up rezistoru
- Generování přerušení
- Capture atd.
*Ta dvojvlnovková dioda je [Schmittův klopný obvod](https://cs.wikipedia.org/wiki/Schmitt%C5%AFv_klopn%C3%BD_obvod). Jeho cílem je čistit hrany signálu (analog => digitální)*

---
### Připojování perifériích
Teď když víme jak fungují jednotlivé porty, tak se můžeme podívat na to jak se k nim připojují periferie. Začneme tou nejjednoduší a to talčítkem.
###### Připojení tlačítka
Tlačítko lze zapojit dvěma způsoby a to podle toho, zda-li používáme pull-up rezistor nebo pull-up rezistor. Tyto rezistory rozhodují o tom jakou hodnotu bude nabývat pin, při rozepnutí tlačítka (up = 1, down = 0). 

![[02 - Tlačítko down.png|303]]![[02 - Tlačítko up.png|189]]

Některé mikrokontrolery mají integrované pull-up rezistory. Potom je stačí pouze zapnout.

**Doplněk: Zákmity** 
Tlačítka při změně stavu trpí tzv. zákmity, kdy chvilku po změně stavu kmitají mezi novou a starou hodnotou. Toto řešíme opakovaným čtením a pak následným porovnáváním hodnot.

![[02 - Zákmity.png|428]]
###### Připojení klávesnice
Ale co když tlačítek máme víc. Například celou klávesnici. Potom by připojování jednoho tlačítka na každý pin bylo silně nepraktické. Proto vyrábíme z tlačítek matici a zapojíme je nějak takto:

![[02 - Matice tlačítek.png|470]]

Při tomto zapojení cyklicky čteme řady tlačítek *(zápisem na výstup vyrábíme nulu)*. Při stisku talčítka v aktivované řadě pak víme jaký bylo aktivováno.

Pro čtení můžeme pak využívat analizace vstupů. Představme si takovéto schéma:

![[02 - Řada tlačítek.png|349]]

Pokud si správně volíme naše odpory (mocniny 2), tak pak podle výsledného napětí jsem schopen detekovat jakoukoliv kombinaci stisku tlačíek. *Jak to funguje při výpoču matikou po mě nechtějte, reálně to nevím.*
###### LED dioda
Led dioda se zapojuje velmi jednoduše. Diodu zapojím mezi zdroj a zem, a při tom omezím napětí (ideální napětí pro diodu je 2 - 20 mA) rezistorem, aby jsem diodu nespálil.

Zapojení k mikrokontroleru jsou dvě. Záleží na tom jestli mikrokontroler nabízí zem či zdroj.

![[02 - Zapojení diod.png|504]]

Led diody se také mohou nacházet v soustavách a tvořit displeje, mezi nejznáměší patří:
- Sedmisegmentové
- Alfanumerické
- Maticové
- Bargrafy
- a Speciální

---
### Napěťové hodnoty MCU
Naše MCU má spotřebu podle toho co používá pro svoje fungování. Pokud je složen z TTL *(Transistor-Transistor Logic)*, tak bude potřebovat napětí 5V, pokud se skládá z CMOS, tak potřebuje méně a to 3,3V.
Tyto hodnoty také ovlivňují hranice pro identifikaci logické 0 a 1. S tím že Ucc značí 1 a 0V značí 0 *(samořejmě ne přesně tyto hodnoty, je tam trochu rozsah)*. Analogové vstupy / výstupy se také pohybují mezi Ucc a 0.