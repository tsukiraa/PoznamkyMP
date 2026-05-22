IPv6 je novější verze IP. IPv6 adresa je 128 bitové číslo, napsáno 8 hextety. Vzniklo, kvůli tomu že došli veřejné IPv4 adresy. Rozdíl mezi IPv4 a IPv6 jsou rozepsné níže. Adresa je rozdělena na prefix sítě a ID hosta. Ty jsou rozděleny délkou prefixu (ekvivalent IPv4 masky). 

| Vlastnosti   | IPv4                                    | IPv6                                  |
| ------------ | --------------------------------------- | ------------------------------------- |
| Délka adresy | 32 bitů                                 | 128 bitů                              |
| Počet adres  | ~ 4,3 miliardy                          | ~ 3,4 x 10^38                         |
| Notace       | 192.168.1.1 (tečková notace)            | 2001:bff5:... (Hexadecimální skupiny) |
| Hlavička     | 20B minimum, proměná délka, cheksum     | 40B fixní, bez checksum, bez NAT      |
| Konfigurace  | DHCP nebo ručně                         | SLAAC, DHCPv6, ručně                  |
| NAT          | Nutný (Nedostatek veřejných IPv4 adres) | Nepotřebý                             |
| IP security  | Volitelný                               | Doporučený (dříve býval povinný)      |
| Fragmentace  | Router i odesílatel                     | Pouze odesílatel                      |
Protože jsou IPv6 adresy příliž dlouhé, tak je zkracujeme a to tak že umazáváme nuly. Pro to slouží dvě pravidla. Vynechej první nuly hextetu a vymaž 1 skupinu nulových hextetů. Například řekněme že máme IPv6 adresu: `2001:0db8:0000:0001:0000:0000:0000:0001`, tak její zkrácená verze bude `2001:db8:0:1::1`
Jak můžete vidět, smazat nulové skupiny hextetů můžeme jen jednou.
###### Pravidlo 3,1,4
Je úzus přidělování těchto adres síti platí pravidlo 3,1,4 a to že první tři hextety se používají pro identifikaci sítě, jeden uprostřed pro rozdělení na podsítě a poslední čtyři pro identifikaci hosta.

| 2001:0db8:acad:             | 0001:             | :2022::2            | /64           |
| --------------------------- | ----------------- | ------------------- | ------------- |
| Global routing prefix (síť) | Subnet ID (posíť) | Interface ID (host) | délka prefixu |
###### Druhy vysílání
Jako v IPv4 i v IPv6 můžeme vysílat unicast, multicast a anycast. IPv6 zrušilo možnost broadcastu. Jeho funkci zastávají multicast adresy.

- **Unicast** - Běžné užívání adres
- **Multicast** - Multicastové adresy se nacházejí v prefixu ff00::/8. Přijímají je jen členové skupiny. V IPv6 je broadcast nahrazen multicastem. Je důležité znát tyto multicast adresy:
	- `ff02::1` - All Nodes: Ekvivalent staršího broadcastu. Osloví všechna zařízení
	- `ff02::2` - All Routers: Osloví všechny routery. Slouží pro SLAAC
	- `ff02::1:ff00:0000/104` - Multicast sloužící pro sjednocení počítačů se 3 stejnými posledními bajty IPv6.
	- `ff02::1:2` - All DHCP agents: Osloví všechny DHCP servery
- **Anycast** - Stejné jako u IPv6. Jedna adresa je užívaná více počítači
###### Typy adres
I u IPv6 máme typy adres. Každý typ slouží k něčemu jinému.
- **GUA** (2000::/3) - Global Unicast Address: Veřejné adresy. Přiřazuje IANA nebo RIR. J
- **LLA** (fe80::/10) - Link-Local: Ekvivalent APIPA adresy. Počítač si ji sám generuje po nahození rozhraní (plně náhodná). Není routovatelná, slouží pouze pro komunikaci vrámci LAN. Pokud zařízení nemá dostatečný výkon pro plně náhodné generování adresy, tak využívá EUI-64
- **ULA** (fc00::/7)- Unique Local Address: Privátní adresy. Nelze používat v internetu. Jejich užívání není doporučeno.
###### Speciální IPv6 adresy
Protoko IPv6 má také vyhrazené některé speciální adresy:
- `::1/128` - Loopback: Adresa sama na sebe. Slouží k testování.
- `::/128` - Nespecifikovaná adresa *(neplést s výchozí cestou)*: Je užívaná když rozhraní nemá žádnou adresu (ani LLC) *LLC se nesmí používat dokuď není ověřená přes DAD*
- `::/0` - Adresa výchozí cesty: Do tohoto zápisu sítě spadají všechny existující adresy IPv6.
###### EUI-64
Ve většině případů (SLAAC, LLC), při nastavování IPv6 adresy si počítač generuje svoje interface ID. To se správně má generovat náhodně. Bohužel některé zařízení nemají dostatečné zdroje pro to, aby toto generování zvládli obstarat. Proto existuje EUI-64, které tvoří IPv6 interface ID z MAC adresy. Tento proces probíhá takto:

1. Vezmeme si naši MAC adresu. - `00:11:22:AA:BB:CC`
2. Doprostřed ní vložíme `FF:FE` - `00:11:22:FF:FE:AA:BB:CC`
3. Převedeme na hextety a převrátíme 7. bit `0(2)11:22FF:FEAA:BBCC`
4. Doplníme prefix sítě a je hotovo (U LLC fe80::) - `fe80::0211:22FF:FEAA:BBCC` 
---
### ICMPv6
Oproti ICMP v IPv4 ICMPv6 neslouží pouze o diagnostiku. Je to plnohodnotný řídící protokol na kterém stojí funkcionalita SLAAC, DAD a NDP. Pro to používá několik různých zpráv, které pak využívá. Vlastně se dá s nadsázkou říct že ICMPv6 nahrazuje ARP a DHCP

Tyto IMCP zprávy a jak se s nimi zachází definuje NDP.

| Typ | Zkratka  | Cíl (multicastivá adresa)          | Popis a funkce                                                                                                                                                     |
| --- | -------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 133 | RS       | `ff02::2` - All Routers            | **Router solicitaion** - První krok SLAAC. Ptáme se: "Je tu někde router. Pošli mi síť, prefix a bránu"                                                            |
| 134 | RA       | `ff02::1` - All Nodes nebo Unicast | **Router advertisment** - Odpověď na RS. Obsahuje síť, délku prefixu, bránu, MTU, bity M/O/A a dobu platnosti. Buď posílá samo (`ff02::1`) nebo odpovídá (Unicast) |
| 135 | NS       | `ff02::1:ffXX:XXXX`                | Neighbor solicitation - zjištění MAC z IP. Obsahuje celou IPv6 Cíle. Slouží pro DAD a funkonionalitu ARP                                                           |
| 136 | NA       | `ff02::1` - All Nodes nebo Unicast | **Neighbor advertisment** - Odpověď na NS. Obsahuje MAC adresu odesílatele. Běžně unicast, odpovídá na `ff02::1` při nespecifikované adrese                        |
| 137 | Redirect | Unicast                            | Router informuje hosta o lepší ceste (pošle adresu jiného routeru)                                                                                                 |
###### "ARP" a DAD
Jak již bylo psáno "ARP" i DAD pro svoji funkconialitu používají NS. Je nutné říct že ARP v IPv6 neexistuje, ale NDP ho nahrazuje takto.:

1. Pošle NS s adresou nodu jehož adresu chce zjistit.
2. Získá odpověď NA s MAC adresou

U DAD to tak jednoznačné není. To má za cíl ověřit zda jeho adresa není duplikát. Ten posílá NS se svojí adresou (tou co chce ověrit) a zdrojovou adresou `::` . Pokud mu přijde odpověď, adresu mění.

###### SLAAC
SLAAC nabízí bezstavouvou konfiguraci hosta. Nastavuje síť, délku prefixu a bránu. Pokud SLAAC nechceme používat, nebo potřebujeme DHCPv6 server pro dodání dalších informací (DNS server, doména atd.), tak se upravují řídící M/O/A *(Managed/Other/Antonomus)* bity. Ty přesněji značí:

**M** - Pokud M=1, tak se všechny informace získávají z DHCPv6 serveru. Počítač se ptá na vše. 
**O** - Pokud O=1, tak zařízení získává dodatečné informace z DHCPv6 serveru
**A** - Pokud A=1, tak zařízení pro svoji konfiguraci použije SLAAC

Kombinace těchto bitů značí:

| M   | O   | A   | Popis                                                                                 |
| --- | --- | --- | ------------------------------------------------------------------------------------- |
| 0   | 0   | 1   | SLAAC bez DHCPv6: Přejímá informace je z routeru                                      |
| 0   | 1   | 1   | SLAAC s DHCPv6 *stateless*: Bere základní informace z routeru, a doplňující z serveru |
| 1   | 1   | 0   | DHCPv6 *(statefull)*: DHCP přiděluje všechny adresy a ukládá všechny parametry.       |
###### Konfigurace DHCP a SLAAC

1. `ipv6 dhcp pool STATELESS_POOL` ← Záčtek konfigurace DHCPv6 serveru
	 `dns-server <ip>` ← Konfigurace DHCPv6 serveru
2. Na interface nastavíme:
	1. `ipv6 address <ip>/<prefix>` ← Nastavení IP adresy
	2. `ipv6 nd other-config-flag` ← Zapnutí bitu O (O = 1)
	3. `ipv6 dhcp server STATELESS_POOL` ← Přiřazení serveru na rozhraní