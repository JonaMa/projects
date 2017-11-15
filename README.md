# Virtueller Bau eines Computers(1)

## Motivation

Mein Interesse am (virtuellen) Entwurf von elektrischen Schaltungen stammt ursprünglich von einem Handy-Spiel, welches entsprechende Möglichkeiten zur Verfügung stellte. Nach dem Entwurf kleinerer, eher aufgabenspezifischer Schaltungen schien die Lösung zu noch leistungsfähigeren Schaltnetzen in der Prozessorbauweise zu liegen. Anschließend folgte der Umstieg auf die Entwicklungsumgebung Logisim, mit der der Entwurf größerer Schaltungen stark vereinfacht wurde.
Mein Ziel der Arbeit war nicht der Nachbau eines bestehenden Prozessors, sondern das eigenständige Entwickeln unter berücksichtigung gebräuchlicher Konzepte.

## Arbeitsumgebung

Logisim ist eine Simula
## Grundlegende Funktionsweise
Ein Prozessor ist eine in der Regel 
Grundsätzlich verknüpft man mit einem Prozessor Recheneinheiten, Speicherbausteine und diverse Kontrolllogik so mit einander, dass   

## Die Teilschaltungen im Detail

### ROM

### RAM

### Register

### ALU

#### Addier-/Subtrahier-Werk

#### Multiplikationswerk

Die hier gebaute Form  ist nicht die schnellste, aber die einfachste Bauform eines Multiplikationswerks. Die Funktionweise ist an die schriftliche binäre Multiplikation angelehnt (teilweise auch andersherum dargestellt):
```
11101 * 11001   (29*25)
_____________        
        11101   x1
       00000    x0
      00000     x0
     11101      x1
+   11101       x1
_____________
   1011010101   (725)            
```
Die einzelnen Ziffern des 2. Faktors werden hierbei von links nach rechts mit dem 1. Faktor multipliziert, das Zwischenergebnis muss in jedem Schritt nach links verschoben werden. Schließlich werden alle Zwischenergebnisse addiert und bilden das Produkt. Das Verfahren ist im Binärsystem deutlich einfacher als im Dezimalsystem durchzuführen, da das Berechnen der Zwischenprodukte lediglich mit dem Faktor 1 oder 0 bewerkstelligt werden muss.

Umsetzung als Schaltung

Das Schaltnetz folgt dem gleichen Schema, nur müssen hier die Zwischenprodukte nach und nach zusammenaddiert werden. Das Berechnen des Zwischenprodukts ist gleichbedeutend mit dem "sperren" oder "setzen" des 1. Faktors. Dies geschieht über Selektoren, die jeweils über die Bits des 2. Faktors gesteuert werden. Auf jeder Stufe wird demnach entweder der 1. Faktor zum bisherigen Zwischenergebnis hinzuaddiert, oder aber lediglich eine 0 addiert. Genauo wie bei der schriftlichen Multiplikation ist jede Stufe (Addierwerk und Selektor) nach links verschoben. 
Anmerkung: Obiges Schaltnetz ist von unten nach oben zu betrachten. Halbaddierer (HA) finden im Gegensatz zu Volladdierern (FA) dann Einsatz, wenn lediglich 2 Bits zu verrechnen sind.
### Statusregister

## Befehlssatz

## Assemblerprogrammierung: Snake

## Einordnung
