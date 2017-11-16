# Virtueller Bau eines Computers(1)

## Motivation

Mein Interesse am (virtuellen) Entwurf von elektrischen Schaltungen stammt ursprünglich von einem Handy-Spiel, welches entsprechende Möglichkeiten zur Verfügung stellte. Nach dem Entwurf kleinerer, eher aufgabenspezifischer Schaltungen schien die Lösung zu noch leistungsfähigeren Schaltnetzen in der Prozessorbauweise zu liegen. Anschließend folgte der Umstieg auf die Entwicklungsumgebung Logisim, mit der der Entwurf größerer Schaltungen stark vereinfacht wurde.
Mein Ziel der Arbeit war nicht der Nachbau eines bestehenden Prozessors, sondern das eigenständige Entwickeln unter berücksichtigung gebräuchlicher Konzepte.

## Arbeitsumgebung

Logisim ist eine Simula
## Grundlegende Funktionsweise
Ein Prozessor ist eine in der Regel frei programmierbare elektrische Schaltung, die Daten basierend auf den programmierten Algorythmen verarbeitet und diese anschließend an weitere Komponenten übergiebt oder Peripheriegeräte ansteuert(z.B. Bildschirm etc.). Neben der zahlreichen Verwendung als Mikrocontroller in eingebetteten Systemen(Waschmaschienen, CD-Spieler) stellt er in einem Computer als Hauptprozessor die zentrale Recheneinheit da (englisch: CPU = *Central Processing Unit*).

Grundsätzlich verknüpft man mit einem Prozessor Recheneinheiten, Speicherbausteine und diverse Kontrolllogik so miteinander, dass diese durch den Programmierer koordinierbar werden. So können komplexe Programme über mehrere Schritte hinweg abgearbeitet werden,die weit über einfache arithmetische Aufgaben hinaus reichen. Jeder Prozessor lässt sich dazu in Assemblersprache, einer sehr maschienennahen Sprache, programmieren (siehe Abschnitt #Programmierung)

### Funktioneller Überblick

#### ROM (Befehlsspeicher) und RAM (Datenspeicher)

Auf dem Befehlsspeicher befinden sich die auszuführenden Programme in Form einzelner Befehle. Daten, die durch Berechnungen oder Nutzereingaben anfallen, werden auf dem Datenspeicher abgelegt. Der Befehlsspeicher ist hier als *Festwertspeicher* konzipiert (*ROM* = *Read Only Memory*, freie Adressierung, jedoch kein Beschreiben möglich), während den Datenspeicher ein *Direktzugriffsspeicher/RAM* bildet (*RAM* = *Random-Access Memory*, freie Adressierung sowie lesen und schreiben möglich). Die bauliche Trennung von Daten- und Befehlsspeicher entspricht der *Harvard-Architektur*(siehe Abschnitt #Einordnung)

#### ALU (arithmetic logic unit)

Die zu deutsch *arithmetisch-logische Einheit* stellt das Rechenwerk des Prozessors da. Je nach Umfang lassen sich mit ihr sowohl arithmetische Operationen(z.B Grundrechenarten), logische Operationen(z.B. Konjunktion, Negation, Vergleichsoperation etc.) als auch Bitmanipulationen(z.B einzelne Bits/Bitgruppen verändern) durchführen. Weiterhin werden zusätzliche Informationen über die Berechnungen(z.B Auftreten eines Übertrags, Division durch 0 etc.) an die Statusregister weitergegeben.

#### Statusregister

Die Statusregister sind einzelne Flipflops, die bestimmte Zustände des Prozessors abspeichern. Wie erwähnt können entsprechende Informationen von der ALU, aber auch von anderen Komponenten wie dem Ein-und Ausgabecontroller stammen. Eine nähere Beschreibung erfogt im Abschnitt #Detailierte Übersicht#Statusregister.

#### Register

Alle Daten, die sich in unmittelbarer Bearbeitung befinden, werden für den schnellen Abruf in einzelnen Speichern, den sog. Registern zwischengespeichert. Es existieren neben den *GPRs* (*General Purpose Register*, universell nutzbar) auch Spezialregister wie einem Adress-Register(siehe Abschnitt #DÜ#Reg)

#### Steuerwerk

Das Steuerwerk ist für die Decodierung und Interpretation der Befehle zuständig. Dazu spricht es die betreffendden Bauteile über Kontrollleitungen an. In meiner Prozessorimplementierung existiert kein zentrales Steuerwerk, dagegen befindet sich die nötige Kontrolllogik direkt bei den zugehörigen Teilschaltungen.

### Bussysteme

Alle Teilschaltungen sind neben einzelnen Steuerleitungen über sog. Busse miteinander verknüpft. Ein *Bus* wird aus mehreren gekoppelten Bits gebildet, dadurch können größerer Zahlenwerte oder Adressen übermittelt werden. Folgende Busse existieren in meinem Prozessor:

**Befehlsbus:** ist in 4 Unterbusse aufgeteilt und übermittelt die Programmbefehle an die Steuerwerke der Bauteile

**Datenbus:** Auf ihm werden können jegliche Berechnungsdaten zu den Bauteilen transportiert werden(z.B das Ergebnis einer Addition)

**Adressbus:** Dieser dient zur Adressierung von RAM und ROM
Eine nähere Beschreibung erfogt im #Technischen Überblick

## Technischer Überblick

### Struktur der Maschienensprache

Zum detaillierten Verständnis der gesammten Schaltung ist es notwendig, die spezifische Maschienensprache des Prozessors grob zu kennen. Dieser teilt sich auf in: 
```
0000 0    0000    0000      0000000000
OPC       Drain   Source    immidiate value
```


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

Die bauliche Trennung von Daten- und Befehlsspeicher entspricht der Harvard-Architektur. In der Von-Neumann-Architektur dagegen liegen Daten und Befehle auf einem gemeinsamen Speicher vor. Letztere zeichnen sich durch eine hohe Universalität, weshalb fast alle modernen Hauptprozessoren dieser Gattung angehören. sind jedoch in ihrer Impementierung komplizierter. Erstere dagegen werden heutzutage in
 Vorteil ist, dass Befehle und benötigte Daten gleichzeitig statt hintereinander geladen und verarbeitet werden können.
