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
	- `ff::1:ff00:0000/104` - Adresy sloužící pro funkčnost NDP (ARP pro IPv6).
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
- `::/128` - Nespecifikovaná adresa *(neplést s výchozí cestou)*: Je užívaná když rozhraní nemá žádnou adresu (ani LLC)
- `::/0` - Adresa výchozí cesty: Do tohoto zápisu sítě spadají všechny existující adresy IPv6.