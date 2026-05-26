RTOS je spolehlivý operační systém, který garantuje správné výsledky v určitém čase. To nutně neznamená že RTOS garantuje výkon. To co garantuje je prdikovost a determiničnost času (doby výkonu) a logiky.

*Například kvůli tomu RTOS systémy nikdy nevyužívají programy s garbage collectrem => nedetermičnost*

Pro pochopení toho jak RTOS a jeho plánování funguje, si nejdříve musíme probrat pár důležitých pojmů.
- Úloha (task): Sekvenční úsek kódu (funkce/program)
- Dávka (job): instance úlohy, která se potom plánuje
- Zdroj: To na čem dávka běží (HW)
- Čas uvolnění dávky (realese time): Je to konkrétní časový okamžik, kdy se dávka stane připravenou ke spuštění
- Relativní deadline: Doba mezi release time a deadline.
- Doba odezvy dávky: Jak dlouho trvalo od release time do reálného dokončení úlohy (ne deadline)
- Laxita - Volný čas před deadline mínus doba nutná pro výkon. $$\text{Laxita} = (\text{Absolutní deadline}) - (\text{Aktuální čas } t) - (\text{Zbývající doba běhu})$$
- WCET - Worst-Case-Execution-Time: Nejdelší doba běhu jedné dávky. Lze zjistit pouze u deterministických úloh => žádná rekurze.  Nutnost pro funkčnost RTOS

![[07 - Pojmy a deadline.png]]

RTOS také dělíme na dva druhy. Soft a Hard. Liší se tím jak moc je výsledek užitečný po deadline:

- HardRTOS - Výsledek je k ničemu pokud se nestihne ho konce dávky. Používá se tam kde jsou časové garance pro život důležité. (Letectví, železnice, doprava)

![[07 - HardRTOS graf.png]]

- SoftRTOS - Výsledek je užitečný i po skončení dávky, jen méně. Tam kde spolehlivost času používaná pro kvalitu služby. (Telefon, Online PC hry, UI)

![[07 - SoftRTOS graf.png]]

---
### Spolehlivost
Protože se RTOS používá hlavně u spolehlivých systémů, tak se musíme pobavit jakými dalšími metodami tyto zůstavají spolehlivé.
To že je zařízení spolehlivé, znamená že při poruše selže bezpečně. (Např. Výtah nespadne při poruše.). Je několik typů jak jsou zařízení spolehlivá:

- Fail-operational/Fault operational: Zařízení musí zůstat funkční i při selhání. Řeší se nejčastěji redundancí (žádný single-point-of-failure). Většinou je navrženo tak že po poruše zůstává funkční v omezeném stavu. (Např. Jaderná elektrárna, autopilot, medicína)
- Fail-safe: Zařízení neohrožje uživatele při selhání. Např. Pokud je nefunkční pedál plynu v autě, tak se odpojí místo toho aby do auta stále posílal plyn.
- Fail-passive - Pracuje i po celkovém selhání ale pasivně. Např. Zámek od trezoru zůstane při poruše zamčený.

---
### Plánovače
Plánovače mají tři různé valstnosti, které upravují jejich funkčnost:
- Statické/Dynamické:
	- Statické plánovače: off-line, probíhá předem, je nutná dokonalá znalost úloh, plnš deterministický
	- Dynamický plánovače: probíhají za běhu, adaptivní, nutné pro obsloužení aperiodických a sporadických úloh
- Neperemptivní/Preemptivní
	- Nepreemptivní plánovač: Plánovač nemůže přerušit úlohu. Úloha čeká až sama skončí
	- Preemptivní plánovač: Plánovač může úlohu přerušit
- Centralizovaný/Necentralizovaný
	- Centralizovaný plánovač: Je řízen jedním zařízením. Single-point-of-failure
	- Necentalizovaný plánovač: Je řízen více zařízeními. Vysoká režie

Máme také různé typy úloh. Těmi jsou:

- Periodické - Pravidelné úlohy, ve kterých se udržují dávky *(opakuje se jednou za 5 sekund)*
- Sporadické - Úlohy s nějakými časovími garancemi, jako například časový rozestupy
- Aperiodické - O časech úlohy nevím nic. Může přijít kdykoliv.
###### Statické plánování
Možné pouze pokud pracujeme s periodickými úlohami. Dané úlohy pak naskládáme na timeline aby se zvládly dokončit do deadline.

![[07 - Statické plánování.png]]

###### Dynamické plánování
Máme dva druhy dynamických plánovačů. S statickou prioritou a s dynamickou prioritou.

- Statická priorita - Všechny úlohy mají neměnící se prioritu. Méně flexibilní. Výkonostně náročné (oproti statickému plánování)
- Dynamická prioria - Úlohy mají různou měnící se prioritu. Více flexibilní. Výkonostně nejdražší 

**Příklad statický** 
- RMS - Priorita úlohy se určuje podle velikosti periody. Čím menší perioda, tím větší priorita
**Příklad dynamický** 
- LLF - Least laxity - Upřeďnostňují se dávky s nejnižší laxitou
- EDF - Closest deadline - Upřednostňuju dávky s nejbližší deadlinem

