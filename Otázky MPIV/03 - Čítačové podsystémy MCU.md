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

