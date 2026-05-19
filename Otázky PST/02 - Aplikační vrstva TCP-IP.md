Aplikační vrstva modelu TCP/IP odpovídá vrstvám 5-7 v modelu [[04 - Referenční model OSI-ISO a síťové prvky|OSI/ISO]]. Protokly této vrstvy umožňují aplikacím přístup k komunikačnímu systému.
Podle toho jak aplikace mezi sebou komunikují (jaké sítě využívají), tak je řadíme do dvou různých modelů aplikace. P2P nebo Klient-Server.

|                | Klient-Server                                                               | Peer-to-Peer                                                                           |
| -------------- | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Model          | Centralizovaný - server obsluhuje jeho kilenty                              | Decentralizovaný - Uzle (počítače) se obsluhují navzájem (plní roli klienta i serveru) |
| Příklady       | Web (HTTP/HTTPS), email (SMTP/IMAP), FTP, [[03 - NAT, DHCP a DNS#DNS\|DNS]] | BitTorrent, Blokchain, SDN peering                                                     |
| Výhody         | Centrální správa, bezpečnost, kontrola přístupu                             | Bez jediného bodu selhání, levnější, škáluje se sám                                    |
| Nevýhody       | Jediný bod selhání, náklady serveru                                         | Obtížná správa, bezpečností rizika, norovnoměrné zatížení                              |
| Škálovatelnost | Server se musí vylepšit při růstu                                           | Roste s počtem uzlů                                                                    |
### Protokoly dle otázky

#### Web server (port 80/443)
Web server může využívat dva protokoly HTTP (port 80) a HTTPS (port 443). S tím že HTTPS je zabezpečená verze (více později). Komunikace probíhá formou požadavků a odpovedí. Tyto požadavky u HTTP/HTTPS jsou:

- GET - získej data (čtení) např.: `GET http://mojesit.cz/index.html` => pošli mi web
- POST - pošli data např.: `POST http://mojesit.cz/kontaktni-formular` => odešli údaje
- PUT - aktualizuj celý záznam např.:`PUT http://mojesit.cz/profil/uzivatel123` => nahraď všechny stávající
- DELETE - Smaž např.: `DELETE http://mojesit.cz/clanek/stary-prispevek` => odstraň ze serveru
- HEAD - Pošli jen halvičku např.:`HEAD http://mojesit.cz/velky-soubor.zip` => zjisti, jak je soubor velký nebo kdy byl naposledy změněn

###### HTTP/HTTPS verze
Protokoly HTTP/HTTPS měl 3 hlavní verze:

- HTTP/1.1 - Starší standart, který pro komunikaci využívá jen jedno TCP spojení. Sériové (nejdříve jeden požadavek, pak další) => pomalé
- HTTP/2 - Přináší multipexing, vrámci jednoho spojení více požadavků
- HTTP/3 - Změna z TCP na UDP (QUIC)

###### HTTPS
Zabezpečená verze HTTP. Pro šifrování používa TLS (nástupce SSL). Každá stránka má certifikát vydáván certifikační autoritou pro verifikaci indentity a veřejného klíče webového serveru. Před šifrování proběhne tzv. TLS handshake. Při něm se dohodnou na šifrovacích algoritmech, a vymění si klíč od symetrické šifry, který je při přenosu šifrován asymetrickou šifrou. Komunikace pak probíhá přes symetrické šifrování (nejčastěji AES). Je rychlejší.

![[02 - TSL handshake.png|639]]

#### FTP (port 21/20)
Protokol FTP (File Transfer Protocol) slouží pro stahování nebo nahrávání souborů na server. Jedná se o klient-server aplikaci. Komunikuje přes TCP. Port 21 se používá pro předávání příkazů a port 20 pro předávání souborů. Má dva režimy. Aktivní (server se připojí k klientovi) a pasivní (klient se připojí k serveru ([[03 - NAT, DHCP a DNS#NAT|NAT]] friendly). Nešifrovaná verze.

#### FTPS/SFTP (port 990/22)
Jedná se o protokol FTP ale šifrovaný. FTPS je šifrovaný přes TLS (990). SFTP je FTP přes tunel SSH (port 22).

#### SMB (port 445)
SMB (Server Message Block) je protokol pro výměnu souborů a tiskáren ve sítích windows. Pro komunikaci používá TCP přes port 445. Od SMBv3 je komunikace šifrována.

#### NFS (port 2049)
NFS (Network File System) slouží k sdílení souborů v linuxu. Klient se připojuje adresář stejně jako lokální a to za pomoci příkazu `mount` . V základu není komunikace zabezpečená. Lze doplnit přes  Kerberos nebo doplněk TSL u NFSv4. Pro přenos používá jak TCP tak UDP přes port 2049.

#### TFTP (port 69)
TFTP slouží pro jednoduchý přenos souborů bez autentizace. Využívá se při bootingu přes síť, nahrání firmware přes síť.

### Telnet (port 23)
Telnet je protokol sloužící pro vzdálené připojení k příkazové řádce počítače. Nezašifrovaný (vše je plain text). Používá TCP a port 23.

#### SSH (port 22)
SSH je šifrovaná náhrada Telnetu. Dnes se používá verze SSH-2 (SSH-1 je zranitelné). Přes funkci SFTP je SSH schopno přenášet i soubory.  Dále se využívá i port-forwardingu a tunelování protokolů.

#### NTP (port 123)
Network Time Protocol synchronizuje čas mezi počítači. Servery nabízející synchronizaci jsou  značeny:

- Stratum 0 - Atomové hodiny
- Stratum 1 - Přímo napojené na stratum 0
- Stratum 2+ - klienti a servery níže

Přesnost se pohybuje v milisekundách v síti, mikrosekundy v LAN

#### Email
Pro internetovou poštu používáme tři protokoly:
###### SMTP (port)
SMTP (Simple Mail Transfer Protocol) slouží pro odesílání pošty z klienta na server (port 587), nebo z serveru na server (port 25) Nešifrovaný
###### POP3
POP3 poštu přijímá a to tak že zprávy ze serveru stáhne (lokálně uloží) a na serveru je odstraní. Vhodné pro jedno zařízení a offline práci. Nezašifrováno.
###### IMAP
IMAP taky poštu přijímá, ale zprávy na serveru zůstávají. Vhodné pro více zařízení. Dnes více používaný protokol.
###### SMTPS/IMAPS
Šifrované verze přes TLS. Preferovat před nešifrovanými verzemi.

##### Cesta mailu
1. Klient odešle email na server (SMTP port 587)
2. Server odesílatele odešle email na server příjemce (SMTP port 25)
3. Příjemce si zprávu stáhne do schránky (IMAP/POP3 port 143/110)

###### Detaily
- [[03 - NAT, DHCP a DNS#DNS|DNS]] MX záznamy -  odesílací server se zaptá [[03 - NAT, DHCP a DNS#DNS|DNS]]. Kdo příjímá maily pro *doména.cz*
- Spam filtry - na přijímacím serveru se kontrolují SPF, DKIM, DMARC záznamy
	- SPF - Ověří zda tento server smí odesílat maily na tuto doménu
	- DKIM - digitální podpis mailu, ověří že mail nebyl změněn
	- DMARC - říká co s maily, co nesplní SPF nebo DKIM dělat
