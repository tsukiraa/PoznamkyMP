Jak jsme již rozebírali ve [[04 - Referenční model OSI-ISO a síťové prvky|čtvrté otázce]], čtvrtá vrstva (protokoly UDP a TCP) zařizují End-to-end spojení mezi odesílatelem a příjemcem. Toto spojení vzniká mez dvěma potry (Něco jako IP pro 4 vrstvu) techto zařízení. Tato vrstva operuje nad síťovou infrastrukturou internetu, která je založena na **přepínání datagramů** na 3. vrstvě, je však důležité mínit že v jiných (telefoních) sítích se používá **přepínání obvodů**. Zde jsou jejich rozdíly.

|          | Přepínání obvodů                                                       | Přepínání datagramů                                                 |
| -------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------- |
| Princip  | Před přenosem se vytvoří vyhrazená cesta mezi odesílatelem a příjemcem | Každý paket jde nezávisle. Různé pakety mouhou jít různými cestami. |
| Příklad  | Telefoní síť, PSTN, ISDN                                               | Internet                                                            |
| Výhody   | Garantovaná šířka pásma, konstantní latence                            | Efektivní využití sítě, oddolnost proti výpadkům                    |
| Nevýhody | Plýtvání kapacitou při tichu, nutné navázání okruhu                    | Variabilní latence, možné zahaování paketů                          |
*Reálně moc nechápu proč tohle po nás chtěj. Zkráceně se dá říct že 3. vrstva funguje na principu přepínání datagramů. Na čtvrté vstvě se pak protokol TCP snaží o "virtualizaci" přepínání obvodů, zatímco UDP zachovává přepínání datagramů.*

---
### TCP (Transmission Control Protocol)
TCP je spolehlivý, spojovaný protocol L4. Garantuje doručení, pořadí i integritu dat. Používá se hlavně při přenosech, kdy je pro nás klíčová integita dat. (Stahování, načítání obrázků, SSH)
###### Formát segmentu (hlavička)
 Protokol TCP má hlavičku o 8 polích a velikosti min 20 bajtů. Níže jsou uvedena i s jejich funkcí:

| Zdrojový port (16b)               | Cílový port (16b)                                                                     | Pořadové číslo (32b)                                                     | Potvrzovací číslo (32b)                            |
| --------------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | -------------------------------------------------- |
| Identifikace aplikace odesílatele | Identifikace aplikace příjemce                                                        | Pořadové číslo prvního bajtu segmentu. Zajišťuje řazení.                 | Čislo dalšího očekávaného bajtu. Potvrzuje příjem. |
| **Délka HDR/Rezerva (4+6b)**      | **Příznaky (6b)**                                                                     | **Velikost okna (16b)**                                                  | **Kontrolní součet/ URG ptr (16+16b)**             |
| - // -                            | SYN=Navázání, ACK=Potvrení, FIN=Ukončení, RST=Reset, PSH=Okamžité,URG=Urgentí zpráva. | Velikost přijímacího okna. Kolik bajtů přijmeme bez potvrzení komunikace | Detekce chyb                                       |
###### Navázání a ukončení spojení TCP
Pro náváznání spojení skrz TPC musí počítače mezi sebou provést tzv. 3-way handshake. To 3-way znamená, že má tři kroky. Ty jsou:

1. SYN (Klient → Server) - "Já klient, chci s tebou navázat spojení na tomto portě."
2. SYN-ACK (Server → Klient)  - "Já server chci s tebou navázat spojení na tomto portě, spojení schvaluji"
3. ACK (Klient → Server) - "Potvrzuji začátek spojení TCP"

Pro ukončení spojení se, pak využívá tzv. 4-way handshake. Jeho 4 kroky jsou tyto:

1. FIN (Klient → Server) - "Už nechci komunikovat"
2. ACK (Server → Klient) - "Ok chápu"
3. FIN (Server → Klient) - "Taky už nechci komunikovat"
4. ACK (Klient → Server) - "OK"

![[09 - TCP, Handshakes.png|479]]

##### Segmantace, multiplexing, řízení toku
###### Segmentace
Pokud jsou data příliš velká nato aby se vešla do jednoho datagramu, tak josu segmentovány. MSS (Maximum Segment Size) lze dopočítat. (MSS = MTU - IP hdr - TCP hdr (1500 - 20 - 20 = 1460))
Po přijetí jsou segmenty sestaveny zpět a to dle seq čísel.
###### Multiplexing
Jedna aplikace může využívat více portů a TCP spojení k komunikaci. Například každá záložka prohlížeče svoje spojení.
###### Řízení toku (Flowcontrol)
Příjemce se s odesíletelem domlová na tzv. window size. To určuje kolik datagramů smí odesílatel přijmout bez potvrzení o přijetí. Pokud příjemce nestíhá obsluhovat spojení, může window size zmenšit a odesílatel zpomalí. 

Příklad pro windowsize = 3:

| 1                      | 2                      | 3        | 4        | 5           | 6                  | 7                  | 8                  | 9                  | 10                 |
| ---------------------- | ---------------------- | -------- | -------- | ----------- | ------------------ | ------------------ | ------------------ | ------------------ | ------------------ |
| Odesláno (Přijato ACK) | Odesláno (Přijato ACK) | Odesláno | Odesláno | Lze odeslat | Čekání  (Za oknem) | Čekání  (Za oknem) | Čekání  (Za oknem) | Čekání  (Za oknem) | Čekání  (Za oknem) |
###### Řízení Přetížení
Řízení přetížení se snaží řídít komunikaci tak, aby se ani jeden z koncových zařízení nepřetížil. Používá k tomu tyto taktiky.
- **Slow start** - Po navázání spojení je okno vždy malé a postupně se zvětšuje. Při každém ACK ho zdvojnásobí
- **Congestion Avoidence** - po dosažení prahu (sstresh), se okna navyšují lineárně
- **Fast Retransmit** - po obdržení třech duplicitních ACK, tak znovu odešle ztracený packet
###### Omezení režie
TCP se snaží menší datagramy sjednotit do jednoho datagramu. Na malé datagramy čeká 200ms. Používá tzv. Nagle algoritmus.

---
### UDP (User Datagram Protocol)

UDP je nespolehlivý, nespojovaný protokol L4. Narozdíl od TCP nic nezaručuje. Díky tomu je rychlejší než TCP. Používá se v aplikacích, kde je důležitá nízká latence. (VoIP, VideoCall atd.)
###### Formát segmentu
Narozdíl od TCP je hlavička protokolu UDP pouze o 4 polích a má pouze 8 bajtů.

| Zdrojový port (16b) | Cílový port (16b) | Délka(16b) | Kontrolní součet(16b) |
| ------------------- | ----------------- | ---------- | --------------------- |

---
### Provnání TCP a UDP

|              | TCP                                    | UDP                              |
| ------------ | -------------------------------------- | -------------------------------- |
| Typ          | Spojovaný                              | Nespojovaný                      |
| Spolehlivost | Garantuje doručení, pořadí a integritu | Negarantuje nic                  |
| Handshake    | 3-way handshake před přenosem          | Nemá handshake                   |
| Řízení toku  | Window size + congestion control       | žádný řízení toku                |
| Rychlost     | Pomalejší - režie navíc                | rychlejší                        |
| Hlavička     | 20 bajtů                               | 8 bajtů                          |
| Použití      | HTTPS,e-mail,FTP,SSH                   | Video streaming, VoIP, DNS. DHCP |

---
### Čísla portů
Každý počítač má své porty. Tyto porty jsou rozřazeny do několika rozsahů, podle toho k čemu slouží.

- **Porty 0 - 1023** - Well-known porty: Porty vyhrazené pro standartní porty. Přikazuje IANA
- **Porty 1024-49151** - Registred porty: Registrované porty pro aplikace. Registrovány u IANA
- **Porty 49152 - 65535** - Dynamické porty: Porty sloužící klientským aplikacím pro spojení se serverem

Seznam aplikačních protoklů a jejich well-known portů je probíráno v otázce [[02 - Aplikační vrstva TCP-IP#Protokoly dle otázky|02]].

---
### RTP a RTPC
RTP a RTPC slouží k přenosu zvuku a videa v reálném čase přes UDP. Negarantují doručení, ale minimalizují latenci
###### RTP (Real-time Transport Protocol)
Zajišťuje přenos zvuku a videa v reálném čase. Obsahuje tyto funkce.
- Pořadová čísla - příjemce zjistí ztrátu, výpadek nebo špatné pořadí datagramů
- Časové razítko - synchronizace přehrávání.
- SSRC - identifikátor zdroje (kdo vysílá)
- Payload type - Typ kodeku
Sudé porty využívá pro video a audio a liché pro RTCP
###### RTCP (RTP Control Protocol)
Řídící protokol k RTP. Posílá informace o kvalitě ořenosu. Oddělené UDP spojení na portu RTP + 1. Obsahuje:
- SR - Statistiky odesílatele
- RR - tráta paetu, jitter, RTT
- SDES - informace o účastnících
- BYE - oznámení o ukončení přenosu

Tyto protokoly používají VoIP aplikace (skype, teams atd.)

