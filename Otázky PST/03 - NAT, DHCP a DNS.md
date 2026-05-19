### NAT
NAT (Network Address Translation) je systém při kterém (router nebo firewall) překládá více nebo jednu lokální IP adresu na více nebo jednu veřejnou IP adresu. 
###### Proč NAT vzniklo a proč ho používáme
- Nedostatek veřejných IPv4 adres - S NAT miliony zařízení mohou sdílet jednu veřejnou IP
- Bezpečnost - Lokání adresy nejsou z internetu přístupné (lze jim jenom "odpovídat")
- Flexibilita - Lze změnit IP adresy zařízení bez změny veřejné IP
- Cena - Jedna IP je levnější než blok IP adres
- Přechod na IPv6 - NAT64 umožňuje přechod přes sítě nepodporující IPv6
###### Druhy NAT
NAT dělíme na několik druhů:

| Typ                     | Překlad   | Popis a užití                                                                                                                                        |
| ----------------------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Static NAT              | 1:1       | Jedna privátní IP => jedna veřejná IP (pevně dané). Používá se pro servery, které musí být dostupné z internetu. *cekem useless*                     |
| Dynamic NAT             | n:n       | Několik veřejných adres je přižatováno hostům. Jakmile jsou všechny adresy užívané, tak se spojení odmítne.                                          |
| PAT/ NAT overload/ NAPT | n:1       | Všechny počítače používají jednu IP adresu. Spojení se odlišují dle užívaných portů. Pokud je port užívaný, tak se použije port + 1. Nejvíce užívaný |
| NAT64                   | IPv6:IPv4 | Překlad IPv6 na IPv4 pro průchod nepodporovanou sítí                                                                                                 |
###### Port Forwarding (statický NAT pro port)
Pokud chceme zpřístupnit jeden port nějakého počítače v síti (např. web server (port 80)), ale je v NAT (PAT), tak ho můžeme zpřístupnit pomocí Port Forwardingu. Port Forwarding vytvoří pravidlo pro jeden port a pokud na něj příjde požadavek, tak ho přepošle na IP v pravidle

Např. Posílej port 80 na 192.168.2.1
1. Příjde požadavek *veřejnáIPsítě*:80
2. Router přepošle požadavek 192.168.2.1
3. 192.168.2.1 požadavek zpracuje a pošle odpověď zpět

---
### DHCP
DHCP (Dynamic Host Configuration Protocol) je protokol, který má za úkol automaticky přidělovat IP adresy a síťové nastavení (Výchozí brána, DNS server, Domain name). V IPv6 může být nahrazován SLACCkem. Komunikuje přes port 67 (server) a 68 (klient).
###### Komunikace mezi DHCP serverem a klientem
Probíhá ve čtyřech krocích:
1. Discover - Posílá klient ([[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|broadcast]]): Kdo je DHCP server.
2. Offer - Posílá server ([[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|broadcast]]/[[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|broadcast]]): Já jsem server a nabízím tuhle adresu
3. Request - Posílá klient ([[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|broadcast]]): Chci tvoji adersu
4. ACK - Posílá Server ([[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|unicast]]/[[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|broadcast]]): OK, IP je tvoje

![[03 - DHCP.png]]

###### Další možné zprávy
- RENEW - Vypršel leasetime, chci obnovit
- REBIND - Po vypnutí chci starou adresu
###### Obsah zprávy DHCP

| Pole           | Příklad                           |
| -------------- | --------------------------------- |
| IP adresa      | 192.168.2.1                       |
| Maska          | 255.255.255.0                     |
| DNS server     | 8.8.8.8                           |
| Leasetime      | 86400 sekund *(24 hodin)*         |
| DHCP server ID | 192.168.2.100 *(IP DHCP serveru)* |
| Domain name    | pslib.cz                          |
###### DHCP relay
Protože DHCP spoléhá na funkcionalitu [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|broadcastů]], tak nastává probém pokud využíváme jeden DHCP server pro více sítí, protože se [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|broadcast]] nedostane přes router. Proto slouží DHCP relay agent, který, pokud je na routeru zaplí, zaznamená DHCP komunikaci od klienta, tak jej přepošle na stanovený DHCP server [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#Typy adresace|unicastem]].

![[03 - DHCP relay.png]]

---

### DNS
DNS (Domain Name System) je protokol který, překládá doménová jména na IP adresy (a naopak). Používá UDP i TCP s portem 53.

Pro pochopené DNS je nejdříve potřeba pochopit z čeho s domény skládají. Vezměme si například `www.pslib.cz`. Takováhle doména se skládá ze čtyř částí:

| www      | .pslib                    | .cz                    | .                       |
| -------- | ------------------------- | ---------------------- | ----------------------- |
| hostname | SLD (Second Level Domain) | TLD (Top Level Domain) | Root (často neviditený) |
Takhle plně vypsané doméně říkáme FQDN (Fully Qualified Domain Name). 
###### Hierarchie DNS
Jak jde vidět z jednotlivých částí, tak jde vidět, že části domény mají jasnou hierarchii (top > second) a je tomu tak. Nejvíše jsou TLD pod nima SLD a nejníž jsou hostname.

| com (komerční) | cz (česko) | org (organizace) |
| -------------- | ---------- | ---------------- |
| google         | pslib      | wikipedia        |
| www            | sharepoint | cs               |
###### Typy DNS serverů
Pro obsluhování klientů slouží několik typů DNS serverů a každý má trochu jinou roli. Ty typy jsou:

| Typ                  | Popis                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------------- |
| Root server          | 13 skupin serverů (root-servers.net A-M). Odpovídají na požadavky adresou na TLD server           |
| TLD server           | Spravuje TLD domény (.com, .cz atd.). Zná autoritativní servery pro svoje SLD domény              |
| Autoritativní server | Má přímou a přesnou odpověď pro svoji SLD doménu. Je za svoji doménu odpovědný.                   |
| Rekurzivní resolver  | Obsluhuje celý proces DNS, takže to klient nemusí dělat.                                          |
| Cache server         | Ukládá odpovědi po dobu TTL (time-to-live). Nemusí se opakovaně ptát. Urychluje opakované dotazy. |
###### Průběh DNS
Teď když víme všechny pojmy můžeme vysvětlit jak funguje DNS.
1. Klient se zeptá resolveru na IP domény
2. Resolver se podívá do cache jestli adresu nemá, pokud ano tak vrací
3. pokud ne tak se resolver obrátí na root server pro IP TLD
4. po odpovědi se resolver obrátí na TLD pro autoritativní server SLD
5. po odpovědi se resolver zeptá autoritativního serveru SLD na IP adresu
6. autoritativní server pošle IP adresu
7. resolver si ji uloží a pošle klientovi

![[03 - DNS.png]]
*čísla nesedí*
