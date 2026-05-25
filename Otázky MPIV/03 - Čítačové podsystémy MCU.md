V MCU jsou pro fugování s časem a vytvoření sychroních obvodů čítačové subsystémy. Ty se skládají komponent co vyrábějí nebo úzce spolupracují s hodiným signálem. Tyto komponenty jsou časovače, čítače a předšličky.
### Hodiny MCU
Začneme hodinama protože on je zdrojem časového signálu. Jsou integrované přímo v MCU. Tento signál je generovaný krystalem a vypadá jako hranatá vlna, která osciluje mezi 1 a 0. U těchto signálů měříme jejich frekvenci v Hz.
Hodiny jsou častokrát připojený na další komponenty, které svím signálem řídí.
### Čítače/Časovače
Tento hodinový signál potom využívají čítače a časovače pro měření času. Samozřejmě nevyužívají jenom ten, mohou taky využívat signály z venčí. Ale jak tedy fungují? Jedná se v podstatě o registr do kterého jde číst a zapisovat, který se s každým signálem buď zvětší nebo zmenší o 1. Přetečením daného registru se dá vyvolat přerušení. Níže přikládám velmi zjednodušený obvod, toho jak vyadají.

![[03 - Čítač easy.png]]

Tak potom jistě máte otázku:"Čím se tedy liší čítač od časovače?" Liší se tím s jakým signálem tento obvod pracuje (na obrázku značen CLK)

- **Čítač** - Signálem je nepravidelný (častokrát externí) signál. Počítáme kolikrát jsme signál dostali.
- **Časovač** - Signálem je pravidelný signál (nejčastěji hodiny MCU). Přetečením registru měříme časové intervaly.

### Předělička
Předělička slouží k pomalení čítače/časovače tím že dělí frekvenci příchozího signálu. Ve skutečnosti je předělička také čítač, ale hodnoty které nabývájí její bity v registru tvoří dělené signály, kterými dělíme původní frekvenci. Pro bližší vysvětlení přikládám obrázek:

![[03 - Průběh předěličky.png]]

V některých MCU se nachází tzv. čítače s konfigrovatelnou předěličkou, což umožňuje větší kontrolu nad řízením počítání daného signálu.

![[03 - Čítač s konfiguriovatelnou předěličkou.png]]

###### Generování časových intervalů
Časovače sčítače lze využívat pro pro měření času. A to tak že spustím přerušení po přetečení registru sčítače. Nejlepší je když velikost registru sčítače přímo odpovídá 
času, který trvá pro přetečení, na našem signálu. Pokud ne tak, musíme použít nějakou tatktiku pro zvíšení či snížení intervalu.

**Generování intrvalu delšího , než rozsah čítače**
- Počítám kolikrát mi čítač přetekl (napříkalad potřebuju dobu 4 přetečení)
**Generování intervalu kratšího, než je rozsah čítače**
- HW cesta - Do registru OCRnA *(nějakej random AVR registr)* zapíšu hodnotu do které se má čítač restartovat po přetečení
- SW cesta - V obsluze přetečení zapíšu hodnotu po resetu.

### Capture registr a Compare registr
Čítače se taky nachází v komponentách, které se jemnují capture a compare registr. 
###### Capture registr
Capture registr slouží k zaznamenávání přesného času při přístupu na nějaký vstupní pin. Funguje tak že společně s hodnotou na pinu se zapíše čas (obsah registru čítače připojený na MCU hodiny.) a potom vyvolá přerušení.

![[03 - Capture registr.png|495]]
###### Compare registr
Funguje tak že se obsah čítače, který je připojený na hodiny porovnává s compare registrem. Pokud oba mají stejnou hodnotu, tak vyvolají přerušení a dají 1 na výstupní pin.

![[03 - Compare registr.png|495]]

### PWM
PWM je modulace, která se používá jako metoda řízení elektrického výkonu. Řídí výkon tak, že velmi rychle přechází mezi nulou a jedničkou, ale tak aby celková doba stavu 1 ku stavu 0 byla podle přání uživatele. Vypadá takto:

![[03 - PWM.png|440]]

Na mikrokontroleru lze  generovat PWM dvěma způsoby:

- SW: Vytvořím kód který dělá něco podobného
	1. `while true:
		1. `wirte 1 na pin
		2. `wait <dutycycle> ms`
		3. `write 0 na pin`
		4. `wait <durationtime - dutycycle> ms
- HW: Pro hardwarové řešení použijeme compare registr. Do compare nastavíme hodnotu která odpovídá poměru <nastavená hodnota> ku velikost registru čítače. Pokud nastane schoda mezi registrem čítače, tak se pin nastaví na nulu. V obsluze přetečení čítače nastavíme pin na 1. Výsledný průběh by měl vypadat takto:

![[03 - Průběh PWM.png]]
