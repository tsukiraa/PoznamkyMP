GPU (Graphics-Processing-Units) neboli grafické karty, jsou jedny z komponent počítače. GPU má podobnou práci jako CPU a to je počítat, avšak narozdíl od procesoru brutálně pararelně. Používají se pro tzv. počty, kdy nad velkým množstvím dat vykonám jednu instrukci. *Např.: Sečtu 1000 dvojic čísel současně.* Framework pro využívání grafické karty se od výrobce k výrobci liší. Pro výpočty (GPGPU) NVIDIA používá CUDA a AMD OpenCL. Dále se používají i pro grafiku, kde jsou abstrahovaný nástroji DirectX, OpenGL nebo Vulcan. Pro pochopení jak tyto počty dělají avšak nejdříve musíme pochopit určté pojmy (pro NVIDII):

- Streaming Multiprocesor: Jedno *jádro* grafické karty. Běžně má 128-256 ALU. Novější grafické karty obsahují hruba 128 SM (RTX4090)
- Warp: nejmenší spustitelná jednotka na SIMT GPU. Obsahuje 32-64 vláken (pro 32 - 64 čísel)

Tyto velké paralerní instrukce můžeme na GPU spouštět dvěma způsoby,
###### SIMD (Single Instruction Multiple Data)
Grafická karta vykoná instrukci nad všemi datamy co jsou v ALU. Pokud se SIMD využívá, tak grafická kara může obstarávat pouze jeden proces.
###### SIMT (Single Instruction Multiple Threads)
Grafická karta vykonává instrukce na jednotlivými thready (čísly) ve warpu (skládá je ze 32 - 64 threadů). Tímto se dá rozdělit proces grafické karty a zmenšit problémy jako je divergence vláken.

GPU má také jiný přístup k vyrovnávacím pamětem a latenci pocházející z čtení/zápisu dat než CPU, místo toho aby se ji snažil snížit na minimum, tak ji GPU skrývá. To znamená že při vykonávání warpu už řadič načítá data (do GRAM nebo VRAM) pro další warp.

---
### Rozhraní pro přenos videa
Pro přenos informací z grafické karty na monitor používáme různé typy konektorů. Nejznámější jsou HDMI, DP a VGA

| **Konektor**         | **Klíčová verze**      | **Typ signálu**     | **Max. reálné rozlišení a frekvence** | **Hlavní vlastnost**                                                                                                                                   |
| -------------------- | ---------------------- | ------------------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **HDMI**             | 2.1                    | Čistě digitální     | 8K @ 60Hz/(4K @ 120Hz)                | **Televizní a konzolový standard.** S videem také přenáší audio.                                                                                       |
| **DisplayPort (DP)** | **1.4a**               | **Čistě digitální** | 8K @ 60Hz s DSC (4K @ 120Hz nativně)  | **Počítačový a herní standard.** Podporuje funkci **MST** (Multi-Stream Transport), což je řetězení více monitorů za sebou z jednoho portu na grafice. |
| **VGA (D-Sub)**      | _Není děleno na verze_ | **Čistě analogový** | Praktické maximum: 1080p @ 60Hz       | **Historický standard (1987).**                                                                                                                        |

---
### Dostupné grafické karty

Při výběru a hodnocení GPU sledujeme následující technické parametry:

- **Určení karty:** Herní (vysoký hrubý výkon, Ray Tracing), Profesionální (certifikované stabilní ovladače pro CAD/3D) a Serverové/AI (obří paměti pro strojové učení, často bez video výstupů).    
- **Konstrukce jádra:** Počet výpočetních jader (CUDA jádra u NVIDIA / Stream procesory u AMD) a přítomnost specializovaných hardwarových jednotek – **RT jádra** (pro výpočet realistického světla) a **Tensor jádra** (maticové výpočty pro AI a upscaling obrazu).
- **Paměťový subsystém:** Kapacita VRAM (dnes standard 12–16 GB pro hraní) a **šířka paměťové sběrnice** (128-bit až 384-bit), která zásadně ovlivňuje celkovou datovou propustnost karty. U spotřebního hardwaru se používají paměti **GDDR6/GDDR7**, u serverů extrémně propustné **HBM**.
- **Fyzické rozhraní:** Zapojení do základní desky přes sběrnici **PCIe x16** (aktuálně verze 4.0 a 5.0) a výstupy pro monitor (HDMI, DisplayPort).
- **Napájení a chlazení:** Energetická náročnost udávaná jako **TDP/TBP** (ve Wattech), která určuje požadavky na zdroj PC a typ chlazení (aktivní s ventilátory, pasivní u slabých karet, nebo vodní bloky). Moderní karty využívají vysokovýkonný napájecí konektor standardu 12V-2x6 (PCIe 5.0).