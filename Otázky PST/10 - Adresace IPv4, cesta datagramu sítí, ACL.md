*Z nějakého záhadného důvodu chce prokeš u adresace popsat 2. vrstvu, proč nevím. Asi to vidí tak že adresa je adresa.*
### Adresace na nižších vrstvách
*Nejspíš*, pro pochopení adresace IPv4 je potřeba pochopit adresaci na L2 a L1. Proto to tady rozepíšeme:
#### *"Vrstva L1"* (Pojemnování jednotlivých portů rozhraní (Cisco))
Na switchích a routrech povětšinou máme více rozhraní do, kterých můžeme zapojovat naše média, proto aby se nám nepletli, tak jsou systemanticky pojmenovaný.
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
IPv4 adresa je 32 bitové číslo napsané v desítkové soustavě a notaci. Skládá se z části síťové a části pro hosta. Toto rozdělení určuje maska sítě. IPv4 adresy řadíme do velkých bloků adres po kterých se rozdávali, těmto blokům říkáme třídy.