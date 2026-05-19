### CIA triáda
Jak již bylo napsáno v první otázce, tak CIA triáda v podstatě zahrnuje cíle kybernetické bezpečnosti. Jejími principy jsou:

**C**onfidentiality (Důvěrnost): 
- Data vidí jen lidi co je vidět mají, a že nejsou šířeny.
- Zajišťujeme tím že:
	- šifrujeme
	- řídíme přístup k datům (AAA)
	- Zamezení šíření (DLP)
- Porušení důvěrnosti je:
	- Data breach
	- Odposlech (MITM)
	- Leak

**I**ntegrity (Integrita): 
- Zaručuje to že data zůstanou ve svém stavu, nebo že se mění tak jak mají.
- Zajišťujeme tím že:
	- Tvoříme Hashe, Kontrolní součty, digitálními podpisy
- Porušení Integrity vypadá tak že:
	- Zničení dat ([Ransomware](03%20-%20Malware.md#Ransomeware))
	- Neoprávněná změna dat

**A**vailability (Dostupnost): 
- Systémy jsou vždy dostupné pro autorizované uživatele.
- Zajišťujeme:
	- Zálohováním
	- Redundance, Load balancing
	- Disaster recovery, Playbook
- Porušení dostupnosti způsobuje:
	- Ddos
	- Fyzické vlivy (Přírodní katastrofa)
	- Softwarové chyby

---
### McCumber cube
Jedná se o model, který slouží k vyhodnocování a tvoření kybernetických opatření *(jestli jsme na něco nezapoměli)*. Skládá se ze tří dimenzí a tvar kostky 3x3x3 (27 oblastí).  Tyto tři dimenze jsou:

- CIA triáda (Důvěrnost, integrita, dostupnost)
- Stav dat (Zpracovávané, uložené a v přenosu)
- Typ opatření (Technologická, Pravidla a postupy, Lidé a školení)

Příklady co patří do jakého pole: *generováno gemini*

| **Důvěrnost (Confidentiality)** | **Opatření: Technologie**                    | **Opatření: Politika a praxe**                                  | **Opatření: Vzdělávání**                                                 |
| ------------------------------- | -------------------------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------ |
| **Uložení (Storage)**           | Šifrování pevného disku (AES-256).           | Směrnice o povinném uzamykání dokumentů do trezoru.             | Školení zaměstnanců, proč nenechávat hesla na papírku u PC.              |
| **Přenos (Transmission)**       | Použití protokolu TLS/SSL pro webový provoz. | Zákaz posílání citlivých údajů běžným nešifrovaným e-mailem.    | Workshop o rizicích používání veřejných Wi-Fi sítí.                      |
| **Zpracování (Processing)**     | Izolace procesů v paměti RAM (Sandboxing).   | Pravidlo „Clean Desk“, aby nikdo nepovolaný neviděl na monitor. | Instruktáž o tom, jak bezpečně pracovat s daty v otevřených kancelářích. |

| **Integrita (Integrity)**   | **Opatření: Technologie**                                    | **Opatření: Politika a praxe**                                   | **Opatření: Vzdělávání**                                            |
| --------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------------- | ------------------------------------------------------------------- |
| **Uložení (Storage)**       | Použití kontrolních součtů (Hash) k detekci změn v databázi. | Proces pravidelné kontroly a auditu integrity uložených dat.     | Školení o tom, jak rozpoznat poškozený soubor nebo databázi.        |
| **Přenos (Transmission)**   | Digitální podpisy potvrzující neměnnost zprávy při cestě.    | Povinnost potvrzovat příjem důležitých dat protistranou.         | Vysvětlení principu Man-in-the-Middle útoků a jak jim předcházet.   |
| **Zpracování (Processing)** | Validace vstupních dat v aplikaci (prevence SQL injection).  | Standardní operační postupy (SOP) pro manuální úpravy v systému. | Trénink vývojářů v psaní bezpečného kódu, který nemění data chybně. |

| **Dostupnost (Availability)** | **Opatření: Technologie**                                     | **Opatření: Politika a praxe**                              | **Opatření: Vzdělávání**                                               |
| ----------------------------- | ------------------------------------------------------------- | ----------------------------------------------------------- | ---------------------------------------------------------------------- |
| **Uložení (Storage)**         | Nasazení diskového pole RAID nebo redundantního cloudu.       | Plán obnovy po havárii (Disaster Recovery Plan) pro zálohy. | Cvičný „fire drill“ – zkouška obnovy dat ze záloh zaměstnanci IT.      |
| **Přenos (Transmission)**     | Zdvojené internetové připojení od dvou různých poskytovatelů. | SLA smlouvy s garantovanou dobou opravy konektivity.        | Poučení personálu, co dělat a koho volat při výpadku sítě.             |
| **Zpracování (Processing)**   | Load Balancery rozdělující zátěž mezi více serverů.           | Politika údržby systémů mimo pracovní špičku.               | Školení operátorů, jak monitorovat přetížení systému a včas zasáhnout. |
### AAA
je model, který slouží pro kontrolu a sledování přístupu v systémech. Jednotlivá Ačka znamenají:
**A**uthentication (Autentizace) - Ověření uživatele (Heslo, karta, biometrika...) 
**A**uthorizacion (Autorizace) - Oprávnění uživatele (Řízení přístupu)
**A**ccounting (Účtování) - Logování a schopnost dohledat co kdo dělal