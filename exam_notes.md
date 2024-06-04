# Examen Retele

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

## Pașii

Pașii trebuie scriși în felul în care a zis Drăgan ca să se puncteze, fără nimic absent sau în plus.

Nu vom avea voie cu o ciornă de hârtie, cu materiale, sau cu programul de calculator de pe computer, doar cu programul Packet Tracer. Totul se scrie în note text în Packet Tracer.

Totul trebuie scris într-o singură notă, cu excepția dacă nu încape pe ecran totul, atunci vom împărți în mai multe note, dar nu mai multe decât e nevoie.

O mare parte din notă vine de la pașii 1, 2, și 3.
Drăgan a zis că dacă vede greșeli aici, oprește evaluarea!

### Pasul 1

Trebuie calculat NA, BA, și RA pentru rețeaua mare (sub-rețelele nu sunt relevante aici).

Se va scrie adresa IP dată în binar, se va scrie sub ea subnet mask-ul în binar, și apoi se va scrie rezultatul de AND binar, pentru a obține NA.

RA este range-ul de adrese folosibile/asignabile, adică de la NA+1 la BA-1.

BA se obține prin OR între NA și inversul subnet mask-ului. Nu e nevoie să se scrie OR-ul explicit ca la AND.

Apoi se va scrie în format zecimal cu prefix length: NA, BA și RA.

Exemplu - se dă aleatoriu 156.234.54.10/22, pasul 1 va fi:

```text
PAS 1

156.234.54.10/22

10011100.11101010.00110110.00001010
11111111.11111111.11111100.00000000
10011100.11101010.00110100.00000000

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
