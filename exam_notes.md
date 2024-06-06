# Examen Retele

- [Examen Retele](#examen-retele)
  - [Cerințe](#cerințe)
  - [Pașii](#pașii)
    - [Pasul 1](#pasul-1)
    - [Pasul 2](#pasul-2)
    - [Pasul 3](#pasul-3)
  - [Alocarea de Adrese IP](#alocarea-de-adrese-ip)
  - [Implementare](#implementare)
    - [Mod de Lucru](#mod-de-lucru)
    - [Tipuri de Conexiuni, Dispozitive, și Module](#tipuri-de-conexiuni-dispozitive-și-module)
    - [End-Devices](#end-devices)
      - [Laptop-ul SERVICE](#laptop-ul-service)
      - [Configurarea unui PC](#configurarea-unui-pc)
      - [Configurarea unui PC pentru testare DHCP](#configurarea-unui-pc-pentru-testare-dhcp)
      - [Configurarea unui Laptop Wi-Fi](#configurarea-unui-laptop-wi-fi)
      - [Server-ul](#server-ul)
    - [Dispozitive Intermediare (cu Cisco IOS)](#dispozitive-intermediare-cu-cisco-ios)
      - [Configurarea comună](#configurarea-comună)
      - [Configurarea specifică switch-urilor](#configurarea-specifică-switch-urilor)
      - [Configurarea specifică routere](#configurarea-specifică-routere)
    - [Configurarea Routerului Wi-Fi](#configurarea-routerului-wi-fi)
      - [Setup \> Internet Setup](#setup--internet-setup)
      - [Setup \> Network Setup](#setup--network-setup)
      - [Wireless \> Basic Wireless Settings](#wireless--basic-wireless-settings)
      - [Wireless \> Wireless Security](#wireless--wireless-security)
      - [Wireless \> Wireless MAC Filter](#wireless--wireless-mac-filter)
  - [Testare](#testare)

## Cerințe

Se va da o adresă aleatorie din rețea, împreună cu prefix length.
În plus, va da o listă de sub-rețele, cu numarul minim de adrese dorite pentru fiecare sub-rețea.

În pașii 1, 2, și 3, vom calcula network address (NA), broadcast address (BA), și range address (RA) pentru rețeaua mare,
vom găsi numărul minim de biți necesari pentru fiecare sub-rețea, vom aloca spațiu în rețea pentru fiecare sub-rețea,
și vom calcula NA, BA, și RA pentru fiecare sub-rețea.

După ce terminăm calculeze, vom implementa în Packet Tracer topologia.

Unele sub-rețele vor avea niște cerințe extra:

- Una va avea serverul (un singur server pentru toate serviciile - mail, DNS, syslog, FTP, etc).
  Rețeaua cu server nu va conține hosturi și nu va folosi DHCP.
- Una va avea DHCP pe ea (probabil cea cu serverul?). Serviciul DHCP va rula pe router, nu pe server.
- Una (sau mai multe?) va avea acces Wi-Fi printr-un router Wi-Fi atașat la ea.

În rețelele cu hosturi (i.e. non-server), pe lânga un host cu adresa statică, vom avea și un host pentru testare DHCP.

## Pașii

Pașii trebuie scriși în felul în care a zis Drăgan ca să se puncteze, fără nimic absent sau în plus.

Nu vom avea voie cu o ciornă de hârtie, cu materiale, sau cu programul de calculator de pe computer, doar cu programul Packet Tracer. Totul se scrie în note text în Packet Tracer.

Totul trebuie scris într-o singură notă, cu excepția dacă nu încape pe ecran totul, atunci vom împărți în mai multe note, dar nu mai multe decât e nevoie.

O mare parte din notă vine de la pașii 1, 2, și 3.
Drăgan a zis că dacă vede greșeli aici, oprește evaluarea!

### Pasul 1

Trebuie calculat NA, BA, și RA pentru rețeaua mare (sub-rețelele nu sunt relevante aici).

Se va scrie adresa IP dată în binar, se va scrie sub ea subnet mask-ul în binar, și apoi se va scrie rezultatul de AND binar, pentru a obține NA.

RA este range-ul de adrese folosibile, adică de la NA+1 la BA-1.

BA se obține prin OR între NA și inversul subnet mask-ului. Nu e nevoie să se scrie OR-ul explicit ca la AND.

Apoi se va scrie în format zecimal cu prefix length: NA, BA și RA.

Exemplu - se dă aleatoriu 156.234.54.10/22, pasul 1 va fi:

```text
PAS 1

156.234.54.10/22

I.P. = 10011100.11101010.00110110.00001010
S.M. = 11111111.11111111.11111100.00000000
N.A. = 10011100.11101010.00110100.00000000

N.A.: 156.234.52.0/22
B.A.: 156.234.55.255/22
R.A.: 156.234.52.1-156.234.55.254/22
```

### Pasul 2

Se calculează numărul minim de biți necesari pentru fiecare sub-rețea.
Atenție, dacă se cere 15 adrese, nu sunt suficienți 4 biți, ci 5, fiindca 2 adrese sunt ocupate de NA și BA.

La pasul 2 vom scrie sub-rețelele în ordine descrescătoare a mărimii lor, nu în ordinea în care au fost date.

Trebuie scrise și WAN-urile care vor conecta sub-rețelele între ele. Dacă avem 4 sub-rețele, vom avea 3 WAN-uri.

În plus, va fi un WAN pentru Wi-Fi, fiindcă routerul Wi-Fi va fi conectat la sub-rețeaua care are acces Wi-Fi prin un WAN. Nu se va scrie rețeaua Wi-Fi în sine la pasul 2, doar WAN-ul care o conectează la sub-rețeaua ei.

Exemplu - se cer sub-rețelele CLUJ de 63, BUCURESTI de 126, cu Wi-Fi, și TIMISOARA de 127, deci 3 LAN-uri și 3 WAN-uri (unul pentru Wi-Fi):

```text
PAS 2

2^7 <= 127 <= 2^8
2^6 <= 126 <= 2^7
2^6 <= 63 <= 2^7
2^1 <= 2 <= 2^2
2^1 <= 2 <= 2^2
2^1 <= 2 <= 2^2
```

Atenție - 2^7 <= 127 <= 2^8 este incorect, deoarece am scris că 128 <= 127. Dar profesorul vrea să scriem așa, deși este un mod foarte ciudat de a scrie, deci așa trebuie scris.

În Packet Tracer, trebuie folosită secvența escape `&le;` pentru simbolul de mai mic sau egal. Nu merge să folosim `<=` direct, pare că se face niște HTML sanitization sau parsing ciudat.

### Pasul 3

Vom aloca spațiu pentru fiecare sub-rețea, începând de la NA-ul rețelei mari, în ordine descrescătoare a mărimii sub-rețelelor, nu în ordinea în care au fost date, inclusiv WAN-urile.

NA-ul unei sub-rețele va fi BA-ul sub-rețelei precedente + 1. Începem de la NA-ul rețelei mari.

Se va scrie NA, BA, și RA, pentru fiecare sub-rețea, cu prefix length-ul corespunzător biților calculați la pasul 2.

Exemplu cu aceleași rețetele ca la exemplul de la pasul 2 (rețea mare 156.234.52.0/22, ca la exemplul 1):

```text
PAS 3

TIMISOARA = 127
N.A.: 156.234.52.0/24
B.A.: 156.234.52.255/24
R.A.: 156.234.52.1-156.234.52.254/24

BUCURESTI = 126
N.A.: 156.234.53.0/25
B.A.: 156.234.53.127/25
R.A.: 156.234.53.1-156.234.53.126/25

CLUJ = 63
N.A.: 156.234.53.128/25
B.A.: 156.234.53.255/25
R.A.: 156.234.53.127-156.234.53.254/25

CLUJ-BUCURESTI
N.A.: 156.234.54.0/30
B.A.: 156.234.54.3/30
R.A.: 156.234.54.1-156.234.54.2/30

BUCURESTI-Wi-Fi
N.A.: 156.234.54.4/30
B.A.: 156.234.54.7/30
R.A.: 156.234.54.5-156.234.54.6/30

BUCURESTI-TIMISOARA
N.A.: 156.234.54.8/30
B.A.: 156.234.54.11/30
R.A.: 156.234.54.9-156.234.54.10/30
```

WAN-urile au fost CLUJ-BUCURESTI, BUCURESTI-Wi-Fi, și BUCURESTI-TIMISOARA.

Nu au fost TIMISOARA-BUCURESTI, BUCURESTI-Wi-Fi, și BUCURESTI-CLUJ, fiindcă rețelele se conectează în ordinea în care au fost date, nu ordinea sortată descrescător după mărime.

Nu s-a alocat spațiu pentru Wi-Fi, doar pentru WAN-ul BUCURESTI-Wi-Fi, fiindcă rețeaua Wi-Fi va folosi NAT, nu va avea spațiu alocat în rețea, decât o singura adresă statică pentru routerul Wi-Fi.

## Alocarea de Adrese IP

În fiecare sub-rețea LAN, se vor aloca adresele IP în ordinea asta, începând de la NA + 1:

1. Default gateway.

   Va fi mereu primul IP folosibil.

2. Switch-uri.

   Deși la examen va fi suficient un singur switch, trebuie rezervate adrese pentru toate switch-urile care pot fi necesare - numarul de adrese folosibile împărțit la 26, rotunjit în sus.

   Exemplu - se cer 700 de adrese într-o rețea, deci vom avea 1022 adrese folosibile. Vom rezerva 1022/26 rotunjit în sus = 40 de adrese pentru switch-uri, deși un singur switch va fi prezent la examen.

3. Hosturi (pe rețele non-server).

    Hosturile vor fi alocate după switch-uri.

4. Server (doar pe rețeaua cu server).

   Server va avea ultima adresă folosibilă, nu prima după switch-uri, ca la hosturi.

   Exemplu - în rețeaua 192.168.0.0/24, serverul va avea adresa 192.168.0.254

În WAN-uri, avem doar două adrese folosibile, una pentru fiecare capăt al conexiunii serial.

## Implementare

Vom crea topologia în Packet Tracer, cu toate LAN-urile și WAN-urile, hosturi, servere, switch-uri, routere, router Wi-Fi.

### Mod de Lucru

Nu se va lucra cu tab-ul CLI sau tab-ul Config, fiindcă e nerealist. Vom adăuga un laptop SERVICE pe care îl vom folosi pentru a accesa prin cablu consolă dispozitivele. Pentru hosturi PC și Laptop, folosim tab-ul Desktop.

Avem grijă să nu încercăm să modificăm module fără să oprim dispozitivul mai întâi, și să nu conectăm cabluri la porturile greșite! Dacă facem ceva ce ar strica echipamentul în viața reală, nu e bine.

Înainte să adăugăm un host, switch, sau router, cream o mică nota cu adresa IP pe care avem de gând să o folosim pentru el (pentru routere, multiple adrese pt. LAN + WAN).

Vom crea rețeaua pornind de la un host în prima sub-rețea -> progresând la switch -> apoi router, apoi mai departe router -> switch -> host în a doua sub-rețea, și tot așa ("de la stânga la dreapta", "de la mic la mare", etc).

Dacă o rețea are Wi-Fi, vom configura routerul Wi-Fi, și cu niște laptopuri Wi-Fi, înainte să progresăm mai departe la următoarea sub-rețea.

Trebuie să configuram complet un dispozitiv înainte să trecem la următorul, i.e. setăm complet rutele statice pe un router, chiar dacă nu am creat încă rețelele la care aceste rute se referă.

Ne asigurăm să salvăm în Cisco IOS configurația (`copy running-config startup-config`).

Nu lăsăm o conexiune SSH activă pe vreun host după ce plecăm de la el pentru că e nesigur.

Trebuie să testăm complet conectivitatea și serviciile pe care le-am configurat, și să ne asigurăm că totul funcționează corect.

Toate dispozitivele trebuie să aibă numele setat în Packet Tracer, nu lăsate cu Router0, PC0, etc.

Conform lui Drăgan: e mai bine să facem mai puțin, dar să fie corect, decât să facem mai mult, dar să fie cu greșeli.

### Tipuri de Conexiuni, Dispozitive, și Module

Conexiune host-switch, switch-router: straight-through Ethernet.

Conexiune router-router Wi-Fi: crossover Ethernet.

Conexiune router-router: serial. Capătul DCE ar trebui să fie în direcția serverului, dacă urmărești DCE-ul să ajungi la routerul rețelei cu serverul.

Conexiune host-router, host-host: crossover, dar nu ar trebui să avem conexiune directă între două hosturi, sau între un host și un router.

Conexiune SERVICE-router, SERVICE-switch: cablu consolă.

Dacă e posibil, ar trebui să facem astfel încât pe ambele capete ale conexiunii să avem aceeași etichetă de port. I.e. două routere conectate să aibă ambele S0/0, nu S0/0 și S0/1, ca să fie mai ușor de urmărit.

Când adăugam un host desktop, înlocuim modulul de Ethernet pre-instalat cu un modul CGE, pentru a avea Gigabit Ethernet.

Pentru laptopuri conectate la Wi-Fi, înlocuim modulul Ethernet cu modulul WPC300N de Wi-Fi.

Pentru switch-uri 2960, nu sunt slot-uri de module, deci nu se schimbă nimic.

Pentru routere 2911, vom adăuga un modul HWIC-2T în slot-ul de la dreapta, cu două porturi serial. Ar trebui și să punem un cover pe cele trei slot-uri goale.

Pentru Wi-Fi, router WRT300N.

### End-Devices

#### Laptop-ul SERVICE

Absolut nicio configurație.

Se va folosi pentru a accesa dispozitivele prin cablu consolă.

Când se configurează routerul Wi-Fi, va trebui conectat prin cablu straight-through la routerul Wi-Fi.
Nu se adaugă un modul WPC300N de conexiune Wi-Fi la laptopul SERVICE.

#### Configurarea unui PC

- Numele PC-ului va fi același ca sub-rețeaua în care e, i.e. in rețeaua CLUJ, PC-ul va fi numit CLUJ.
- Modulul CGE.
- Configurăm IP-ul static din IP Configuration, cu adresa IP calculată pentru host, cu subnet mask-ul și gateway-ul calculate pentru sub-rețeaua în care e hostul, și cu IP-ul serverului pentru DNS.
- Configurăm aplicația Email:
  - Your Name = numele hostului
  - email address = numele hostului @info.ro
  - Incoming + Outgoing mail server = IP-ul serverului
  - Username = numele hostului
  - Password = 123456

#### Configurarea unui PC pentru testare DHCP

Fără modul CGE (modulul deja existent e suficient), fără IP static, și fără configurație de email.

Când DHCP pe router a fost configurat, setăm IP-ul pe hostul de testare DHCP la modul DHCP, și verificăm că primește corect IP-ul.

Ar trebui să primească un IP imediat după hostul cu IP static, dacă au fost excluse corect adresele rezervate pentru switch-uri.

#### Configurarea unui Laptop Wi-Fi

- Nu se adaugă modul CGE ca la PC, ci modulul WPC300N.
- Vor avea email.
- IP-ul obținut prin DHCP, nu static ca la PC-uri.
- Se vor numi i.e. "WiFiBucurestiLaptop1", "WiFiBucurestiLaptop2", etc.
- Obținem adresele MAC prin `ipconfig /all` în Command Prompt, vor fi necesare pentru a seta un whitelist pe routerul Wi-Fi.
- Desktop > PC Wireless > Connect, refresh, conectare la rețeaua Wi-Fi, cu parola setată pe routerul Wi-Fi. DHCP va fi automat activat.

#### Server-ul

- Numit SERVER.
- Modulul CGE.
- Configurare de client email, nu doar server email, acela e alt pas, are și un client email.
- IP static, ultima adresă folosibilă din sub-rețeaua cu serverul.
- Din tab-ul Services:
  - HTTP va fi deja activat.
  - Nu se va activa DHCP, fiindcă DHCP va fi pe router, nu pe server.
  - Se activează DNS, și se adaugă un record A care asociază adresa info.ro cu IP-ul serverului.
  - SYSLOG va fi deja activat.
  - Email va fi deja activat, dar va trebui configurat:
    - Domain Name = info.ro
    - Pentru fiecare host, se va crea contul de email cu numele hostului, și parola 123456.
      Deci fiecare desktop PC (exceptând cel de testare DHCP), fiecare laptop, și serverul vor avea câte un cont de email.
  - FTP va fi deja activat. Trebuie creat un cont pentru fiecare host, la fel ca la email, cu permisiuni RWL (Read, Write, List).

### Dispozitive Intermediare (cu Cisco IOS)

Acestea sunt switch-urile și routerele. Multă configurație e comună între ele.

Nu e voie cu meniul help (`?`) în CLI, te dă afară dacă vede că ai folosit help.

#### Configurarea comună

- `clock set HH:MM:SS MONTH DAY YEAR`, ex. `clock set 12:00:00 1 Jan 2020` (singura comandă rulata din EXEC mode, fără `config terminal`)
- `hostname [nume, la fel ca în Packet Tracer]`
- `enable password ciscoenpa55` - deși `enable secret` va cauza `enable password` să fie ignorat
- `enable secret ciscosecpa55`
- `service password-encryption`
- `banner motd #mesaj#` - Se poate folosi orice caracter ca delimitator. Mesajul ar trebui să fie ceva ce interesează pe colegii de la muncă, i.e. "Până luna Iulie, schimbați parolele.".
    Nu un mesaj de genul "Accesul neautorizat este interzis", deși așa a zis la cursul Cisco, nu așa vrea Drăgan.
- Syslog:
  - `service timestamps log datetime msec`
  - `service timestamps debug datetime msec`
  - `logging host [IP-ul serverului]`
- `no cdp run` - Se dezactivează CDP, pentru securitate.
- `no ip domain-lookup` - Pentru a evita întârzieri când tastăm comenzi greșite.
- `ip domain-name info.ro`
- `crypto key generate rsa` - când cere mărimea, 2048
- `username Admin01 privilege 15 secret Admin01pa55`
- `ip ssh version 2`
- `line console 0`
  - `password ciscoconpa55`
  - `login`
  - `exec-timeout 15 15`
  - `logging synchronous`
- `line vty 0 15`
  - `password ciscovtypa55` - deși se va ignora din cauza `login local`
  - `login local`
  - `exec-timeout 10 10`
  - `logging synchronous`
  - `transport input ssh`
- Când terminăm de configurat, salvăm cu `copy running-config startup-config`.

#### Configurarea specifică switch-urilor

- `ip default-gateway [IP-ul gateway-ului de pe rețeaua switch-ului]`
- `interface vlan 1`
  - `ip address [IP-ul switch-ului] [subnet mask-ul switch-ului]`
  - `description DESCRIERE` - Fără delimitator, nu ca la banner. Ceva descriptiv.
  - `no shutdown`
- Se dezactivează toate porturile nefolosite.
  - Se poate folosi `interface range FastEthernet 0/1-24`, `shutdown`, pentru a dezactiva porturile rapid porturile FastEthernet.
    S-ar putea să fie nevoie de un port FastEthernet pentru hostul de testare DHCP, atunci doar acel port FastEthernet nu va fi dezactivat.
  - Se dezactivează porturile GigabitEthernet nefolosite, dacă sunt.

#### Configurarea specifică routere

Nu la oferta de autoconfigurare care apare prima dată în CLI.

- Nu se configurează interfața VLAN ca la switch-uri.
- Interfețele serial WAN și GigabitEthernet LAN (plus una GigabitEthernet WAN pentru Wi-Fi, dacă e cazul) se configurează:
  - `interface [nume interfață]`
  - `ip address [IP-ul interfeței] [subnet mask-ul interfeței]`
    - Dacă e un default gateway pe LAN, va fi prima adresă folosibilă din sub-rețea.
    - Dacă e un WAN cu două adrese folosibile, va fi fie prima, fie a doua adresă folosibilă.
  - `ip helper-address [cea mai apropiată interfață a routerului cu DHCP]` - Pentru forward cereri DHCP la routerul cu DHCP.
    - Nu trebuie pus IP-ul de pe LAN al routerului cu DHCP, ci interfața serială cea mai apropiată. Ar trebui să funcționeze DHCP chiar dacă nu este conectat switch-ul de pe LAN-ul routerului cu DHCP.
    - Helper address va fi configurat doar pe LAN-ul pe care routerul este default gateway, nu pe WAN-uri.
    - Rețeaua server nu va avea helper address, deoarece nimic acolo nu va folosi DHCP.
    - Rețeaua routerului cu DHCP nu are nevoie de helper address, fiindcă nu e nevoie de forward, routerul DHCP va primi direct cererile. Probabil routerul cu serverul va fi și routerul cu DHCP.
  - `description DESCRIERE` - Ceva descriptiv.
  - `no shutdown`
- Ar trebui să fie deja dezactivate toate porturile nefolosite, implicit.
- Securitate extra pentru routere:
  - `security passwords min-length 10`
  - `login block-for 50 attempts 3 within 15`
  - `banner login #Accesul neautorizat este strict interzis!#`
- Rute statice:
  - Pentru fiecare rețea care nu e direct conectată, se va adăuga o rută statică.
  - `ip route [network address] [subnet mask] [interfața WAN care duce în direcția rețelei]`
- DHCP (doar pe un router) - pentru fiecare rețea care are nevoie de DHCP:
  - `ip dhcp excluded-address [adresa default gateway] [adresa ultimului host cu IP static]`
    - De exemplu, într-o rețea 192.168.0.0/24 cu 126 de adrese folosibile, și un singur host, hostul va avea IP-ul 192.168.0.7, deci comanda va fi `ip excluded-address 192.168.0.1 192.168.0.7`.
  - `ip dhcp pool [nume rețea]`
    - `network [network address] [subnet mask]`
    - `default-router [adresa default gateway]`
    - `dns-server [adresa serverului DNS, aceeași pentru toate rețelele]`
    - `domain-name info.ro`

### Configurarea Routerului Wi-Fi

E complet diferită de cea a unui router normal.

Rețeaua Wi-Fi nu va avea spațiu alocat în rețeaua mare, doar WAN-ul are nevoie de spațiu. Rețeaua Wi-Fi va fi una separată, privată (192.168), și va folosi NAT pentru a conecta dispozitivele Wi-Fi la rețeaua mare.

Routerul Wi-Fi va avea o adresă statică pe acel WAN, și ca folosi NAT pentru a conecta dispozitivele Wi-Fi la rețeaua mare.

Implicit, IP-ul routerului Wi-Fi va fi `192.168.0.1`. Username/password vor fi admin/admin.

Se va configura prin pagina web a routerului, nu prin CLI.

Portul Internet este pentru uplink-ul la rețeaua mare, iar porturile Ethernet oferă conexiunea prin fir la rețeaua Wi-Fi (routerul Wi-Fi conține și un switch integrat pentru porturile Ethernet).

Trebuie conectat laptop-ul SERVICE prin Ethernet la routerul Wi-Fi (nu prin portul Internet!), configurat cu IP static, și conectat la pagina web a routerului.

Se folosește cross-over Ethernet pentru portul Internet.

Trebuie să fie cel puțin un laptop conectat la Wi-Fi, și cel puțin unul care nu se poate conecta, pentru a demonstra că filtrul MAC funcționează.

Pentru configurare laptopuri, vezi [Configurarea unui Laptop Wi-Fi](#configurarea-unui-laptop-wi-fi).

#### Setup > Internet Setup

- Internet Connection Type la Static IP.
- Internet IP Address și Subnet Mask la un IP pe WAN-ul care conectează routerul Wi-Fi la o sub-rețea din rețeaua mare, și masca corespunzătoare (255.255.255.252 pentru un WAN cu două adrese folosibile).
- Default Gateway la cealaltă adresă de pe WAN, adică adresa celuilalt router de pe WAN, care va conecta sub-rețeaua Wi-Fi la restul rețelei.
- DNS la IP-ul serverului DNS, ca la restul rețelelor.

#### Setup > Network Setup

- IP Address = 192.168.x.y (ceva aleatoriu, nu 0.1)
- Subnet Mask = 255.255.255.240 (14 useri)
- DHCP pornește de la N.A. + 1
- Static DNS - IP-ul serverului DNS

#### Wireless > Basic Wireless Settings

- Network Name (SSID) = WiFi + Numele rețelei la care e atașat, i.e. dacă avem Wi-Fi pe rețeaua BUCURESTI, SSID-ul va fi WiFiBUCURESTI.
- Standard Channel = 6 sau 11

#### Wireless > Wireless Security

- Security Mode = WPA2 Personal
- Passphrase = o parolă

#### Wireless > Wireless MAC Filter

Enabled, și se adaugă adresele MAC ale laptopurilor Wi-Fi obținute prin `ipconfig /all` în Command Prompt, în mod whitelist (Permit PCs listed below to access wireless network).

Vom lăsa cel puțin un laptop fără adresă MAC în whitelist, pentru a demonstra că filtrul funcționează.

## Testare

Ar trebui să testăm toată conectivitatea și serviciile pe care le-am configurat, pentru a găsi și repara probleme:

- `ping` între toate dispozitivele. Orice ar trebui să poată face ping la orice altceva
  - cu excepția laptopurilor din rețeaua Wi-Fi care sunt în spatele NAT-ului, acelea nu pot primi conexiuni din exterior, dar ar trebui să poată face ping la orice, atât timp cât ele inițiază conexiunea
  - routerul Wi-Fi nu va raspunde la pinguri pe WAN, deoarece e configurat să ignore ICMP care vine din exterior pe WAN. Ar trebui să răspundă la pinguri de la un laptop pe LAN-ul Wi-Fi.
  - `traceroute` / `tracert` (depinde dacă e Command Prompt sau Cisco IOS) poate ajuta la depanare dacă o conexiune nu merge.
- `ssh` în toate dispozitivele care au SSH activat (routere și switch-uri).
  - Switch-urile trebuie să poată primi SSH din afara LAN-ului lor (dacă a fost uitat `ip default-gateway` nu va merge din afara LAN-ului).
  - Trebuie să ceară parola userului Admin01 (nu parola ciscovtypa55).
  - Trebui să afișeze banner MOTD. Dacă e router, trebuie să fie și banner login.
- Conexiunea consolă trebuie să ceară parola de consolă, și enable trebuie să ceară parola de la enable secret.
- Cele șase servicii configurate trebuie să funcționeze:
  - Trebuie să se poată trimite și primi email între toate hosturile.
  - Trebuie să se poate accesa FTP-ul, cu upload și download, de pe toate hosturile.
  - DHCP trebuie să funcționeze din toate rețelele mai puțin cea cu serverul.
    - Trebuie ca hostul de testare DHCP să primească un IP imediat după hostul cu IP static, nu unul rezervat pentru switch-uri.
  - Trebuie să putem accesa info.ro de pe toate hosturile.
    - DNS
    - HTTP
  - Serverul trebuie să primească syslog de la toate dispozitivele intermediare.
