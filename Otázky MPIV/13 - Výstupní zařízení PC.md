### Display
Displeje fungují tak že na ploše zobrazují různé objekty. Nejčastěji fingují na principu svítících pixelů, ale existují i vyjímky typu E-ink.
###### LCD displeje
Funguje na principu toho že blokuje části podsvícení. To blokují tekuté krystali. Pokud jsou pod napětím tak blokují a pokud ne tak neblokují. Při tomto blokování blokují části pixelu (RGB) . Takže takhle je potom displej schopen vytvořit různé barvy, kdy např. zablokuje červenou a zelenou, tak pixel vypadá jakože svítí modře. LCD displejů máme více druhů: **TN** (rychlé, špatné úhly), **IPS** (věrné barvy, skvělé úhly), **VA** (dobrý kontrast).
###### (AM)OLED
Fungují na přesně opeačném principu LCD. Každý pixel je sestrojen z několika diod (RGB), a barvy mícháme podle toho jak nám displeje svítí. Pokud chceme aby displej svítil modře, tak rozsvítíme pouze modrou diodu pixelu. Výhodami je nižší latence a dokonalá černá (lepší kontrast). Nevýhodama je rozostření a vypalování pixelů. 
###### E-ink
E-Ink funguje na tom principu, kdy je v displeji pigment (černý s negativním nábojem, bílý s pozitivním). Ty se pohybují pomocí **statické elektřiny** (přitažlivost plusu a mínusu), kde se má vykreslit text nebo obraz. Výhodou je nízká spořeba baterie, kdy spotřebovává energii jen při změně. Nevýhodou je nízká obnovací frekvence.

Další vlastnosti které jsou vhodné pro zvážení při nákupu:
- Rozlišení: FullHD, 2K, 4K atd.
- Obnovovací frekvence: 60,120, 144Hz
- Svítivost: Měřena v nits

### Tiskárny
Tiskárny fungují tak že na papír tisknou text nebo obrázky. Také mají několik typů.
###### Termální tisk
Termální tisk funguje tak že tiskne na termálně sensitivní papír, kde pokud papír ohřeje tak zčerná. Potom precizním ohřívání papíru jsme schopni vytisknout nejčastěji text. Používá se nejčastěji v tiskárnách účtenek. Výhodou je to že pro tisk nepotřebuje toner, potřebuje pouze papír. Nevýhoda je ta že tiskne pouze černobíle
###### Ink-jet
Funguje tak že na papír nanáší různě baravný inkoust malími tryskami. Výhody jsou levnější než tiskárny laserové. Nevýhodou je možné ucpání trysek a rozmazanost obrazu při špatném nakládání po tisku (Inkoust neuschl).
###### Laserové
Laserem nanáší na válec elektro-statické pole, které pak nanáší toner na papír.  Toner se pak na papíře zapíká. Jejich výhodou je to že jsou rychlejší. Nevýhoda je to že mohou být dražší.
###### Jehličkové
Princip je takový že jehla velmi rychle přes barvící pásku vytečkovává znaky na papír. Výhodou je nízká spotřeba toneru, nevýhodou je jednibarevný tisk.

#### Doplněk: Aditivní/Subtraktivní proces barev
Můžeme si věimnout že u tiskáren a displejů se používají jiné označení pro barvy. U displejů je to RGB a u tiskáren CMYK. Neliší je jenom základními barvami, ale i tím jak s barvami pracují. Tiskárny pracují s subtraktivním modelem, tudíž kód FFFFFFFF by znamenal černou. U RGB to je naopak, kdy pracuje s modelem aditivním, tudíž FFFFFF je bílá.

---
### A/D a D/A převodníky
V elektronice neumíme moc dobře pracovat s analogovími hodnotami, proto si je musíme převádět na hodnoty digitální. Např.: představme si že chceme měřit napětí. Dané napětí je analogová hodnota, tudíž je nekonečně přesná, řekněme že pro náš příklad je proud 6,1200000122...V. A/D převodník vezme tuto hodnotu a promění ji do čísla, s určitou hodnotou, s kterou počítač či obvody  jsou schopni pracovat (binární číslo). Pro pochpení je potřeba pochopit dva pojmy. Vzorkovací frekvence a bitová hloubka.

- Vzorkovací frekvence - Když chceme převádět měnící se analogový vstup tak ho musíme vzorkovat (aka. kdy se podíváme co se venku děje). Toto vyjadřujeme jako vzorkovací frekvenci, která ukazuje kolikrát za sekundu danou hodnotu změříme
- Bitová hloubka (Kvantizace) - Pro kolik hodnot daný vstup moniturujeme. Přesnost výstupu.

**Doplněk: Nyquist-Shannonův teorém**
Pro přesnou rekonstrukci signálu je potřeba alespoň dvojnásobná vzorkovací frekvence, než je frekvence měřeného signálu.

#### A/D převodníky
Typycky slouží jako převod napětí na číslo (viz příkald). Na vstupu je  obvykle analogový multipexer (pro výběr vstupu). Bitová šířka bývá běžně (8, 8+2n ... 16 bitů | n naleží <0;8> N).

![[13 - AD převodník.png]]

Uvnitř funguje na základě aproximace. Analogový vstup je posílán do komparátoru, který je porovnáván s výstupem D/A převodníku, který převádí data z aproximačního registru. Pokud hodnota D/A větší, tak se hodnota v aproximačním registru zmenší, pokud je zase menší, tak se hodnota zvýší. Hodnota z aproximačního registru se pak zapisuje jako výsledek. 

![[13 - Aproximační převodník.png]]
#### D/A převodník
D/A v podstatě funguje jako zapojení tlačítek při využívání analizace vstupů, kdy pokud zapojíme rezistory mocniny dvojky, tak že jsou na ně správně přivedeny bity hodnoty. (Kdy MSB jde na rezistor s nejmenším napětí a LSB s největším). Tak jejich výsledným součtem hodnot napětí získáme analogový výstup.

![[13 - DA převodník.png]]