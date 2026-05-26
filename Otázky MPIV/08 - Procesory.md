Procesor je mozkem počítače, ale může se lišit od výrobce k výrobci. Jaké vlastnosti mohou tyto procesory mít:

- **Frekvence:** Taktovací frekvence jádra udávaná v Hz (dnes GHz). Určuje počet instrukčních cyklů za sekundu a slouží jako jeden z ukazatelů výkonu.
- **Počet jader:** Udává, kolik samostatných výpočetních jednotek procesor má pro paralelní zpracování úloh.
- **ISA:** Instrukční sada (např. CISC/RISC) definující sadu instrukcí, kterým procesor rozumí, společně s jeho bitovostí.
- **Velikost vyrovnávacích pamětí:** Rychlé vestavěné paměti L1, L2 a L3, které zrychlují přístup k datům v RAM.

Jak píšu, jedním z rozlišovacích prvků je ISA. Podle ní se dají procesory velmi jednoduše rozlišit:

###### Architektura x86-64

Jeden z nejpoužívanějších typů procesorů. Používá se hlavně v PC a serverech. Nabízí skvělý absolutní výkon, ale mívá vyšší spotřebu a relativně vyšší cenu. Architektura původně vznikla u Intelu (jako 16/32-bit x86), avšak její 64-bitové rozšíření (x86-64) vytvořila společnost AMD.

- **Bitovost:** 64 bitů (se zpětnou kompatibilitou pro 32 bitů)
- **Instrukční sada:** CISC (uvnitř procesoru překládaná na RISC mikrooperace)
- **Typická frekvence:** Pohybuje se v jednotkách GHz (běžně 2,5 až 5+ GHz)
- **Výrobci:** Intel, AMD

###### Architektura ARM

Architektura navržená britskou firmou ARM. Dnes je využívaná hlavně v přenosných zařízeních, kde dominuje díky své vysoké energetické efektivitě a nízké spotřebě. Tyto procesory zcela ovládají trh s chytrými telefony, avšak v dnešní době se dostaly na takovou úroveň výkonu, že úspěšně pronikají do notebooků a serverů. Pro svou příznivou cenu a efektivitu se používají také v síťových prvcích nebo jednodeskových počítačích.

- **Bitovost:** 32 / 64 bitů
- **Instrukční sada:** RISC
- **Typická frekvence:** Od stovek MHz (mikrokontroléry) po jednotky GHz (telefony, PC)
- **Výrobci:** Apple, Qualcomm, MediaTek, Samsung (ARM architekturu pouze licencuje)

###### Architektura RISC-V

RISC-V je moderní open-source architektura, která na rozdíl od ARM nevyžaduje placení licenčních poplatků. Je populární v akademické sféře, mikrokontrolérech a vestavěných (embedded) systémech, ale rychle se rozšiřuje i do oblastí umělé inteligence, serverů a vesmírného průmyslu. Představuje flexibilní a vysoce modulární volbu. Jako koprocesor ji využívá například Raspberry Pi 5, samotná jádra pak najdeme v mnoha průmyslových čipech.

- **Bitovost:** 32 / 64 / 128 bitů (specifikace definuje tyto základní šířky)
- **Instrukční sada:** RISC (s modulárními hardwarovými rozšířeními)
- **Typická frekvence:** Od desítek MHz (IoT) po jednotky GHz (vývojářské desky)
- **Výrobci:** SiFive, Western Digital, Andes Technology (čipy s RISC-V staví stovky firem; ATMega používá architekturu AVR)

---
### Superskalarita
Když si vzpomeneme na pipeline, tak víme že ní prochází instrukce, které vykonává jedno jádro (přesněji jeho komponenty). Procesor se stává superskalární, když tyto komponenty zdvojíme. Tím zdvojíme počet pipeline a začneme vykonávat instrukce paralerně.
**Je důležité že toto zdvojení probíhá v jednom jádře. Napříkad dvě ALU pro fázi EX.**
Rozdělení a podoba těchto pipeline se dají kategorizovat do dvou kategorií:

- Unifikované: Několik stejných pipeline (zdvojíme procesor) V praxi se moc nepoužívá.
- Specializované: Různé pipeline, pro různé účely (celočíselná/float/paměť atd.)

I když tato multiplikace přináší větší výkon, tak přináší i svoje problémy:

###### Datové hazardy
Stejně jako pipelinig nám superskalarita přináší problémy s závyslostí dat. Tyto hazardy jsou:

**RAW** - Read after write
**WAR** - Write after read
**WAW** - Write after write

Poslední dva hazardy jsou avšak falešné a to proto že jdou odstranit Tomasuluvím algorimem, který spočívá v tom že se výsledek operace vždy do nového registru *(Velmi zjednodušeně řečeno)*.
###### Out-of-order execution a Reorder-Buffer
Kvůli tomu že dvě pipeline fungují paralerně, tak už nemáme jistotu v tom že jsou instrukce prováděné v správném pořadí. V reorder bufferu se zapisují výsledky operací, které již proběhli (klidně out of order). Cílem bufferu je to, aby výsledky se do registrů v pořadí, které je napsané v kódu a tím simuloval vykonávání v správném pořadí.

Do bufferu instrukce vstupují v programovém pořadí a taky z něho tak odcházejí, takže pokud dojte k provedení mimo pořadí programu, tak se čeká něž se dokončí starší instrukce před ním.

![[08 - RoB.png]]

Pokud se nepovede predikce skoku, tak společně s výplachem pipeliny se také musí z reorder bufferu smazat všechny instrukce novější než kritický bod (skok).

---
### Predikce skoku
Predikce skoku je jedním z řešení řídícího hazardu v pipeliningu, Predikci skoku dělá povětšinou HW procesoru, tudíž prediktor je součástí mikroarchitektury. Pokud odhadne špatně, tak se musí vypláchnou pipeline a v případě superskalární procesorů i RoB. <= obrovské zpoždění

Prediktory jsou statické a dynamické:
- Statické - Jednoduší schémata zadrátovaná v procesoru. Rozhoduje se podle daných pravidel. Menší přesnost. *Napříkad: Skoky u smyček nastanou, ale u podmínek ne.*
- Dynamické - Dedikovaná komponenta, tvoří si tabulku historie skoků (`adresa skoku` | `<skočil/neskočil>`) a pak se rozhoduje na základě statistiky.