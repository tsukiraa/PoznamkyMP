*Z nějakého záhadného důvodu chce prokeš u adresace popsat 2. vrstvu, proč nevím. Asi to vidí tak že adresa je adresa.*
### Adresace na nižších vrstvách
*Nejspíš*, pro pochopení adresace IPv4 je potřeba pochopit adresaci na L2 a L1. Proto to tady rozepíšeme:
#### *"Vrstva L1"* (Pojmenování jednotlivých portů rozhraní (Cisco))
Na switchích a routrech povětšinou máme více rozhraní do, kterých můžeme zapojovat naše média, proto aby se nám nepletli, tak jsou systematicky pojmenovaný.
*(U Cisca jsou)* Porty jsou pojmenovány typem (fastEthernet, gigabitEthernet), číslem slotu a číslem portu (rozhraní). Tyto porty se dají vypsat v různých formátech.

| Formát                | Příklad             |
| --------------------- | ------------------- |
| Typ číslo             | FastEthernet0       |
| Typ slot/port         | GigabitEthernet 0/0 |
| Typ slot/subslot/port | FastEthernet 0/0/0  |
| Zktatky               | Fa0/0 Gi0/0/0       |
#### Vrstva L2 (MAC adresa)
Viz první otázka → [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#MAC Vrstva|01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)]]
### IPv4 adresace
IPv4 adresa je 32 bitové číslo napsané v desítkové soustavě a s tečkovou notací (rozděleno do 4 oktetů). Skládá se z části síťové a části pro hosta. Toto rozdělení určuje maska sítě. IPv4 adresy řadíme do velkých bloků adres po kterých se rozdávali (při začátku užívání internetu), těmto blokům říkáme třídy.

| Třída | Rozsah 1. oktetu | Výchozí maska | Sítí      | Host na síť | Komu se dávali/Využití       |
| ----- | ---------------- | ------------- | --------- | ----------- | ---------------------------- |
| A     | 1 - 126          | /8            | 128       | 16 mil      | Velké organizace             |
| B     | 128 -191         | /16           | 16 384    | 65 534      | Střední podniky, instituce   |
| C     | 192 - 223        | /24           | 2 097 152 | 254         | Malé sítě, domácnosti        |
| D     | 224 - 239        | - // -        | -         | -           | Multicast                    |
| E     | 240 - 255        | - // -        | -         | -           | Experimentální a nevyužívané |
Adresy také dělíme na adresy veřejné a soukromé. Adresy soukromé slouží pouze pro komunikaci v LAN. Pokud chce počítač komunikovat se adresou soukromou mimo lokální síť, tak si musí svoji adresu přeložit (pomocí [[03 - NAT, DHCP a DNS#NAT|NAT]]) na adresu veřejnou. Pro soukromé adresy platí tyto rozsahy.

- **10.0.0.0/8 (Třída A)** - Počet adres v síťi: 16 777 216 
- **192.168.0.0/16 (Třída B)** - Počet adres v síťi: 65 536
- **172.16.0.0/12** - Počet adresv síti: 1 048 576

Existují potom ještě speciální adresy sloužící pro specifické účely:

- **0.0.0.0/0** - Nespecifikovaná adresa: Do této sítě patří všechny existující adresy. Používá se při směrování (výchozí brána) nebo při nastavování ACL pravidel
- **172.0.0.0/8** - Loopback adresy - Odkazují samy na sebe. Slouží pro testování nebo pro komunikaci počítače samo se sebou
- **169.254.0.0/16** - APIPA: Adresa přiřazená na rozhraní při selhání DHCP konfigurace. Komunikace možná jen v LAN.
- **x.x.x.0** - Adresa sítě: Nelze přiřadit pro počítač. Slouží k označení sítě.
- **x.x.x.255** *(poslední adresa v síti)* - Směrovatelný broadcast sítě: Osloví všechny hosty této sítě. Lze zaslat i z sítě jiné. V dnešní době zahazován routery z důvodu ochrany před Smurf útoky.
- **255.255.255.255** - Limited Broadcast: Osloví všechny počítače ve stejné síti jako je počítač odesílatele. Používá se když je počítač bez adresy.
- **224.0.0.0/4** - Rozsah sloužící pro IPv4 multicasty.

*Lze používat i anycast. Ten funguje, tak že více počítačů má 1 unicasovou adresu*
### Protokoly L3 *(a ARP z nějakého důvodu)*
Na třetí vrsvě nám zajišťuje komunikaci protokol IP (Internet Protocol). Slouží k směrování paketů mezi jednotlivými sítěmi. K tomu používá IP adresu, kterou jsme probrali výše. Na obrázku níže je vyzobrazena jeho hlavička:

![[10 - Hlavička IP.png]]

Nejdůležitější součásmi jsou:

- **Verze** - Jestli se jedná o IPv4 nebo IPv6
- **TTL (Time-To-Live** - Číslo, které značí kolikrát paket může být směrován. Každý router toto číslo zmeší o 1. Pokud je TTL 0, tak paket router zahodí.
- **Protokol**: Informace pro 4. vrstvu. Značí zda-li se jedná o TCP, UDP nebo o IMCP.
- **Checksum**: Kontrolní součet
- **Zdrojová a cílová IP adresa**: Identifikace zdrojového a cílového počítače.

#### IMCP
IMCP je také protokolem třetí vrstvy, jeho cílem je ověření dostupnosti cíle. Slouží pro diagnostiku. Více zde: [[04 - Referenční model OSI-ISO a síťové prvky#ping (IMCP L3)]]

#### ARP
*Z nějakého důvodu je tady v této otázce ARP.* Ten má zaúkol přeložit IP adresu na MAC adresu. (L3 → L2). Více zde: [[01 - Vrstva síťového přístupu, Ethernet (1 a 2 vrstva)#ARP]]

### Cesta datagramu
Jak tedy informace přes třetí vrstvu putují? Pojďme si to představit na příkladě. Máme dva počítače v jiných sítí, rozdělené dvěma routry (viz obrázek). Komunikace bude probíhat takto:

Počítač má na začátku IP adresu cíle. Protože ví že daná adresa se nenachází v jeho síti, tak odešle paket s IP adresou cíle na MAC adresu routeru (výchozí brány). Daný router se podívá na IP adresu a nasměruje ho (pošle ho) na MAC adresu routeru co má přístup k síti s cílem. Tam si router přečte IP adresu a paket odešle na MAC adresu cílového počítače.

Můžeme si všimnout, že se během komunikace několikrát změní MAC adresa. Ta se mění při každém "hopu" do další sítě (po směrování). Pokud mezi jednotlivími počítači není [[03 - NAT, DHCP a DNS#NAT|NAT]], tak se IP adresa za celou komunikaci nemění.

![[10 - Schéma sítě.png]]

### ACL - Access Control List
ACL je uspořádaný list pravidelm, který se používá nejčastěji používá (Lze používat i pro konfiguraci NAT na Cisco routerech) pro filtraci provozu ([[04 - Referenční model OSI-ISO a síťové prvky#Firewall (L3 - L7)|Firewall]]). Pravidla se vyhodnocují postupně ze shora dolů. První schoda má efekt. Na konci ACL bývá `deny any` (zablokuje vše).

ACL má tři typy. Základní, rozšířený a jmenný.

- **Standartní (Čísla 1 - 99 a 1300 - 1999)** - Velmi jednoduché filtrace pomocí zdrojové IP adresy.
- **Rozšířená (Čísla 100 - 199 a 2000 - 2699)** - Rozšířený filtr. Filtrace nejenom dle zdrojových i cílových IP adres, ale i za pomoci protokolu (TCP/UDP/IMCP) nebo portu (L4)
- **Jmený** - Standartní nebo rozšířený, pojmenovaný stringem

Také pokud chceme v ACL označit síť, tak můžeme narazit na to, že se maska zapisuje v tvz. Wildcard formátu. Ten zapíšeme tak že každý bit masky invertujeme. Například z masky  `255.255.255.0` se stane `0.0.0.255`

ACL jde taky nastavit IN (před zařízením) nebo OUT (za zařízením). Pokud filtrace probíhá před zařízením (IN), tak například u routeru se filtrace provede před tím než paket bude směrován. Pokud je ACL za zařízením (OUT), tak filterace proběhne po. Čstěji se používá ACL před zařízením, protože šetří zdroje.
###### Ukázka pravidel ACL
`permit <ip adresa sítě> <maska>`  - povolení sítě
`permit <ip adresa počítače> 0.0.0.0`  - povolení jednoho hosta
`permit tcp` - přijmi tcp
`deny any` - vše zamítni

### Dodatek k otázce:  Zabezpečení přístupu k managování switche a routeru
Pro vytvoření bezpečného managment přístupu (SSH) potřebujeme udělat tyto kroky:
- `ip domain-name moje-firma.local` – Nastavení domény pro šifrovací klíče.
- `crypto key generate rsa general-keys modulus 2048` – Vygenerování silných RSA šifrovacích klíčů.
- `ip ssh version 2` – Vynucení bezpečnější verze protokolu SSH.
- `service password-encryption` – Skrytí všech hesel v konfiguraci.
- `enable secret MojeHeslo123!` – Zašifrované heslo pro privilegovaný režim.
- `access-list 99 deny any log` – Zákaz všeho ostatního se záznamem.
- `line vty 0 4` – Vstup do nastavení vzdálených linek.
- `login local` – Ověřování pomocí lokální uživatelské databáze.
- `transport input ssh` – Povolení výhradně šifrovaného SSH připojení.