Referneční model OSI/ISO (International Organization for Standardization/Open Systems Interconnection) se snaží o standardizaci počítačových sítí. Při ní rozdělila proces posílání dat přes síť do sedmi vrstev. Existuje taky model TCP/IP, který se snaží o podobný výsledek s 4 vrstvami, porovnání níže:

| OSI/ISO (ISO)                 | TCP/IP (DoD)      |
| ----------------------------- | ----------------- |
| L1 Fyzická (média/modulace)   | Data access layer |
| L2 Spojová (MAC)              | Data access layer |
| L3 Síťová (IP)                | Internet          |
| L4 Transportní (TCP/UDP)      | Transport         |
| L5 Relační                    | Application       |
| L6 Prezenční                  | Application       |
| L7 Aplikační (HTTPS,SSH .atd) | Application       |
Než, ale začneme vysvětlovat jak model funguje, tak si musíme objasnit pojmy, které model užívá. Ty jsou:
- **Protokol** - Soubor pravidel pro komunikaci mezi zařízeními ve dané vrsvě (TCP, HTTP, ARP atd.)
- **Rozhraní** (interface) - Komunikace mezi jednotlivími vrstvami (např. [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Standart IEEE 802.2|LLC]] mezi L2 a L3)
- **PDU (Protocol Data Unit)** - Datová jednotka dané vrstvy. Ty jsou:
	- L7-L5 - Data
	- L4 - Segment/Datagram
	- L3 - Paket
	- L2 - Rámec
	- L1 - Bity

### Zapouzdření
Model funguje tak že data při cestě na síť musí projí všemi vrstvami modelu. Každý protokol k datům během této cesty si přidá svoje informace, nejčastěji ve formě hlavičky. (Občas přidají informace i v rámci patičky.) Tomuto přidávání dat se říká zapouzdřování. Při přijetí se z zapuzdřených dat získávají data odpouzdřováním. Pro představu jak to vypada v praxi níže

| \|   ^  |                  |        |        |        | L7-5 hdr | Data |     | *Data*    |
| ------- | ---------------- | ------ | ------ | ------ | -------- | ---- | --- | --------- |
| \|   \| | *zapouzdřování/* |        |        | L4 hdr | L7-5     | Data |     | *Segment* |
| \|   \| | *odpouzdřování*  |        | L3 hdr | L4     | L7-5     | Data |     | *Paket*   |
| \|   \| |                  | L2 hdr | L3     | L4     | L7-5     | Data | FCS | *Rámec*   |
| v   \|  | Preambule        | L2 hdr | L3     | L4     | L7-5     | Data | FCS | *Bity*    |
*Co se týče L1 a L2 je v tom trochu bordel, protože některé zdroje říkají že vlastně L1 i L2 je součástí ethernetového rámce a některé že ne. Viz [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Rámce]].*
### Funkce vrstev
Každá vrstva má svoji specifickou úlohu kterou má plnit. Tyto funkce jsou rozepsané níže:

- L1 - Fyzický přenos bitů: Modulace, média atd. Nic neinterpretuje jen tvoří a přenáší signál
- L2 - MAC adresování, tvorba rámců, detekce chyb. Přenos po jednom fyzickém spoji (hop-to-hop. Dělí se na dvě podvrstvy. [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#MAC Vrstva|MAC]] a [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Standart IEEE 802.2|LLC]]
- L3 - Logické adresování IP a směrování packetů přes sítě.
- L4 - End-to-End komunikace. Segmentace/Reassembly dat, řízení toku
- L5 - Správa relací - zahájení, udržení a ukončení spojení. Synchronizace a obnovení přerušeného provozu
- L6 - Formátování a kódování dat - šifrování, komprese, konverze znakových sad.
- L7 - Rozhraní mezi aplikací a sítí. Poskytuje specifické síťové služby uživatelským aplikacím.

### Síťové prvky a jeji funkcionalia na vrstvách
Každý prvek zpracovává data na své OSI vrstvě.
###### Opakovač (L1)
Pracuje na první vrstvě. Vezme signál z média, zesílí ho a pošle ho dál.
###### Rozbočovač (hub) (L1)
Vezme signál ze jakéhokoliv portu a pošle jej na všechny ostatní porty. Zastaralý, dnes nahrazován přepínačem switchem.
###### Most (bridge) (L2)
Spojí dva LAN segmenty. Učí se MAC adresy, pokud nezná pošle všude (mimo příchozí port). Odděluje kolizní domény. *V podstatě switch s menším počtem portů.* Dnes se nepoužívá.
###### Přepínač (switch) (L2)
Přepíná rámce na základě MAC adresy *(taková křižovatka)*. Každý port = svoje kolizní doména. Broadcast doména je buď celý switch nebo VLAN. Učí se MAC adresy, pokud nezná pošle všude (mimo příchozí port). Adresy ukládá do CAM tabulky. Existují různé typy switchů:
- **Store and Forward** - Prohlédne celý rámec. Zkontroluje CRC 
- **Cut-Through** - Dívá se pouze na MAC adresu a posílá dál (nižší latence)
- **Fragment-free** - Čte prvních 64 bajtů zda-li nejde o [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Rámce|runt]]
###### Směrovač (router) (L3)
Směruje pakety mezi různými sítěmi na základě IP adresy a směrovací tabulky. Odděluje broadcast domény, Každý interface = jiná síť. Do směrovací tabulky se zaznamenávají informace buď staticky nebo dynamicky za pomoci routovacích protokolů (OSPF, RIP, BGP).
Při průchodu routerem se změní L2 rámec pro průchod jinou sítí (změní se MAC adresa).
###### L3 Switch
Kombinace switche a routeru v jednom zařízení. Přepíná jako switch, ale umí i směrovat bez pomoci routeru. Vhodné pro [[06 - Inter-VLAN Routing|Inter-VLAN routing]], nebo páteřní přepínač v LAN.
###### Firewall (L3 - L7)
Filtruje síťový provoz dle stanovených pravidel (nejčasteji ACL). Řadíme je podle toho na jakých vrstvách pracují.
- L3 - Filtr podle IP adresy
- L4 - Filtr podle portu
- L5-L7 - NGFW, třízení podle aplikací a služeb
- Stateful - Sleduje stav připojení TCP
###### L4-L7 Switch
Přepínání na základě portů a služeb. Slouží jako load-balancing, aplikační firewall, content switching. Používá se v datových centrech

### Nástroje pro sledování a diagnostiku síťového provozu
Při problémech, při průchodu jednotlivími vrstvami nám k řešení pomáhají diagnostické nástroje. Ty jsou:
###### Wireshark (L1-L7)
Zachycení síťového provozu a jeho analýza v reálném čase. Zobrazí celý obsah paketu.
###### tcpdump (L2-L7)
CLI zachtávač provozu pro Linux/Unix. Ukládá do `.pcap` (lze otevřít v wiresharku).
###### ping (L3)
Test dosažitelnosti hosta. Zobrazí RTT (Round Trip Type) a ztrátovost packetů. V případě nedostupnost různé kódy:
- 0 - net unreachable
- 1 - host unreachable
- 2 - protocol unreachable
- 3 - port unreachable
###### tracert/traceroute (L3)
Zobrazí cesu hop-by-hop. Funguje tak že postupně zvyšuje TTL. Víme kudy packet jde a kde se ztratí.
###### nslookup (L7 [[03 - NAT, DHCP a DNS#DNS|DNS]])
příkaz pro DNS dotaz. Přlož doménu na IP. Možnost získat další informace o záznamu (autoritativní server, TTL atd.)
###### ipconfig / ip addr (L3)
Zobrazí síťové nasavení koncového zařízení. `ipconfig /all` je pro Windows a `ip addr show` pro Linux
###### netstat / ss (L4)
Zobrazí aktivní TCP/UDP spojení, otevřené porty a statistiky. Windows používá `netstat -an` , Linux používá `ss -tulnp`
###### arp -a (L2/L3)
Zobrazí arp tabulku
###### Cisco show
Diagnostika na cisco zařízeních. např `show run`

---
### Troubleshooting po vrstvách
1. L1 - Je zapojený kabel
2. L2 - Je zařízení v MAC tabulce? Správná [[05 - VLAN a Návrh podnikové sítě|VLAN]]?
3. L3 - Je přístupný router? Mám správnou IP?
4. L4 - Je port otevřený?
5. L7 - Funguje mi [[03 - NAT, DHCP a DNS#DNS|DNS]]?