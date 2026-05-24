Mikrokontroler je ozančení pro jednočipový počítač. To znamená že jeden čip obsahuje všechny věci co potřebuje počítač ke svojí práci. Tím je procesor (ALU), operační paměť a pevnou paměť. Častokrát obsahují i spoustu podpůrných obvodů, které nejčastěji slouží pro vstupní a výstupní piny.

---
### Architektury
Ve světě mikroprocesorů existuijí dvě nejčastější architektury. Von Neumannova a Harvardská architektura. Tyto architektury se liší tím jak přistupují k rozdělení a přístupům k paměti.

**Von Neumannova architektura** je architektura pro kterou je typické sejdnocení paměti programu (instrukce, chod atd.) a paměti dat. Výhoda této architektury je zjednodušení, kdy nemusíme rozlišovat instrukce pro přístup do paměti programu a paměti dat, což vede k zjednodušení čipu. Další výhodou je povětšinou menší cena. Nevýhoda je povětšinou v omezení na jednu sběrnici mezi procesorem a pamětí. *<= sériový přístup*

![[01 - Von Neumann.png]]

**Harvardská architektura** je architektura kde dochází k rozdělení paměti. Hlavní výhodou je paraleriace přístupu *(k paměti dat se přistupuje nezávisle od paměti instrukcí)*. Povětšinou toto hardwarové rozhodnutí vede i k větší bepečnosti mikrokontroleru. *(menší šance k neoprávněnému přístupu do paměti programu)*. Avšak nevýhodami je větší složitost čipu a vyšší cena.

![[01 - Harward.png]]

---
### Architektura instrukční sady (ISA)
Architektura instrukční sady *(Architektura souboru instrukcí (ISA))* je abstraktní rozhraní mezi HW a SW, které definuje základní náležitosti procesoru. Mezi tyto věci co ISA definuje patří:

- Instrukční sady (CISC x RISC)
- Sady a velikosti registrů (flagové registry,)
- Organizaci paměti (Von Neumann/Harward)
- Adresní módy
- atd.

Mezi nejčastěji používané ISA v dnešní době patří:

- IA-32 (Intel)
- x86-64
- ARM32, ARM64
- AVR
- RISC-V

###### Instrukční sady
Instrukční sady dělíme do dvou velkých skupin RISC a CISC. **RISC** je redukovaná instrukční sada. To znamená že obsahuje pouze základní instrukce a od programátora se očekává, že složitější a specialiovaný funkce vyrobí právě z nich. **CISC** je naopak komplexní instrukční sada, která obsahuje i mimo základní instrukce i instrukce rozšířené a specializované. Ty jsou častokrát optimalizované a šetří vývojáři čas (pokud píše v asembleru).

Obě řešení mají své výhody a nevýhody. **RISC** sice zjednodušuje celkovou architekturu počítače, ale jednoduchíma instrukcema ředí kód, mezitím co **CISC** má kód hustší, ale trpý složitými překaldači kódu a řadiči. Také u CISC vzniká problém kdy vývojáři a překladače nevyužívají složitější instrukce, proto platí že 89% programu je tvořeno 1% instrukční sady

V dnešní době se CISC používá hlavně v architekturách pro PC a RISC v mikrokontrolerech a telefonech. Je avšak důležité říci, že se v dnešní době upouští od CISCu a přemýšlí se o přesunu na architerktury s RISCovou sadou. Například když je CISC jenom ve frontendu, ale obsluhují ho RISC procesory (překlad od RISC ISA).

Instrukce v istrukčních sadách také dělíme a to na:
- Aritmeticko-logické (+, -, ×, ÷ / ADD, SUB, LSL atd.)
- Pro práci s pamětí (LD,ST)
- Řízení toku programu (JMP, BREQ)
- Speciální operace

Dále je potřeba zmínit, že CISC umí provádět výpočty přímo s hodnotami v paměti *(vztah Registr - Paměť)*, zatímco RISC provádí veškeré výpočty výhradně nad registry *(vztah Registr - Registr)* a do paměti data pouze čistě načítá nebo ukládá.
###### Doplněk: Adresní módy
Celkově říká jak se ukazuje na paměť a data. Je jich několik:
- přímý operand `ADD R1, 5` (konstanta v kódu)
- regisrtový přímý `ADD R1, R2` (přímo ukazuju na registr s hodnotou)
- registrový nepřímí `LD R1, [R2]` (ukazuji na registr, kde je hodnota adresy paměti)
- bázový s offsetem `LD R1, [R2 + 4]` (registr s odkazem do paměti + offset *pro procházení seznamu*)

---
### Mikroarchitektura
Mikroarchitektura je fyzické provedení ISA. Jednotlivá mikroarchitektura například definuje velikost paměti, počet jader, rozložení registrů atd.
Stevebními prvky těchto procesorů *(a jejich mikroarchitektur)* jsou:

- Registry
- Multiplexory
- ALU *Algoritmo-Logická Jednotka (Unit)*
- Paměť programu
- Paměť dat
- Registrové pole

Níže je obrázek jak se z těchto částí dá postavit mikroprocesor:

![[01 - Jádro RISC-V Zjednodušeno.png]]

###### Instrukční cykus
Procesor během jednoho taktu musí stihnout celý instrukční cyklus, ale co to vlastně ten instrukční cyklus je? Instrukční cyklus tak nějak popisuje co má procesor všechno udělat pro vykonání instrukce. Ten se rozděluje do 5 instrukčních fází. Jimi jsou, podle toho jak jdou v pořadí: Načítání instrukce, dekódování instrukce, čtení instrukce, provedení instrukce a uložení výsledku. Více o nich ale až v otázce číslo 04.

![[01 - Instrukční cyklus.png]]
###### Řadič
Řadič jak jeho jméno napovídá řídí procesor. Dělá dvě činnosti: Dekóduje operační znak *(část instrukce co říká co se bude dělat)* a pak to že podle něho a dalších proměných (stavové registry atd.) tvoří řídící signály.

Např: přečte `ADD` takže:
- Nastaví ALU na sčítání
- MUX nastaví tak že na vystupu ALU jsou hodnoty vybraných registrů
- Nastaví WE

Řadič se realizuje několika způsoby:
- Kombinačně - Velmi jednoduché pro řešení
- Sekvenčně 
	- Obvodově - Napevno zadrátovaný
	- Mikroprogramovatelný - Řízený mikrokódem

###### Doplněk: Měření výkonu
Výkon procesorů se častovkát vyjadřuje v Hz. Hz v tomto případě značí kolik instrukčních cyklů zvládne vykonat za jednu sekundu. (resp. frekvence jeho hodin)