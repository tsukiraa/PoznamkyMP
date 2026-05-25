Instrukční cyklus je základní opakující se proces, při kterém procesor (CPU) vykonává strojové instrukce programu. Je rozdělen do 5 fází. Tyto fáze jsou:
1. Načítání instrukce (IF): Má za úkol vypsat instrukci z paměti instrucí na základě čísla v PC (progam couteru)
2. Dekódování instrukce (ID): Dekodér překládá instrukci na operand (s čím se dělá) a opcode (co se dělá), tu potom načte z registru *skrytá fáze OF*.
3. Provedení instrukce (EX): Procesor provede instrukci uvedenou v opcode na operandech
4. Zapsání do paměti (MEM): V této fázi probíhá čtení nebo zápis do hlavní paměti
5. Zapsaní do registrů (WB): V této fázi se ukládají výsledky do registrů
Při vykonání instrukce procesor musí fyzicky projít všechny tyto fáze s tím že avšak některé může logicky přeskočit. *Např. Když se nic do paměti nezapisuje, tak se multipexingem přejde do fáze WB*

Pokud se podíváme na velmi zjednodušený obraz procesoru mikrokonterleru, tak tyto komponenty mají za úkol vykonávání jednotlivých instrukcí.

![[04 - Instrukční cyklus + Procesor.png]]

### Pipelineing (řetězení)
Dříve před pipelineingem každá instrukce musela čekat na tu druhou než prošla všema komponentama. S řetězením instrukce pouštíme za sebou než projdou všema fázema.

![[04 - NoPipelineing.png|411]]
![[04 - Pipelineing.png]]

##### Hazardy
I když je řetězení efektvní, tak vznikají hazardy. Vznikjí ná tři typy hazardů.
- Datové hazardy
- Řídící hazardy
- Strukturální hazardy
###### Strukturální hazard
Ten vzniká v Von Neumannově architektuře. Protože máme pouze jednu sběrnici pro data i instrukce, takže při pipelinigu vzniká logicky ke kolizi.
**Řešením je:** Používat Harwardskou architekturu
###### Řídící hazard
Nastává při skoku v programu *(instrukce `JMP, BREQ` atd.)*. Před vyhodnocením nevíme jakou další instrukci chceme spustit. 
![[04 - Řídící hazard.png]]
**Řešení:**
- STALL - Nenačítáme další instrukce dokud nevíme výsledek
- FLUSH - Vypláchnutí pipeline. Pokračuji tak, jako kdyby se neskočilo, když se skočí, tak vše zahodím. Kombinuje se s predikcí skoků s tím že tipuji že je pravděpodobnější. 
###### Datový hazard
Instrukce potřebuje pracovat s daty, které jsou závyslé na úpravách instrukce prováděné před ní. Jsou tři RAW, WAR a WAW, avšak jediný skutečný hazard je RAW (Read after Write).
**Řešení:** 
- STALL - Počkám než se vykoná instrukce, která data přepisuje
![[04 - Stall.png]]
- Forwarding -  Data se přepošlou přímo pro instrukci která ji potřebuje. (Přeskoční fáze MEM a WB) 
 ![[04 - Forwarding.png]]

---
### Přerušení
Přerušení je proces kdy se procesor informuje o ukončení události. Jakmile se zaznamená přerušení, tak po dokončení instrukčního cyklu si uloží návratnou adresu a (pokud zrovna není vykonání přerušení blokováno (obsluhuje se jiné)) skočí na adresu (vekor přerušení) obsluhy přerušení. Obsluha přerušení musí být co nejkratší. Ideálně skok na jinou adresu.

![[01 - Instrukční cyklus.png]]![[04 - Obsluha přerušení.png|192]]

Přerušení mohou být vnitřní a vnější.
- Vnější - asynchroní od pefriferií. Dále rozlišujeme na maskovatelné (Lze ignorovat (pin)) a nemaskovatelné (Nelze ignorovat (Pokles Ucc))
- Vnitřní - Pochází od procesoru. Nejčastěji způsobeno chybou (dělení nulou, watchdog,breakpoint atd.)