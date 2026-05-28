Paměti RAM (u procesorů sloužící jako hlavní paměť) má jednu obrovskou nevýhodu. Jedná se o volatilní paměť, což znamená že se data po vypnutí systému smažou. Pokuď potřebujeme uchovat data po vypnutí systému, tak potřebujeme pevné disky (HDD, SSD) neboli zařízení pro ukládání dat.
###### HDD (Magnetické disky)
Magnetické disky fungují na základě tvoření magnetického pole na plotnách disku. V dnešní době se jedná o pomalejší typ disku (odezva 5 - 10ms), proto se nehodí pro ukládání operačního systému. Dnes se využívájí hlavně pro ukádání uživatelských soborů (dokumenty, fotky atd.)
**Vlastnosti**
- Velikost: 1 - 32 TB
- Rychlost otáček: 5400 RPM - 7200 RPM - 10000 RPM
- Životnost: Větší než u SSD, žádná degradace buňek
- Fyzická oddolnost: Křehký na pády, problém s magnety
- Užití: Soubory, zálohování

###### SSD (Solid-State-Disk)
SSD funguje na principu udržování nabití v disku (V NAND obvodech). Jsou o mnohem rychlejší než HDD, ale jsou považovány za nespolehlivé (díky degradaci polí při opakovaném zápisu), proto se používají hlavně na ukádání dat, kde je potřebná nízká latence (Soubory operačního systému/Hry/swap). Pro rychlejší připojení lze připojit na PCI-E sběrnici (M.2 NVMe).
**Vlastnosti**
- Velikost: 0,1T - 8 TB
- Rozeznání úrovní náboje *(čím víc úrovní, tím větší úložiště, ale nižší životnost a latence)*: SLC, (3D)TLC, (3D)QLC
- Životnost: Nižší, degradace buňek
- Fyzická oddolnost: Odolný vůči pádům a magnetům
- Užití: Soubory OS, Hry, swap

---
### SD karta
SD karty fungují na stejné principu jako SSD, jsou to NAND flash paměti v menším form factoru. I když se u SD karet používá SD nebo SPI.
 - SPI to znamená že mezi nima tvoří komunikaci 4 řídící vodiče (MOSI,MISO,SCLK a SS) a 2 pro napětí (Ucc a GND).
-  SD busu je rozdílem to že komunikace je paralerní a half-duplex. Tudíž se řídí přes 6 řídících vodičů (DAT0 - 3,CMD a CLK) a 2 pro napětí.

---
### FLASH paměť
Typy pamětí flash jsou v dnešní době jedna z nejvíce používaných technologií co se týče úložných zařízení. Jedná se o elektronicky mazatelnou paměť. Její organizace je po blocích (mazání) a stránkách (čtení), kdy organizace bloků je libovolná dle výrobce čipu (obvykle 64kiB). Při přepisu buňky se musí smazat celý blok, a pak se s úpravama znova zapsat. Používá se v USB přenosných pamětěch, SD kartách, SSD discích a čipech (BIOS/UEFI/TPM).

**Vlastnosti (typy flash)**
- **Rozhraní:**
	- sériová
	- paralerní (emulace (E)EPROM)
	- speciální (USB)
- **Technologie: (z čeho je)**
	- NAND *velké kapacity, pomalejší W/R*
	- NOR *menší kapacity, rychlejší zápis, velmi rychlé čtení*
- **Organizace**
	- pevná vs. variabilní velikost bloku
	- více bankové *více nezávyslích částí*
	- se zavádějící sektorem *pro spouštění OS*
- **Co chceme aby dělala?**
	- Paměť dat
	- Paměť programu
	- Speciální paměti

Protože se také paměti typu FLASH opotřebovávají s každým zápisem, tak se používá tzv. FTL, který virtualizuje adresaci paměti a data ukládá tak, aby co nejvíce zajistil zdravý paměti.

###### Doplněk: EEPROM,NVRAM, EPROM
 - EEPROM: Elektronicky přepisovatelná paměť, adresace po bajtech, paralerní/sériová komunikace
 - NVRAM: baterií zálohovaná paměť typu RAM
 - EPROM: Lze smazat UV světlem. Dnes používaná jako OTP (One-time-programeble)