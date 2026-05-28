Jedna za komponent, kterou počítač potřebuje k funkconionalitě je paměť. Touto pamětí se nemyslí disk. Myslí se paměti, které jsou součástí paměťové hyrearchie.
### Proč je potřebujeme
Pokud si představíme náš procesor, tak operační paměti na první pohled vypadají docela zbytečně, protože bychom mohli zapisovat výsledky výpočtů přímo na pevný disk. Jenže tady přichází problém. Zápis na disk trvá dlouho. Než běžný HDD obslouží náš procesor, může to trvat klidně desítky milisekund, což pro procesor představuje spoustu vyhozených taktů. Proto jsme mezi disk a procesor postavili takzvané vyrovnávací paměti (**L1, L2, L3 cache a RAM**).

![[10 - Hirearchie pamětí.png]]

Při čtení z disku se potom načte celý blok dat, který se uloží právě do těchto pamětí pro rychlý přístup. Očekává se totiž, že procesor bude využívat i data uložená v bezprostřední blízkosti nebo je bude potřebovat opakovaně ve velmi krátkém čase (princip prostorové a časové lokality).
Úplně nejrychlejší pamětí procesoru jsou registry (klopný obvody), které dokáží procesor obsloužit okamžitě v rámci stejného taktu.
I když jsem dříve řekl, že RAM je skrytá paměť, tak tomu tak ) není, protože programy adresují hlavně ji. Do tohoto srovnání jsem ji však zařadil proto, že důvod její existence je dosti podobný tomu, proč existují paměti L1, L2 a L3 přímo uvnitř procesoru – slouží jako rychlý mezistupeň, který chrání procesor před drastickým zpomalením.

---
### Jak funguje?
Jak jsme již řekli, tak tyto paměti fungují na principu **časové a prostorové lokality**.
- Prostorová lokalita - Paměť doufá to že posobě jdoucí data jsou uložena blízko sebe.
- Časová lokalita - Paměť dofá v to že se stejná data budou používat v blízkém časovém horizontu.
Z těchto principů potom vniká celková funkconionalita, kdy čte celý blok paměti. Pokud potom procesor chce další hodnotu blízko ní tak odpoví z nejbližší (nejrychlejší) paměti co má.
Takže například, pokud projíždíme matici (seznam v seznamu), tak se vrámci cache více vyplácí procházet ji po řádcích (projedu jeden seznam a pak další) než po sloupcích (střídám mezi seznamy).

![[10 - Příklad lokality.png]]
###### Pojmy
- Cache hit - Data jsou v cache (LX), nemusí se číst z halvní paměti
- Cahce miss - Data nejsou v cache, musí se načíst z hlavní paměti <= zpomalení
- Hit/Miss ratio - Poměr obou hodnot

---
### Problémy vyrovnávacích pamětí
No takže jsme upravili data, ale co teď. Máme data v cache, ale ne v hlavní paměti. Tyto data, které se liší od hlavní paměti označujeme dirty bitem. Paměť můžeme z tohoto stavu synchrnoizovat dvěma způsoby.

- Write-through - Současně se zápisem do cache se zapisuje do hlavní paměti => pomalé, zatěžuje sběrnici
- Write-back - Cache odkládá přepis dat až na poslední chvíli. Zapisuje do ní pokud data z cache vyhzuje

Tento problém se avšak škáluje pokuď používáme multiprocesorové systémy. Když má jádro každého systému jinou kaskádu pamětí, tak se jednoduše může stát že při zápisu do jední z nich, může být paměť v té druhé zastaralá.
K tomuto problému se dá přistoupit dvěma přístupy. 

- MUTEX - Data zamknu, nedovolím aby další procesor/program s datama pracoval dokuď není verze napsaná v hlavní paměti
- MESI - Paměti si data označují a mezi sebou komunikují, ohledně validity dat. Bity které mohou nabývat
	- Modified = Obsah cache není stejný jako obsah paměti
	- Exclusive = Jenom já mám data v paměti a jsou stejné jako v paměti hlavní
	- Shared = Data jsou ve vícero cache, ale jsou stejná
	- Invalid = Data jsou neplatná

![[10 - MESI.png|229]]

---
### DRAM/SRAM
Máme dva typy pamětí, podle toho z čeho se skládají:

- SRAM: Dražší typ paměti, uchovává paměť zapomoci tranzistorů. Je rychlejší a fyzicky objemější. Používá se hlavně v L1, L2 a L3 cache
- DRAM: Levnější typ paměti, uchovává paměť pomocí kondenzátorů. Je pomalejší a fyzicky menší. Po nějaké době se v ní data musí obnovovat. Používá se pro RAM.

---
### Volně prodejné RAM paměti
Když kupujeme ram, tak nám záleží na těchto vlastnostech
- Velikost - Jak moc velké moduli jsou
- Patice - PC => DIMM;Notebook => SO-DIMM;
- Typ paměti: DDR4, DDR5, DDR3 <= U verzí se liší klíč (jiná patice)

Pamětí podle verzí:

| Vlastnost                   | DDR3                   | DDR4                   | DDR5                                                           |
| --------------------------- | ---------------------- | ---------------------- | -------------------------------------------------------------- |
| **Standardní napětí (V)**   | 1,5 V                  | 1,2 V                  | **1,1 V** _(PMIC přímo na modulu)_                             |
| **Efektivní frekvence**     | 800 až 2133 MHz        | 1600 až 3200 MHz       | **4800 až 8400+ MHz**                                          |
| **Šířka kanálu na modul**   | 1 × 64-bit             | 1 × 64-bit             | **2 × 32-bit** _(efektivnější pro vícejádrová CPU)_            |
| **Délka dávky dat (Burst)** | BL8 (přenese 8 balíků) | BL8 (přenese 8 balíků) | **BL16** _(přenese 16 balíků = naplní celou Cache Line naráz)_ |