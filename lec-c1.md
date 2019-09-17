% Einführung in C - Teil 1
% Martin Sulzmann

# Inhalt

* Motivation
* Hello World
* "Work flow": Editieren/compilieren/ausführen
* Argumentübergabe
* IO printf/scanf
* Einfache Datentypen und Ausdrücke
* Kontrollstrukturen
    * Bedingte Anweisung (Kurzschlussauswertung)
    * Schleifen (break und continue)
* Komplexe Datenstrukturen

# Motivation


## Wieso C/C++ (nicht?)

## C am Beispiel (quicksort)

~~~~{.c}

#include "stdio.h"

/*

Quicksort.

Idea: Split array (some totally ordered collection of values) into
       those that are lesser and greater than a certain element
       (commonly referred to as the pivot element).

      Quicksort the lesser and greater array.

      Concat results.


Example:

[3,2,5,1,4]


pick first element 3 as our pivat element.

lesser = [2,1]

greater = [5,4]


quicksort [2,1] => [1,2]

quicksort [5,4] => [4,5]


Then,

   quicksort [3,2,5,1,4] 

= (quicksort [2,1])  ++ [3] ++ (quicksort [5,4])

= [1,2] ++ [3] ++ [4,5]

= [1,2,3,4,5]

 */

void display(int x[], int low, int high) {

  printf("\n x[] low = %d high =%d \n", low, high);
  
  for(; low <= high; low++) {
    printf("%d ", x[low]);
  }
}


void swap(int* x, int* y) {
  int temp = *x;
  *x = *y;
  *y = temp;
}



void quicksort(int x[],int left, int right){
   int i, j, pivot, temp;

   if(left < right){
      pivot=left;
      i=left;
      j=right;

      /*
       1. Partition array 
         
           (a) 
           From left and right compare against pivot element.
           Adjust i (upper left frontier) and j (lower right frontier).
           Swap if frontiers haven't met yet.

           (b)
           Swap so that pivot element is placed in the middle.

       2. Recursively quicksort

           (a)
           All elements lesser (or equal) than pivot element.
           (b)
           All elements greater than pivot element.         

       */

      // (1a)
      while(i<j){
	while(x[i]<=x[pivot] && i<right) // (I1)
            i++;
	while(x[j]>x[pivot])            // (I2)
            j--;
         if(i<j){
	   swap(&x[i], &x[j]);
         }
      }

      // (1b)
      swap(&x[pivot], &x[j]);

      // (2a)
      quicksort(x,left,j-1);
      // (2b)
      quicksort(x,j+1,right);

   }
}

/*

Further comments.

(I1) The side condition "i<right" ensures that we stay
     within valid array bounds.
     We might reach the case that i = right.
     Access to x[right] is valid, so we can safely evaluate
     x[i] <= x[pivot] and then abort the while loop because
     condition "i<right" no longer holds.

(I2) 

*/



int main() {

  int x[] = {3,4,1,2,5};
  int l, r, l2, r2;

  display(x, 0, 4);
  quicksort(x, 0, 4);
  display(x, 0, 4);  
  
  return 1;
}
~~~~~~~~~~~


## Haskell im Vergleich 

~~~~~{.haskell}
quicksort [] = []
quicksort (p:xs) = (quicksort lesser) ++ [p] ++ (quicksort greater)
    where
        lesser = filter (< p) xs
        greater = filter (>= p) xs

~~~~~~~

## Zusammengefasst


C/C++

* Totale Kontrolle!

* Keine (wenig) Sicherheit (safety, correctness, ...)


Haskell

* Sehr viel Sicherheit!

* Weniger Kontrolle (lazy evaluation, GC, ...)
 

# Hello World

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h> // standard i/o lib

// Wird vom Betriebssystem gestartet
int main() { 
  printf("Hallo Welt! \n"); // vgl. java println() 

  return 0;  // Rueckgabewert Fehlercode/Status
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Work flow

* Program editieren (z.B. emacs, jeder andere
   Texteditor tut es auch)
* Compilieren und ausführen:

~~~~~~~~~~~~~~~~~~~~~~~~~
$ gcc.exe helloWord.c

$ ./a.exe
Hallo Welt!
~~~~~~~~~~~~~~~~~~~~~~~~~

Alternativ

~~~~~~~~~~~~~~~~~~~~~~~~~
$ gcc.exe -o helloWord.exe helloWord.c

$ ./helloWorld.exe
Hallo Welt!
~~~~~~~~~~~~~~~~~~~~~~~~~

# main Argumentübergabe

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>

// argc Anzahl der Aufrufargumente bei Programmstart
// argv Array der Argumente 
int main(int argc, char* argv[]) {
  int i = 0;
  while(argc >= 0) {
    printf("Argument %d = %s \n", i+1, argv[i]);
    i++;
    argc--;
  }
  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Beispiel

~~~~~~~~~~~~~~~~~~~~~~~~~
$ ./mainArgs.exe hi 5        
Argument 1 = ./mainArgs.exe
Argument 2 = hi 
Argument 3 = 5 
Argument 4 = (null) 
~~~~~~~~~~~~~~~~~~~~~~~~~

# IO printf

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>

int main() {
 printf ("Characters: %c %c \n", 'a', 65);
 printf ("Decimals: %d %ld\n", 1977, 650000L);
 printf ("float: %f \n", 3.1416);
 printf ("%s \n", "A string");
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

liefert

~~~~~~~~~~~~~~~~~~~~~~~~~
Characters: a A 
Decimals: 1977 650000
float: 3.141600 
A string 
~~~~~~~~~~~~~~~~~~~~~~~~~


# IO printf Zusammenfassung

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
 printf ("Decimals: %d %ld\n", 1977, 650000L);
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Erster Parameter: eine Zeichenkette (Literal mit Format-Steuertext) 
* Weitere Parameter: Variablen mit Typen gemäß Format-Steuerung
* Platzhalter im Format-Steuertext: 

~~~~~~~~~~~~~~~~~~~~~~~~~
%d    Dezimalzahl 
%f   Kommazahl  (float oder double)
~~~~~~~~~~~~~~~~~~~~~~~~~

* Sonderzeichen im Format-Steuertext: 

~~~~~~~~~~~~~~~~~~~~~~~~~
\n    new line, Zeilenvorschub 
\t    Tabulator 
~~~~~~~~~~~~~~~~~~~~~~~~~

* Mehr Info hier:


[http://www.cplusplus.com/reference/clibrary/cstdio/printf/](http://www.cplusplus.com/reference/clibrary/cstdio/printf/)



# IO scanf

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {
  char str [80];
  int i;

  printf ("Geben Sie Ihren Namen ein: ");
  scanf ("%s",str);  // Referenzuebergabe via Pointer
  printf ("Alter: ");
  scanf ("%d",&i);   // & Operator liefert Pointer auf i  
  printf ("%s ist %d Jahre alt.\n",str,i);
  printf ("Geben Sie ein hexadezimale Zahl ein: ");
  scanf ("%x",&i);  // Referenzuebergabe via Pointer
  printf ("You have entered %#x (%d).\n",i,i);
  
  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# IO scanf

Referenzübergabe via Pointer

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
  char str [80];
  int i;
  scanf ("%s",str);
  scanf ("%d",&i); 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Pointer = Adresse
* `str` Pointer auf erstes Element
* `&i` Pointer auf `i`

Mehr Info

[http://www.cplusplus.com/reference/clibrary/cstdio/scanf/](http://www.cplusplus.com/reference/clibrary/cstdio/scanf/)

# Einfache Datentypen: Char, Int und Float

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {
  // Ganzzahlig
  char c;  // 1 Byte
  int i;   // 2-4 Byte je nach Plattform
  // Gleitkomma
  float f;  // 4 Byte
  double d; // 8 Byte

  // Typoperatoren: short, long, unsigned
  short int i16;  // Immer 2 Byte
  long int i32;   // Immer 4 Byte
  unsigned long int u32; // Wertebereich 0 - 4294967295

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Überlauf und Genauigkeit

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
  int i;
  float f;
  i = 1294967295 - 3234;
  f = 0.1f + 0.2f;
  if (f == 0.3f) { ... }
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Überlauf bei `Int`
* Genauigkeit bei `Float`

Mehr Info


[http://floating-point-gui.de/](http://floating-point-gui.de/)

[http://realtimecollisiondetection.net/blog/?p=89](http://realtimecollisiondetection.net/blog/?p=89)


# Typedef

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>

typedef int km_hour;       // typedef alterTyp neuerType;
typedef short int int16;

int main () {
  int i=3;  // m/second
  km_hour j=4;
  i = j + i; // Unlogisch km/h und m/secs zu addieren
  
  int16 k=3;
  short int l;
  l = 2 + k;     // typedefs sind nur Abkuerzungen !

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


[http://en.wikipedia.org/wiki/Typedef](http://en.wikipedia.org/wiki/Typedef)


# Konstanten

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {
  int i;
  const int k = 3;
  const int j;      // zufaellige Konstante

  i = 1;
  i = 2;

  // j = 3;  error read-only variable
  // k = 3;  error read-only variable

  printf("i=%d, j=%d, k=%d \n",i,j,k);
  
  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# C hat kein Boolean

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {
  // C hat kein Boolean aber Integerausdruecke
  // haben eine Boolesche Interpretation
  int b=1;
  if(b != 0) 
    printf("true"); 
  else 
    printf("false");

  b=0;
  if(b)  // Integerausdruck ungleich 0 ist true
    printf("true"); 
  else  // Integerausdruck gleich 0 ist false
    printf("false"); 

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Globale und lokale Variablen

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>

int i;    // globale Variablen
char c;
float f;

int main () {
  char i = 'a';  // lokale Variablen im scope
  int c = 3;     // der main Funktion
  float d = 1.1;

  f = d + 1.9;

  printf("i=%c, c=%d, f=%f \n",i,c,f);
  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Arithmetische Ausdrücke

~~~~~~~~~~~~~~~~~~~~~~~~~~~
 3 + 5   Addition
 3 - 5   Subtraktion
 3 * 5   Multiplikation
 3 / 5   Division
 3 % 5   Modulofunktion

 -3      Negation
~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Operatoren auch mit Zuweisung kombinierbar 

~~~~~~~~~~~~~~~~~~~~~~~~~
  +=, -=, *=, /= 
~~~~~~~~~~~~~~~~~~~~~~~~~


* Rangfolge wie gewohnt

~~~~~~~~~~~~~~~~~~~~~~~~~
 3 * 5 - 3
~~~~~~~~~~~~~~~~~~~~~~~~~


# Automatische Typkonvertierungen

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {

  // Bei Zuweisung konvertiere vom 
  // einfacheren zum komplizierteren Typ: 
  // char -> short int -> long int -> float -> double 
  float x = 2;

  // Umgekehrt kann zu Datenverlust fuehren
  int i = -2.3;
  printf("-2.3 und %d \n", i);
  // -2.3 und -2 

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Konvertierung in (Teil)ausdrücken

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {

  // Konvertierung in Ausdruecken. Vorsicht!
  float f1 = 18 / 10 * 1.5; 
  float f2 = 18.0 / 10.0 * 1.5;
  printf("f1=%f f2=%f \n",f1,f2);
  // f1=1.500000 f2=2.700000

  // Jeder Teilausdruck wird fuer sich betrachtet, nur so
  // praezise wie noetig aufgrund der beteiligten Typen.
  // 18 / 10 --> 1 

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Explizite Typkonvertierung (Cast)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {

  float x = 340.2;
  int i = (int)x;  // erzwinge Typkonvertierung
  printf("i=%d \n",i);

  char c1 = (char)i;
  char c2 = (int)x; // implizite automatische Konvertierung
  printf("c1=%c c2=%c \n",c1,c2);

  float f1 = (float)(18) / (float)(10) * 1.5; 
  float f2 = 18.0 / 10.0 * 1.5;
  printf("f1=%f f2=%f \n",f1,f2);

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Logische Ausdrücke

~~~~~~~~~~~~~~~~~~~~~~~~~
    &&    AND      if (i>=0 && i<6) { } 
    ||    OR       if (i<0 ||  i>=6) { } 
    !     NOT      if (!i) { }
~~~~~~~~~~~~~~~~~~~~~~~~~

Vergleichsoperatoren sind Boolesche Werte: 

~~~~~~~~~~~~~~~~~~~~~~~~~
    <, >, ==, !=, <=, >=
~~~~~~~~~~~~~~~~~~~~~~~~~

# Bit-Operationen

~~~~~~~~~~~~~~~~~~~~~~~~~
    &     bitweises AND 
    |     bitweises OR 
    ^     bitweise XOR (exklusiv oder) 
    ~     bitweise Komplement (Negation)
    <<n   bitweise links um n Positionen schieben 
          (0 nachschieben) 
    >>n   bitweise rechts um n Positionen schieben 
          (0 nachschieben)
~~~~~~~~~~~~~~~~~~~~~~~~~

# Bit-Operationen Beispiele

* bitweises AND/OR

.           B7 B6 B5 B4 B3 B2 B1 B0
----------- -- -- -- -- -- -- -- --
 x           0  1  1  1  0  0  0  1
 y           0  0  1  1  1  0  0  1
 x&y         0  0  1  1  0  0  0  1
 x|y         0  1  1  1  1  0  0  1


* bitweises links schieben

.     B7 B6 B5 B4 B3 B2 B1 B0
----- -- -- -- -- -- -- -- -- 
x      0  1  1  1  0  0  0  1    
x<<2   1  1  0  0  0  1  0  0

  

   
# Bit-Operationen Anwendungen

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {
  unsigned short int x = 8; // 16bit Hardwareregister
  int i = 3;            // Bitposition

  // Pruefen, ob Bit i gesetzt ist: 
  if (x & (1<<i)) {printf("bit %d in x gesetzt\n",i);};
  // Setzen von Bit i ohne Veraenderung der uebrigen Bits:
  x = x |(1<<i); 
  // Ruecksetzen von Bit i ohne Veraenderung:
   x = x & ~(1<<i); 
   // Negieren ("kippen", "toggeln") von Bit i 
   // ohne Veraenderung:
   x = x ^(1<<i); 

   return 0;
} 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Bedingte Anweisungen

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {

  if (i<10) { // If Anweisung
     printf("i kleiner 10! \n");
  } else {
      printf("i groesser 9! \n");
  }
  switch (i) { // Fallunterscheidung
        case 1: printf("Fall 1\n");
                break;
        case 5: printf("Fall 5\n");
                break;
        default: printf("restliche Faelle\n");
  }
  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## Kurzschlussauswertung ("short-circuit evaluation")

Betrachte folgendes Programmfragment.

~~~~~~{.c}
if(n !=0 && m / n > 0) { ... }
~~~~~~~~~

Die Reihenfolge der Operanden ist wichtig!

Falls `n !=0' falsch, wird die folgenden Operanden nicht mehr ausgewertet.
Sprich, ein bedingter Ausdruck mit einer Sequenz von mit `&&`
verketteten Operanden entspricht einer Programmsequenz von verschachtelten
bedingten Ausdrücken.

Obiges Programmfragment entspricht.

~~~~~~{.c}
if(n !=0) {
  if(m / n > 0) { ... }
}
~~~~~~~~~


Eine ähnliche "Kurzschlussausertungs Regel" gilt für eine mit `||` verkettete
Sequenz von Operanden. In diesem Fall bricht die Auswertung ab (wird kurzgeschlossen),
sobald einer der Operanden wahr ist.



# Bedingter Ausdruck

~~~~~~~~~~~~~~~~~~~~~~~~~
cond ? thenExp : elseExp
~~~~~~~~~~~~~~~~~~~~~~~~~


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {
  int a=1,b=2,z;

  if (a>b) 
    z=a;
  else z=b;

  z = (a > b) ? a : b;    // z = max(a, b) 

  printf("a=%d, b=%d, z=%d \n",a,b,z);  
  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Schleifen

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () {
    int i;
    for (i=0; i<5; i++) { // For Schleife
        printf("Hallo Welt for %d \n",i);
    }
    i = 0;
    while (i<5) { // While Schleife
        printf("Hallo Welt while %d \n",i);
        i++;
    }
    i = 0;
    do { // Do-While Schleife
        printf("Hallo Welt do-while %d \n",i);
        i++;
    } while (i<5);

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

## `break` und `continue`


`break`: Abbruch und Ausstieg aus der Schleife.

`continue`: Abbruch und Fortführung der Schleife.

Betrachte folgendes Beispiel.

~~~~~~~~~{.c}
#include <stdio.h>

int main() {
  int i = 0;

  while (1) {

    if(i >= 40) {
      break;
    }

    i++;

    if(i >= 30) {
      continue;
    }

    printf("\n i = %d", i);

  }

  printf("\n Ende: i = %d", i);

  return 0;
}
~~~~~~~~~~~~~~~~


# Enumerationen 

*Mengen von endlichen Elementen*

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
enum Wochentag {
  Mo=0,
  Di=1,
  Mi=2,
  Do=3,
  Fr=4,
  Sa=5,
  So=6
};

enum Wochentag wt = Mo;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* Abbildung von Bezeichner auf Integer Werte
* Abbildung optional, Compiler verwendet aufsteigende Nummerierung
* `Wochentag` beschreibt eine Menge von Bezeichnern
* `wt` enthält eines der Elemente in der Menge


# Enumerationen Beispiel

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
int main () { 
  enum Wochentag wt = Mo;
  switch(wt) {
   case Mo: printf("Montag \n");
            break;
    default: printf("andere Tage \n");
  };

}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Enumerationen in C versus C++

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
enum Wochentag wt = 2;
int i = Do;
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Schwache Typisierung
* Obiges Programm wird ohne Warnung akzeptiert
* Starke Typisierung in C++

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
  enum Wochentag w2 = 5; // ok in C 
  enum Wochentag w2 = Wochentag(5); // ok in C++
  Wochentag wt2 = Wochentag(4); // ok in C++ 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Vergleich enums in C und C++

[http://www.cprogramming.com/tutorial/c-vs-c++.html](http://www.cprogramming.com/tutorial/c-vs-c++.html)

# Strukturen 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
struct position {
  int x;
  float y;
};

int main () { 
  struct position p = {1,3.0};                 // Initialisierung
  printf("position (%d,fd) \n",p.x,p.y);       // Zugriff
 return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


* "Primitive öffentliche Klasse ohne Vererbung und Methoden"
    * In C++, "struct = public class"
* Zugriff via "Dot Notation"


<!-- http://www.thegeekstuff.com/2011/12/c-arrays/ -->

# Arrays

* Sequenz von Elementen vom gleichen Typ
* Array mit 4 Elementen

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+===================================================+
| elem1     |  elem2      | elem3      | elem4      |
+===================================================+
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* `int iArr[4]`
    * Array mit 4 `int` Elementen
    * Zugriff auf Elemente via `iArr[index]` 
        * Erstes Element `iArr[0]`
        * Letztes Element `iArr[3]`

# Initialisierung von Arrays

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
  int b[10];
  int i;
  for(i=0; i<10; i++) b[i] = i;

  int a[] = {0,1,2,3,4,5,6,7,8,9};

  char s1[] = {'c','o','d','e','\0'};

  char s2[] = "code";
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Initialisierung jedes Elements einzeln
* Initialisierung gesamtes Array bei Deklaration (Länge inferriert)
* String = Array von Zeichen
* Das Ende des Strings wir gekennzeichnet durch
 das Nullterminator Zeichen 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
`\0`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* `s2` hat Länge 5!

# Array Zugriff ohne Bereichsüberprüfung

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () { 
  int a[10] = {0,1,2,3,4,5,6,7,8,9}; // Initialisierung
  int i;

  for(i=0; i<11; i++) {
    printf("a[%d]=%d ",i,a[i]); 
    // Keine Bereichsueberpruefung!
  }
  printf("\n");

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Kein (Laufzeit)Check ob in `a[i]` der Index `i` innerhalb der Arraygrenzen
  liegt

# Arrays und Strukturen

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>

struct position {
  int x;
  float y;
};

int main () { 
  struct position ps[2] = { {2,4.0}, {1,3.0} };
  int i;

  for(i=0; i<2; i++) {
    printf("ps[%d].x=%d ",i,ps[i].x); 
  }

 return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Mehrdimensionale Arrays

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
#include <stdio.h>
int main () { 
  int a[3][2] = { {0,1},{2,3},{4,5}}; // Initialisierung
  // Oder {0,1,2,3,4,5}
  // sprich hintereinander im Speicher
  int i,j;

  for(i=0; i<3; i++) {
    for(j=0; j<2; j++)
      printf("a[%d,%d]=%d ",i,j,a[i][j]); 
    printf("\n");
  }

  return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* a[Reihe][Spalte]
* `int b[3][4][5]` ...



# Typbezeichner ("Type Qualifier")

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
signed, unsigned, long, short, long long, const, 
volatile, static, auto, extern, register
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Speicherklassen: `static`, `auto`, `extern`, `register`

* Konstante: `const`

* Vorzeichen: `signed`, `unsigned`

* Genaugigkeit: `long`, `short`, `long long`

* `volatile` verhindert Optimierung (z.B. Speicherung Variable in Register)


# Varianten 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.c}
union u {
  int x;
  float y;
};
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Alle Komponenten benutzen die gleiche Speicherzelle.
* Typische Anwendung:
    * Wenn Speicher knapp ist (zuerst benutze int danach float)
    * Verschieden Interpretation von Bitmustern (Typkonvertierungen)



# Zusammenfassung: C Teil 1

* main Programm
* IO (printf, scanf)
* Einfache Datentypen (char, int, float, double), const und typedef
* Arithmetische/Logische Ausdrücke und Bitoperationen
* Überlauf und Genauigkeit
* Typkonvertierungen (automatisch, cast)
* Kontrollstrukturen (Bedingte Anweisungen und Schleifen, bedingter Ausdruck)
* Komplexe Datenstrukturen

