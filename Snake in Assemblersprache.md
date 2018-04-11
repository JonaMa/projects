# Programmierung von Snake in Assemblersprache

## Inhaltsverzeichnis

- [Einleitung](#1)

- [Snake](#2)

  - [Programmablaufplan](#2.1)

  - [Assemblerprogramm](#2.2)

- [Erweiterungsmöglichkeiten](#3)

## Einleitung<a name="1"></a>

Ziel des 2. Arbeitsabschnittes war die Programmierung des Spieleklassikers Snake auf meinem Prozessor. In dem Spiel bewegt man eine Schlange über ein Spielfeld und versucht, möglichts viele Punkte ("Pixfeed") zu "fressen", die eine Erhöhung der Wurmlänge zu folge haben. Dabei darf der Wurm sich selber oder andere Hindernisse nicht berühren. 

Besonders in der Anfangszeit befasste ich mich weiter mit den Rechenwerken, welche im nächsten Teil vorgestellt werden. Außerdem arbeitete ich ein Konzept für die Implementierung einer Interrupt-Schaltung im Prozessor aus. Das dazu notwendige System für das Konflikt- und Prioritätsmanegament stellte sich jedoch als recht komliziert heraus, weshalb das Projekt vorläufig beendet wurde. Snake konnte dadurch nicht in dem erwarteten Funktionsumfang fertiggestellt werden (siehe [Erweiterungsmöglichkeiten](#3)).

## Snake<a name="2"></a>

In der vorigen Arbeit wurde eine Testversion von Snake vorgestellt, bei der jedoch jeder Pixel der Schlange neu berechnet wurde. Um bei der vergleichsweise langsamen Simulationsgeschwindigkeit des Prozessors von ca. 80-120 Hz ein adäquates Spielerlebnis zu schaffen, berechnet der neue Algorithmus nur den Kopf- und Endpixel neu. Dafür wird der der Eintrag des Endpixes stets mit dem neuen Kopfpixel überschrieben, der sich dadurch nicht mehr an einer festen Speicheradresse der RAM befindet sondern durch alle benutzten Adressen rotiert. Bei einer Erhöhung der Wurmlänge müssen gegebenenfalls alle Einträge über dem Kopfpixel verschoben werden, um die Reihenfolge zu wahren. 

Falls die Schlange auf einen gesetzten Pixel trifft, wird geprüft, ob es sich dabei um den Pixfeed handelt. Ist dies der Fall, wird die Schlange verlängert und ein neuer zufälliger Pixfeed auf einem nicht gesetzten Pixel ausgegeben. Wenn nicht, muss die Schlange in sich selbst gelaufen sein und das Spiel wird beendet. Gesteuert wird die Schlange über die Tasten w-a-s-d. Sie kann auch aus dem Spielfeld herausbewegt werden und erscheint in diesem Fall am gegenüberliegenden Rand. Weitere Informationen können dem Programmablaufpan und dem kommentierten Programm entnommen werden.  

![image](https://user-images.githubusercontent.com/31915930/36232751-47aafb2e-11e3-11e8-98b5-9839d7ebaf21.png)

### Programmablaufplan (PAP)<a name="2.1"></a>

Da das Programmieren in Assemblersprache schnell unübersichtlich wurde, erstellte ich mit mit PapDesigner vorab einen groben PAP, nach welchem ich das Spiel programmierte. 

![image](https://user-images.githubusercontent.com/31915930/36232327-aa944b0c-11e1-11e8-94c6-d0355de4ba2c.png)

### Assemblerprogramm<a name="2.2"></a>

Für die Erläuterung meiner Assemblersprache sei auf meine vorige Dokumentation _Virtueller Bau eines Computers in Logisim (erster Teil)"_ in den Abschnitten #Technischer Überblick ##Struktur des Befehlssatzes und #Assemblerprogrammierung hinzuweisen.

```
spi 0 dv 66       } Setzen der Startpixel 
spi 0 dv 98
spi 0 dv 130
movi 0 ram dv 66  } Laden in Datenspeicher
movi 0 ram dv 98
mov 0 ram dv 130

mov 0 cx dv 2     #"Wurmlänge"-1
mov 0 ex dv 528   #erster Pixfeed
spi 0 ex
mov 0 p dv 2      #"Kopfposition" in P gespeichert, um unmittelbaren Zugriff auf den Kopf- und Endpixel zu haben

jmf 1 key $1      $1 #Taste drücken um zu starten

===============   } Hauptschleife
===============

mov 0 bx ram      $2 #Kopfpixel nach BX zur Bearbeitung
jmf 1 key $3      #Einlesen des IPBs überspringen, wenn kein Eingabe vorhanden

mov 0 dx ipb      #Einlesen

mov 0 ax dv "w"   $3  } Prüfen von DX ("Bewegungsrichtung") auf 4 mögliche Zustände
cmp 0 ax dx
jmt 1 eq $4

mov 0 ax dv "a"
cmp 0 ax dx
jmt 1 eq $5

mov 0 ax dv "s"
cmp 0 ax dx
jmt 1 eq $6

mov 0 ax dv "d" 
---------------
+x bx bx 0        #"d"  } Berechnung des neuen Kopfpixels basierend auf "Bewegungsrichtung"
jmp 1 0 $7
+y bx bx 0        $4 #"w"
jmp 1 0 $7
-x bx bx 0        $5 #"a"
jmp 1 0 $7
-y bx bx 0        $6 #"s"

===============

tpi 0 bx          $7  #Kopfpixel bereits gesetzt?
jmt 1 pix $10

---------------
cmp 0 cx p        #"Kopfposition" == "Wurmlänge"?
jmt 1 eq $8

inc p p 0         #"Kopfposition"+1
jmp 1 0 $9
mov 0 p dv 0      $8 #"Kopfposition"=0

clp 0 ram         $9 #Löschen des Endpixels
jmp 1 0 $14

===============   } Folgender Abschnitt: Vorgehen beim Treffen auf ein gesetztes Pixel

cmp 0 bx ex       $10	#Kopfpixel == Pixfeed?
jmf 1 eq $16      #wenn nicht, Spiel beenden

---------------   } Verschieben aller Einträge über Kopfposition nach rechts (RAM)
inc p p 0
mov 0 ax p

movi 0 stk ram    $11 } Auslesen in Schleifenform
cmp 0 p cx
jmf 1 gr $11

movd 0 ram stk    $12 } Versetztes Zurückspeichern in Schleifenform
cmp 0 p ax
jmf 1 eq $12
---------------

inc cx cx 0       #Erhöhung "Wurmlänge"
mov 0 ex rg       $13  } Prüfen und Setzten eines neuen Pixfeed
jmt 1 pix $13
spi 0 ex			

===============

mov 0 ram bx      $14 #Speichern und setzen des neuen Kopfpixels
spi 0 bx 

jmt 1 rtc $15     $15 #Warten auf Realtimeclock (zur Kompensation verschiedener Simulationsgeschwindigkeiten)

jmp 1 0 $2        #Zum Anfang der Hauptschleife springen

===============
===============

res ipb           $16  } Spielende
jmt 1 key $17     $17 #Auf Nutzereingabe warten
res all           #Prozessor Zurücksetzten
```

## Erweiterungsmöglichkeiten<a name="3"></a>

Das Programm ist in seinem Funktionsumfang noch erweiterungsfähig. Da es Pixel, die nicht dem Pixfeed entsprechen, nach dem Ausschlussprinzip erkennt, können vor Beginn der Hauptschleife beliebige Pixel als Hindernis in das Spielfeld gesetzt werden. Die Art des Hindernisses könnte auch im Vorfeld durch den Spieler ausgewählt werden. Möglich wäre zum Beispiel das Spielen mit, ohne, oder mit unterbrochener Bande, aber auch ein Labyrinth-ähnlicher Spielplan. Das Setzen des neuen Pixfeeds erfolgt ebenfalls in Berücksichtigung der Hindernisse.
Desweiteren wäre die Ausgabe der Punktzahl nach Ende des Spiels wünschenswert. Sie kann durch "Wurmlänge"-2(Ausgangslänge) berechnet werden und muss vor der Ausgabe in einzelne dezimale Ziffern umgewandelt werden. Die Umwandlung kann entweder durch eine zusätzliche Funktion (Software) oder eine neue ALU-Einheit (Hardware) bewerkstelligt werden. Für die Ausgabe der Ziffern wird eine weitere Funktion benötigt, die nach Berechnung der entsprechenden Sprungadresse auf einen Datenabschnitt im Programmspeicher zugreift, in dem das Layout aller Ziffern und Buchstaben (3x5 Pixel) gespeichert ist. Durch den *SPS*-Befehl können die Zeichen anschließend frei auf dem Bildschirm positioniert werden. Auch das Ausgeben von Texten wie "Game Over" kann auf diese Weise realisiert werden.
