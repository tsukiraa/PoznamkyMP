Směrování je činnost kterou provádí router, L3 switch nebo PC při posílání paketu do své sítě. V sítích směrujeme buď staticky nebo dynamicky:

|                   | Statické                                       | Dynamické                                                  |
| ----------------- | ---------------------------------------------- | ---------------------------------------------------------- |
| Princip           | Trasy jsou ručně konfigurované administrátorem | Routery s vyměňují informace o sítích                      |
| Správa            | Vysoké - Každá změna = zásah člověka           | Nízká - routery se adaprují samy                           |
| Škálovatelnost    | Nízká *max. dvě sítě*                          | Vysoká - Pro sítě s deseti routery a více                  |
| Bezpečnost        | Vyšší - routing nelze ovivnit z sítě           | Nízká - routing lze ovlivnit z sítě *(např. RIP spoofing)* |
| Reakce na výpadek | Žádná - router směruje i při výpadku stejně    | Automatická - přepočítá trasy, najde záložní cestu         |
| Zátěž CPU/RAM     | Minimální - nic nepočítáme                     | Větší - udržba tabulek, SPF algoritums                     |
| Použití           | Stub sítě, default route                       | Podnikové sítě, ISP, WAN s více cestami                    |

Toto směrování provádí dle své směrovací tabuky. Tato směrovací tabulka obashuje tyto informace:
- **Síť a prefix/maska** - Kam cesta směřuje. Cílová síť. Pokud k adrese vyhovují více záznamů, tak potom použije shodu s nejdelším prefixem
- **Next-hop** - Next-hop adresa značí adresu dalšího routeru, nutnou pro přenos.
- **Exit-interface** - Ozančení inerface, přes který paket odešleme.
- **Administrativní vzdálenost (AD)** - Priorita a důvěryhodnost trasy. Při směřování se při schodě, použije adresa s nižším AD
- **Metrika** - Cena trasy dle routovacího protokulu. Upřednostňuje se trasa s nejmenší cenou.
- **Typ záznamu** - Jak se záznam přidal:
	- **C** - Přímo připojeno
	- **S** - Staticky (administrátorem) zavedeno
	- **L** - Lokální cesta (Sítě s maskou /32 nebo /128 (adresy routeru)). Používá se aby se paket zbytečně neposílal do sítě 
	- **R** - RIP protokol
	- **O** - OSPF protokol
	- **D** - EIGRP
	- **B** - BGP

Zápis pak může vypadat takto:

`C    10.0.0.0/30 is directly connected, GigabitEthernet` <= Přímo připojené síť *žádný next hop*

`O    192.168.3.0/24 [110/2] via 10.0.0.2, 00:05:22, GigabitEthernet0/0` <= Trasa přes OSPF
###### Priority cest
Pokud dojede ke schodě cest (paket se dá poslat více cestami), tak se rozhoduje tímto postupem.
1. Paket se pošle cestou, která má největší prefix. *Upřednostní se /24 než /8*
2. Pokud jsou prefixy steně dlouhé, tak se pošle cestou s nejmenší Administrativí vzdáleností (AD)
3. Pokud je i AD identické, tak se posílá trasou, která má co nejmenší metriku.
4. Pokud je všechno stejné, router posílá všema stejnýma cestama. (Loadbalancing)
###### Administrativní vzdálenost
Různé typy přidávání cest do směrovací tabulky mají přiřazené svoje výchozí Administrativní vzdálenosti. Ty jsou definovány v tabulce níže:

| Zdroj                            | Dodnota AD | Poznámka                                                        |
| -------------------------------- | ---------- | --------------------------------------------------------------- |
| Přímé připojení                  | 0          | Nejdůvěryhodnější - fyzicky přidáno                             |
| Statická trasa                   | 1          | Ruční konfigurace adminem                                       |
| EIGRP (souhrn)                   | 5          | Při sumarizaci, pro odflitrování nechtěného provozu             |
| BGP (eBGP)                       | 20         | Směrovací protokol mezi autonomními systémy (AS)                |
| EIGRP (interní)                  | 90         | Standartní cesty přidány přes EIGRP protokol                    |
| OSPF                             | 110        | Cesty přidány přes OSPF *nejvíce používaný*                     |
| RIP                              | 120        | Cesty přidány přes RIP *starší protokol, max 15  hopů*          |
| BGP (iBGP)                       | 200        | Vnitřní BGP (uvnitř AS)                                         |
| Nedosažitená/Nedůvěryhodná trasa | 255        | Router odmítá tuto trasu použít. Administrátorské vypnutí trasy |
Při nastavování statické route můžeme nastavt i její administrativní vzdálenost (vytvoření floating route):
`ip route <ip> <maska> <next-hop> <AD>`

---
### Protokoly směrování
Protokoly používají pro směrování tři systémy pro výměnu směrovacích informací. Ty jsou **Distance-vector (DV)**,**Link-state (LS)** a **Path-vector**.

**Distance-vector** funguje tak že si mezi sebou vyměňují jejich známé trasy. Tento princip se nazívá routing by rumor, potože si rutery plně věří že sdílená cesta je funkční. Tím že se v podstatě posílají celé směrovací tabulky, tak je konvergence *(naučení tras mezi všemi routery)* relativně pomalá. Také tato metoda trpý problémama typu směrovacích smyček. Tyto chyby se řeší nejčastěji přes techiky split horizont, rute poisoning atd.

**Link-state** funguje tak že si routery posílají jen jaký porty (s tím jakou mají propustnost) mají a k čemu jsou připojený. Těmto zprávám se říká LSA a pomocí LSA floodingu (Router přepošle přijaté LSA všem sousedům) se informace rozšíří přes celou zónu. Protože jsou data jednoduší tak je čas pro konvergenci nižší. Z těchto dat si pak LS pomocí algoritmu SPF sestaví mapu celé zóny podle které routuje.

**Path-vector** je systém který se využívá výhradně v protokolu BGP pro edge routery. Místo toho aby sdílel jak trasy, tak sdílí přes jaké autonomní systémy (AS) se musí paket dostat aby dorazil do cíle. Výměna informací probíhá podobně jako u DV.

###### Jednotlivé protokoly
Tyto systémy proužívají jednotlivé protokoly směrování. V praxi se používají halvně čtyři typy protokolů a to RIP, OSPF, EIGRP a BGP.

**RIP** - Nejstarší z uvedených protokolů. Jako metriku používá hop-count (aka kolik routerů si zprávu přepošle). Maximální hop-count je 15. Využvá DV
Verze:
	- RIPv1: Classful, broadcast aktualizace 30 sekund
	- RIPv2: Classless, používá multicast 224.0.0.9, MD5 (Ověřování drbů)
	- RIPng: RIPv2 pro IPv6. Multicast ff02::9

**OSPF** - V dnešní době nejpoužívanější. Jako metriku používá cenu komunikace (propustnost). Kvůli LS, které používá, je náročější na hardware. Umožňuje rozdělení velkých sítí do areích (zón). Používá multicast 224.0.0.5 a 224.0.0.6. IPv6 je podporováno od verze OSPFv3.
Pro přehled ceny OSPF:

| Typ rozhraní (BW)        | Cena |
| ------------------------ | ---- |
| Serial (1,544 Mbps)      | 64   |
| FastEthernet (100 Mbps)  | 1    |
| GigabitEthernet (1 Gbps) | 1    |
| 10 GigE (10 Gbps)        | 1    |
*Od 100 Mbps je cena stejná. Proto se doporučuje zvýšit reference-bandwidth a to pomocí příkazu `auto-cost reference-bandwidth 10000`*

**EIGRP** - Je cisco protokol, je zajímavý tím že kombinuje systém LS i DV ve svém DUAL algoritmu. Komunikuje pomocí multicastu 224.0.0.10.

**BGP** - Jediný protokol využívající Vector-path. Slouží hlavně na edge routrech.

---
### Konfigurace tabulek
###### Statické směrování
Pro každou route jeden příkaz. Příkaz pro přidání statické route je:
`ip route <síť kam směrujeme> <maska sitě> <next-hop adresa> <AD>`
*Místo next hop adresy lze také použít exit-interface*
###### RIPv2 konfigurace
RIP se na routeru nastavuje ve více krocích:
1. `router rip` <= začátek konfigurace RIP
2. `version 2` <= přepnutí do RIPv2
3. `network <ip>` <= zadání sítí co propagujeme a které propagovat chceme.
4. `passive-interface <interface>` <= odstraníme porty které nechceme v RIPu
5. `auto-summary` <= automatická sumarizace *(může působit problémy)*
###### OSPF konfigurace
OSPF se konfiguruje podobně jako RIP, jeho kroky jsou:
1. `router ospf <num>` <= začátek konfigurace OSPF
2. `router-id 1.1.1.1` <= ID routeru. **Není IP**
3. `network <ip> <wildcard mask> area <area>` <= přiřazení porů s IP adresou do arei
4. `passive-interface <interface>` <= odstraníme porty které nechceme v OSPF