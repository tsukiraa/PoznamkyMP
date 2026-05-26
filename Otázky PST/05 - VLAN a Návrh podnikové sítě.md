VLAN (Virtual LAN) je logické rozdělení switche na samostané sítě bez ohledu na fyzické zapojení. Protože dvě VLANy jsou dvě rozdělené sítě tak musí mezi sebou komunikovat buď přes router nebo přes L3 switch.
Pro VLANy máme jisté rozsahy o kterých by jsme měli vědět. Ty rozsahy jsou:

| VLAN ID      | Název     | Popis                                                    |
| ------------ | --------- | -------------------------------------------------------- |
| 1            | Default   | Výchozí - *na začátku u všech portů*                     |
| 2 - 1001     | Normální  | Běžné nasazení                                           |
| 1002  - 1005 | Vyhrazené | FDDI, Token Ring                                         |
| 1006 - 4094  | Rozšířené | Jen v VTP transparent módu *(mimo dynamické trunkování)* |
Jistě se ptáte: *"Proč vlastně chceme rozdělit switch na několik sítí?"* Důvodem je to, že VLAN přináší spoustu výhod:
- Bezpečnost - Segmentace subjektů
- Menší broadcast domény - menší zahlcení sítě
- Flexibilata - Lze změnit síť nastavením nebo změnou portu.
- QoS - Prioritizace provozu (voice, video)
- Úspora hardware - Jeden switch, více sítí
### Typy VLAN
VLANy se dělí na několik druhů. Jimi jsou:
###### (Normální) VLAN
Normální síť. Komunikace je tagovaná.
###### Native VLAN
Liší se od normální VLAN, tím že svoji komunikaci NEtaguje. Výchozí nastavení je default (VLAN1). Doporučuje se změnit na jinou, pro zabránění útoku VLAN hopping *(např. na VLAN 999)*. V trunku smí být jen jedna. Používáme ji pro podporu zařízení co nerozumí tagům nebo jako kanál pro protokoly co používají netagovanou komunikaci.
###### Managent VLAN
VLAN dedikovaná pro managment switche a dalších síťových prvků. Přiřadí se IP adresa SVI (Switch Virtual Interface). Výchozí nastavení je VLAN 1. Doporučuje se změnit.
###### Voice VLAN
Speciální VLAN pro IP telefony. Při zapojení IP telefonu je IP telefon zapojen do switche a PC do IP telefonu *(a tím zpřístupňuje PC k síťi)*. Na portu switche jsou pak dvě VLANy. Jedna voice VLAN pro telefon a pak native VLAN pro PC. Voice VLAN má větší prioritu.

### Nastavení portů
Při přiřazování VLAN k portům můžeme porty dávat do různých módů. Tyto módy jsou.
###### Access port
Je  mód portu, kdy je k němu přiřazená pouze jedna VLAN. Přepíná pouze do portů se stejnou VLAN, nebo do trunku. Nastavujeme jej takhle:
1. `switchport mode access`
2. `switchport access vlan 10`
###### Trunk port
Přenáší více VLAN najednou. Rámce jsou tagované až na native VLAN. Ta v trunku může být jen jedna. Nastavujeme jej takhle:
1. `switchport mode trunk`
2. `switchport trunk allowed vlan 10,20,30`
3. `switchport trunk native vlan 99`
### Tagování rámce
Eternetový rámec se vrámci VLAN taguje. A to vrámci standartu IEEE 802.1Q. Upravený rámec vypadá takto.

| Cíl MAC | Zdroj. MAC | 802.1Q Tag | TPID                            | PCP                | VID               | Data + FCS |
| ------- | ---------- | ---------- | ------------------------------- | ------------------ | ----------------- | ---------- |
|         |            |            | indentifikátor tagu vždy 0x8100 | priorita (QoS 0-7) | číslo VLAN 0-4095 |            |
### Vytváření podsítí
Když rozdělujeme sítě, tak se nám bude hodit vědět jak se vytvářejí podsítě. Existují dvě metody.
###### FLSM (Sítě konstantní velikosti)
Všechny sítě mají stejný počet hostů. Sice jednoduché pro výpočet, ale plýtvání adresami
###### VLMS (Sítě různé velikosti)
Velikost sítí se přispůsubuje počtu hostů. Neplýtváme adresami.

*Typ: 2^n - 2 = počet hostů (n = počet bitů v masce)*

### Návrh podnikové sítě
Vrámci této otázky má student navrhnout síť s šesti VLANnama. Má přitom rozdělovat sítě pomocí VLSM.
1. **Výchozí zadání**
	* **Síť:** `192.168.0.0/24`
	* **Počet VLAN:** 6
	* **Metoda:** VLSM (Variable Length Subnet Mask)
	
	 Výpočet celkového prostoru
	* $32 - 24 = 8$ bitů pro hosty
	* $2^8 = 256$ adres k dispozici (`192.168.0.0` až `192.168.0.255`)

 2. **Návrh velikosti podsítí**
	Pokus 2 (Zredukované požadavky)
	* **VL1:** 25 hostů -> alokováno $32$ adres ($2^5$)
	* **VL2:** 25 hostů -> alokováno $32$ adres
	* **VL3:** 25 hostů -> alokováno $32$ adres
	* **VL4:** 30 hostů -> alokováno $32$ adres
	* **VL5:** 10 hostů -> alokováno $16$ adres ($2^4$)
	* **VL6:** 10 hostů -> alokováno $16$ adres
	* **Celkem:** $160$ adres ($\le 255$) -> **OK (vyhovuje)**

---
