Spolehlivá síť je v počítízačovýh sítích definována 4 body:

- Tolerance chyb - Redundance: Vícenásobné záložní cesty
	- Více switchů, kabelů routerů atd.
- Škálovatelnost - Možnost rozšiřovat sítě bez vlivu na výkon a rychlost
	- Počítat s maximální zátěží
- QoS (prioratizace) - Některým službám musíme dát prioritu (např. VoIP)
	- Vpodstatě řeší VLAN
- Bezpečnost - Dodržet [[02 - CIA triáda, McCumber cube#CIA triáda|CIA triádu]] na úrovni sítí.
	- Viz. [[Vítejte|KYB]]

Avšak vrámci této otázky se budeme bavit hlavně o bodu redundance. *A to hlavně proto že to je tak nějak halvním tématem té otázky.* 
### Co je to redundance?
Redundance je nabytek síťových prvků sloužící k zaručení provozu většiny zařízení, i přes selhání jednoho z prvků. Výhodami redundance jsou:
- Dostupnost - I při výpadku jednoho zdroje mohu data pokračovat jinou cestou.
- Odolnost - Neexistuje žádný single-point-of-failure
- Loadbalancing: Zátěž se rozdělí mezi všechny redundantní prvky
- Údržba - Zařízení jde z provozu vyjmout a to bez výpadku
Redundanci nám v sítích pomáhají tvořit hlavně tři protokoly: **STP, EtherChannel a FHRP**
---
### STP a problémy redundance na L2
Řekněme že chceme začít stavět redundantní síť. Problémy s její stavbou začínají již na 2. vrstvě. Jeden z nich je ten, jak switch zachází s broadcastem, tak nám dojde že pokud switche zapojíme do smyčky, tak se broadcast bude nekonečně šířit a množit sítí. Ukázka ve videu níže.

![[07 - Broadcast Storms.mp4]]

Mezi další problémy patří i to, že nebudou plně fungovat MAC tabulky a to díky tomu že počítač může používat různé switche pro komunikaci. Čímž se informace v MAC tabulce budou vždy zneplatňovat.

A v neposlední řadě L2 rámec nemá TTL, což zanamená, že se může stát, že rámec bude navždy putovat sítí.

Všechny tyto problémy řeší STP.
#### STP a jak funguje
STP dělá na první pohled velmi jednoduchu věc. Zablokuje všechny redundantní trasy v LAN, aby zničil všechny možné smyčky. Tyto trasy odblokuje při selhání některých z prvků, jakmile nebude hrozit, že vytvoří smyčku.
###### Spanning tree algorithm
Toho dosahuje za pomoci jednoduchého algoritmu. Switche mezi sebou komunikují díky BPDU rámcům. Ten po krocích dělá toto:
1. Volba Root Bridge - Switch s nejmenším BID se stane root bridgem. Pokud vznikne shoda, tak rozhoduje nejnižší MAC adresa
2. Root port - Na každé non-root switchi se vybere jeden port s nejnižší cenou k root bridge. Pokud je cena cesty stejná, tak se rozhoduje priorita bridge, pokud je i ta stejná (Více kabelů paralerně) tak rozhoduje priorita portu a pokud je i to stejné tak rozhoduje nejnižší číslo portu.
3. Designated port - Na každém segmentu sítě je jeden designated port. Na root bridge jsou všechny porty designated.
4. Blocked/Non-designated/Alternate ports - Zbylé porty se zablokují, přijímají pouze BPDU

![[07 - STP.png]]

###### Výpočet ceny
Cena portu se určuje podle rychlosti (lépe řečeno šířky pásma), kterou komnikuje. Rychlosti a ceny jsou uvedeny níže v tabulce:

| Rychlost | STP Cena | RSTP Cena | Poznámka       |
| -------- | -------- | --------- | -------------- |
| 10 Mbps  | 100      | 2 000 000 | Starý ethernet |
| 100 Mbps | 19       | 200 000   | Fast ethernet  |
| 1 Gbps   | 4        | 20 000    | GigE - běžné   |
| 10 Gbps  | 2        | 2 000     | 10 GigE        |
###### Stavy portů
Při zapnutí STP nebo při připojení zařízení porty procházejí těmito fázemi:
1. Blocking (RSTP Discarding) - 20s - Nepřeposílá data, přijímá BPDU, blokuje smyčky
2. Listening (RSTP Discarding) - 15s - Zpracovává BPDU, buduje topologii. Nepřeposílá
3. Learning - 15s - Plní MAC tabulku, stále nepřeposílá data
4. Forwarding - Přeposílá data (pokud je designated nebo root port)
*pokud se v 2. kroku jistí že je port redundantí, tak jde do stavu blocking*
###### Druhy STP
STP má několik druhů, ty jsou vypsány níže:

| Zkratka     | Standart | Popis                                                                                                        |
| ----------- | -------- | ------------------------------------------------------------------------------------------------------------ |
| STP         | 802.1D   | Původní - pomalá konvergence (asi 50s). Dnes nepoužíváno                                                     |
| RSTP        | 802.1w   | Rapid STP - rychlejší komvergence (asi 6s). Základ dneška                                                    |
| MSTP        | 802.1s   | Multiple STP - více stromů pro skupiny VLANů. Efektivní Loadbalancing. Menší hardwareová náročnost než PVST+ |
| PVST+       | Cisco    | Per-VLAN STP+ - Cisco proprietární. Každá VLAN má svoji vlastní instanci STP. Výchozí na cisco zařízeních    |
| Rapid PVST+ | Cisco    | Kombinace PVST a RSTP                                                                                        |
###### Konfigurace STP (Cisco)
Ve výchozím stavu je STP (PVST+) na ciscu zaplé. Vypneme ho pro určité VLANy `no spanning-tree <vlan>`
Priorita se nastavuje buď ručně (`spanning-tree vlan <vlan> priority <násobky 4096>`) nebo dle nastavení root bridge (`spanning-tree vlan <vlan> root primary`) a jeho zálohy ( `spanning-tree vlan <vlan> root secondary`).

###### PortFast & BPDUGuard
STP můžeme vypnout pro určité porty a ušetřit si čekání něž si port projde svími fázemi. Toto se hodí pro porty u kterých víme že na nich nejsou další síťové prvky. PC a tiskárny.
Zapneme ho příkazem `spanning-tree portfast`
Ale POZOR, na portu s portfastem switch neposlouchá BPDU a přeposílá ho dál. To lze zneužít při sofistikovaných útocích. Toto řeší BPDU guard, kdy jakmile port zaznamená BPDU rámec, tak vypne port. 
Zapneme ho příkazem `spanning-tree portfast bpduguard default`

---
### Etherchannel (agregace linek)
Etherchannel řeší to když propustnost jednoho kabelu nestačí. Umožňuje totiž spojit až 8 linek dohromady. Taky tvoří redundanci těchto linek (když jeden kabel nebo port nefunguje, tak máme dalších x kabelů) a přitom je všechny využívá (STP vidí pouze jednu linku).
Etherchannel můžeme buď nastavit staticky nebo dynamicky.

- Statické - Bez protokolu, Mód on. Nekontroluje stav sítě. Problémy při částečném selhání linky
- Dynamycké (PAgP (cisco) a LACP (802.3ad))  - S protokolem mezi sebou linky komunikují. Při problémech se linka odpojí.

Všechny linky v Etherchannelu musí mít stejnou rychlost, duplex a nastavení (vlan, mód portu)
Hodnoty u portů nabívají různých hodnot, níže je tabulka jaké hodnoty vytovří etherchannel

| LACP/PAgP        | active/desirable | passive/auto |
| ---------------- | ---------------- | ------------ |
| active/desirable | Vytvoří          | Vytvoří      |
| passive/auto     | Vytvoří          | Nevytvoří    |
###### Konfigurace Etherchannel (Cisco)
Nejdříve se pro interface nasaví channel-group. Ta se nastavuje příkazem `channel-group <N> mode <mód>`. Tento příklad platí pro oba protokoly. Jaký protokol se použije rozhoduje slovo použité pro mód (PAgP => `desirable/auto`, LACP => `active/passive`)
Potom tento channel-group používáme a nastavujeme jako běžný interface.

---
### FHRP (Firsthop redundancy protocol)
S STP a Etherchannelem máme hotovou redundanci na L2, ale stále nám zůstává jeden single-point-of-failure a to je výchozí brána. Právě její redundanci zajišťuje protokol FHRP.
Ten tento problém řeší, tak že dva či více routerů spojí do jednoho virtuálního, kterému přidělí virtuální IP a MAC adresu. Adresa tohoto virtuálního routeru se pak stane výchozí bránou.
Routery mezi sebou komunikují vrámci HELLO paketů, které ověřují zda jsou ostatní routery funkční (Posílá se v intervalu Hello času), pokud druhý nic nepošle je vnímán jako nefunkční a druhý přebere jeho práci (Čeká se hold vteřin).

![[07 - FHRP.png]]

Protokol FHRP má několik verzí, které se od sebe liší funkčností. Ty jsou popsány níže:

| Protocol                                  | Standart | Výchozí čas         | Klíčové vlastnosti                                                                                                                              |
| ----------------------------------------- | -------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| HSRP (Hot Standby Router Protocol)        | Cisco    | Hello: 3s Hold: 10s | Cisco propietární. Jeden router je Active a druhý standby. Od HSRPv2 podporuje IPv6. VMAC: 00-00-0C-07-AC-XX                                    |
| VRRP (Virtual Router Redundancy protocol) | RFC 5798 | Hello: 1s Hold: 3s  | Otevřený standart. Funguje na stejném principu jako HSRP. Role jsou Master/Backup. Master může mít svoji IP jako VIP<br>VMAC: 00-00-5E-00-01-XX |
| GLBP (Gateway Load Balancing Protocol)    | Cisco    | Hello: 3s Hold:10s  | Cisco proprietární. Jediný FHRP s loadbalancingem. Více routerů je aktivní a dělí si práci. Role jsou AVG (řídí provoz) a AVF (poslouchají AVG) |
###### Konfigurace FHRP
FHRP se na cisco routerch nastavuje v těchto krocích: (pro oba routery jsou potřeba provést)
- HSRP:
	1. `standby <cisloskupiny> ip <ip>` < nastavení IP adresy
	2. `standby <cisloskupiny> priority <priorita>` < nastavení priority (čím větší číslo, tím větší priorita)
	3. `standby <cisloskupiny> preempt` < obnovení původního stavu po obnovení slehného routeru
	4. `standby version 2` < nastavení verze (doporučná 2)
- VRRP
	1. `vrrp <cisloskupiny> ip <ip>` < nastavení IP adresy
	2. `vrrp <cisloskupiny> priority <priorita>` < nastavení priority (čím větší číslo, tím větší priorita)
	3. `vrrp <cisloskupiny> preempt` < obnovení původního stavu po obnovení slehného routeru
*nastavuje se na interface*
###### Stavy HSRP
Stejně jako STP i FHRP po zapnutí prochází stavy. Ty jsou:
 1. Initial - Výchozí stav po startu. HSRP negunguje.
 2. Learn - Čeká na hello zprávu s VIP
 3. Listen - Zná VIP, sleduje Hello zprávy, ale není Active ani Passive
 4. Speak - Odesílá hello, účastní se volby Active a Standby
 5. Rozdělení
	 - Active - Obsluhuje VIP, posílá hello každé 3 sekundy
	 - Standby - Sleduje hello, připraven převzít obsluhu VIP pokud vypadne Active