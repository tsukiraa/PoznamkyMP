VLANy se chovají jako každé jiné odělené sítě. Proto je potřeba spojení těchto virtuálních sítí nějaké L3 zařízení. Pro Inter-VLAN routing se používají přesněji 3 metody.

### Legacy metoda
Princip této metody je ten, že se k každé VLANě budeme chovat jako kdyby to byl samostaný switch. V praxi to vypadá tak že na úrovně switche přiřadíme ke portům naše VLANy a ty propojíme s routerem.

![[06 - Legacy Inter-VLAN routing.png]]
### Router-on-a-Stick
Narozdíl od předešlé metody do routeru zapojíme trunk a rozřazujeme komunikaci vněm na subinterface. Na switchi [[05 - VLAN a Návrh podnikové sítě#Trunk port|vytvoříme trunk]] a zapojíme ho do routeru. V roueru začneme takto tvořit subinterface:
1. `interface gigabitEthernet 0/0/0.10` <= vytvoření subinterface
2. `encapsulation dot1Q 10` <= přiřazení VLANy *(bez tohoto příkazu subinterface začne brát native)*
3. `ip address ...` <= přiřazení ip adresy

![[06 - Router-on-a Stick.png]]

### L3 Switch
Tato metoda funguje tak že se pro každou VLANu vytvoří [[05 - VLAN a Návrh podnikové sítě#Managent VLAN|SVI]]. Přes ně pak switch routeuje VLANy. 
###### VLANy s access porty na L3 switch
Pouze vytvořím SVI pro každou VLAN a zapnu routování.
1. `ip routing` <= zapnutí routingu
2. `interface vlan 10` <= vytvoření SVI
3. `ip address ...` <= přiřazení ip adresy
###### Připojený L2 switch s vlanama
Připojím k L3 switch a pracuju jako kdyby byli připojeny přes access port
1. `interface gigabitEthernet 0/0`
2. `switchport trunk encapsulation dotq1` <= roztřídit trunk
3. `switchport mode trunk`
4. `switchport trunk allowed vlan 10,20,30`
###### Route mimo VLANy
Vypnu switchport a přiřadím ip adresu
1. `interface gigabitEthernet 0/0`
2. `no switchport`
3. `ip address ...`

### Porovnání

|                     | Legacy                  | Router-on-a-Stick         | L3 Switch SVI          |
| ------------------- | ----------------------- | ------------------------- | ---------------------- |
| Počet portů routeru | 1 port na VLAN          | 1 port celkem             | Router volitelný       |
| Výkon/Propustnost   | Omezena rychlostí portů | 1 Gb/s sdílí všechny VLAN | Rychlý - HW akcelerace |
| Škálovatelnost      | Nízká - Omezení porty   | Střední - 4-5 VLANů       | Vysoká - desítky VLANů |
| Latence             | Nízká (přímé linky)     | Nízká až střední          | Nejnižší (wire-speed)  |
| Nasazení dnes       | Nepoužívá se            | SOHO, malé sítě           | Podniková síť          |
