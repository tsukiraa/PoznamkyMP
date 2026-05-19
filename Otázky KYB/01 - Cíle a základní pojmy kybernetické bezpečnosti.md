### Cíle kyberbezpečnosti

Cíle kybernetičnosti najdeme v její definici. Kybernetika je oborem, který se zabývá ochranou počítačových systémů a sítí před neoprávněným přístupem k systémům, krádeži informací, počítačovou kriminalitou nebo poškození hardwaru, softwaru a elektronických informací.

Tudíž jejím hlavním cílem je zajistit [důvěrnost, integritu a dostupnost](obsidian://open?vault=KYB&file=Ot%C3%A1zky%20KYB%2F02%20-%20CIA%20tri%C3%A1da%2C%20McCumber%20cube.md#CIA%20tri%C3%da)
) systémů.

To se dá rozepsat do těchto pěti bodů:

1. Identify (Identifikace): Pochopení toho co chráníme a jeho zranitelnosti. (Inventarizace, penetrační testování)
2. Protect (Ochrana): Vyvinout a implementovat vhodná opatření pro zajištění provozu a ochrany dat. (Šifrování, Fyzická obrana atd.)
3. Detect (Detekce): Snaha včasně identifikovat výskyt kybernetické události. (SIEM, NIDS, HISP, NetFlow atd.)
4. Respond (Reakce): Řeší co se má dělat po tom co nastane incident. (Plán reakce na incidenty, opatření)
5. Recovery (Obnova): Snaha se po útoku snažit obnovit poškozené systémy a zvýšit odolnost. (Zálohy)

 [Zdroj: Wikipedia](https://cs.wikipedia.org/wiki/Po%C4%8D%C3%ADta%C4%8Dov%C3%A1_bezpe%C4%8Dnost)

---

### Pojmy kybernetické bezpečnosti

###### Data
- Osobní data
	Údaje týkající se identifikovatelné osoby. (Fyzická osoba) V EU spadají pod ochranu GDPR
	Například:
	- Jméno a příjmení
	- Adresa (IP nebo Bydliště)
	- Kontaktní údaje (mail, telefonní číslo) ...
	[Zdroj: Wikipedia](https://cs.wikipedia.org/wiki/Osobn%C3%AD_%C3%BAdaj)
- Firemní data
	Údaje vlastněné organizací či firmou. Mohu mít pro firmu zásadní hodnotu. Jejich krádež nebo ztráta může vést k likvidaci firmy.
	- Výzkum
	- Data účetnictví
	- Smlouvy, seznamy zákazníků
	*Zdroj jsem nenašel ale asi není potřeba*
###### Hrozby
- Externí
	Zvenčí, mimo firmu, mimo organizaci, mimo síť
	- Vnější útočník
- Interní
	Útok zevnitř, z naší firmy, organizace, sítě
	- Zaměstnanec
	- Neznámé zařízení v síti
	- Více nebezpeční než vnější
	[Zdroj: Kaspersky](https://encyclopedia.kaspersky.com/knowledge/recognizing-internal-threats/)

######  Zranitelnost
Zranitelnost (anglicky vulnerability) je označení pro chybu v software, hardware, implementaci nebo v managementu, která způsobuje bezpečnostní problém.

###### Zneužití
Zneužití (anglicky exploit) je  činnost, které využívající chybu v software, hardware, implementaci nebo v managementu, která způsobí původně nezamýšlenou činnost a umožňuje tak získat nějaký prospěch.

###### Riziko
Riziko (anglicky risk) je označení pro pravděpodobnost že dojde k zneužití některé z zranitelností

###### Plocha útoku
Souhrn všech možných zranitelností, které může útočník využít k útoku.

###### Vektor útoku
"Cesta" kterou si útočník vybere pro útok na systém. Tudíš jeho výběr zranitelnosti a exploitu.


###### Typy Hackerů
- Blackhat
	- Hacker který napadá systémy pro obohacení. Kyberkriminálník.
- Grayhat
	- Hacker který využívá zranitelností bez vědomí majitele/správce daného systému, ale nedělá tak pro zisk. *"Dělá to tak nějak pro srandu"*
- Whitehat
	- Etický hacker, je placený aby odhaloval zranitelnosti a zkoušel je využít.
- State-sponsored
	- Hackeři placení státem. Nejčastěji se snaží ničit infrastrukturu, tvořit výpadky systémů nebo krást data v nepřátelských zemích.
- Hacktivist
	- Aktivista který využívá hacking pro prosazení svých cílů. Například: Anonymus a Ddos na Scientologii
- Script kiddie
	- Nezkušený hacker používající již vytvořené nástroje ostatních hackerů

###### Zero-day Attacks
Jedná se o útoky, které zneužívají do té doby neznámé zranitelnosti.
[Zdroj: Kaspersky](https://encyclopedia.kaspersky.com/glossary/zero-day-exploit/)


---

### Fáze útoků
*Nevím úplně co tím pospi myslel ale dávám sem Inrusion Kill chain*
Pro některé útoky nedávají některé kroky smysl. Např. Ddos nepotřebuje zadní vrátka ani komunikaci. Tudíž v tomto případě je vynecháváme. *asi* 

1. Reconnaissance - Získání informací o cíli a zjištění zranitelnosti (OS, rozložení sítě, OSINT)
2. Weaponization - Tvorba exploitu (Tvorba malware, příprava [Botnetu](03%20-%20Malware.md#Botnet) pro Ddos)
3. Delivery - Doruční exploitu na cíl (USB disk, Síť, E-mail atd.)
4. Explotion - Využití zranitelnosti (Spuštění programu, zahájení zahlcení)
5. Installation - Zajištení přístupu na počítač, aby fungoval i po vypnutí. (zadní vrátka)
6. Command & Control - Vytvoření kanálu pro komunikaci počítače s útočníkem 
7. Actions on Objectives - Využití sytému pro cíle útočníka (Krádež dat, zničení systému)
[Zdroj: Wikipedia](https://en.wikipedia.org/wiki/Cyberattack)