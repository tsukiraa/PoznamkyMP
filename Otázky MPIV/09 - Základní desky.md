Základní deska je jedna z nejdůležitějších komponent počítače. Jejím úkolem je propojit mei sebou jednotlivé komponenty. Zákadní deska se povětšinou skádá z:
- Patice pro CPU
- Ne-volatilní čipy: Obsahují firmware, BIOS/UEFI, TPM
- Sloty pro operační paměť: Obvykle DIMM sloty obsluhující RAM paměti.
- Čipová sada: Tvoří rozhraní mezi CPU, hlavní pamětí a ostatními komponenty
- Generátor hodin: Sloužící pro synchrnoizaci
- Sloty por rozšiřující karty: PCI, PCI-e
- Konektory pro pevné disky: SATA, M.2

Desky také rozlišujeme do několika velikostí, nejdůležitější a nejpoužívanějšími typy jsou:

- ATX *(305-244mm)* - Nejpoužívanější standart. Vytvořen Intelem.
- MicroATX *(244x244mm)* - O 20% menší než ATX. Oproti větší verzi obsahuje méně rošiřujících karet. Běžná volba pro kancelářské počítače.
- MiniITX (170x170mm) - Nejmenší volba. Používá se hlavně v small-form-factor zařízeních. Jako jsou miniPC a domácí kina
- 
Při nákupu na komerčním trhu se desky dělí podle dvou hlavních kritérií:
1. **Platforma (Patice):** Desky jsou přísně rozděleny podle výrobců procesorů. Procesor AMD nelze osadit do patice určené pro Intel a naopak. (Příklady moderních patic: Intel LGA1700/LGA1851 vs. AMD AM5).
2. **Třída čipsetu:** Výrobci (Asus, Gigabyte, MSI, ASRock) osazují desky různými třídami čipsetů od AMD a Intelu, čímž segmentují trh:
    - Low-end (Kancelářské - např. Intel H610 / AMD A620)
    - Mainstream (Herní/Střední třída - např. Intel B760 / AMD B650)
    - High-end (Pro nadšence - např. Intel Z790 / AMD X670)

---
### Rozhraní PCI / PCI-E
PCI a PCI-E jsou systémové sběrnice sloužící k připojení rozšičujících karet *(GPU, Síťová karta atd.)*. Běžně počítače mají 1 - 3 rozšírující sloty *(Nepřesná informace)*
###### PCI
PCI je v dnešní době zastaralá a v podstatě nepoužívaná sběrnice. Jedná se o paralerní sběrnici. Tato sběrnice je také sdílená, takže při zapojení více karet docházelo k spoždění z důvodu arbitráže. Dnes se používá jen pro legacy komponenty.
###### PCI-E
Novější standart, který se hojně používá napříč hardware (Od slabšího Rabsberry po server). Jedná se o sériovou P2P sběrnicí. To znamená že karta má dedikované spojení s chipsetem/řadičem (PCI-E root compex), ten pak komunikuje s CPU a pamětí. PCI-E existuje v různých konfiguracích podle toho kolik nezávyslích linek pro komunikaci daná karta potřebuje a ty jsou: 1x, 2x, 4x, 8x, 16x. 

![[09 - PCI-E strom.png]]

PCI-E je v dnešní době tak používaná že neslouží pouze k připojení rozšiřujících karet, ale složí i pro zajištění interních spojů (Síť, řadič SSD atd.)

PCI-E má taky odrůdu NVM, které slouží pro vyskokorychlostní zapojení SSD disků.
Také existuje standart thunderbolt, který přenáší PCI-E signál společně s videem.

---

### USB
Periférní sériová sběrnice, jedná se o univerzální sběrnici pro nenátočná zařízení (Myš, klávesnice, flash paměť). Skládál se za čtyř vodičů (5V,GND,DATA+,DATA-) *(do USB 2)*, dnes má avšak už více (9-24). USB má několik verzí. Dnes již exustuje USB 5. USB připojuje zařízení do stormové struktury.

![[09 - USB strom.png]]

Každé zařízení má svoji 7 bitovou adresu => limitace na 127 zařízení, každé zařízení má svoje endpointy (lze připodobnit portů). Verze 3 a víš jsou full-duplex komunikace.

