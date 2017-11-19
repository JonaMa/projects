# Virtueller Bau eines Computers(1)

## Motivation

Mein Interesse am (virtuellen) Entwurf von elektrischen Schaltungen stammt ursprünglich von einem Handy-Spiel, welches entsprechende Möglichkeiten zur Verfügung stellte. Nach dem Entwurf kleinerer, eher aufgabenspezifischer Schaltungen schien die Lösung zu noch leistungsfähigeren Schaltnetzen in der Prozessorbauweise zu liegen. Anschließend folgte der Umstieg auf die Entwicklungsumgebung Logisim, mit der der Entwurf größerer Schaltungen stark vereinfacht wurde. 
Mein Ziel der Arbeit war nicht der Nachbau eines bestehenden Prozessors, sondern das eigenständige Entwickeln unter berücksichtigung gebräuchlicher Konzepte. Durch das Vorhandensein einer Ein- und Ausgabe hat das Projekt den Status eines vollwertigen Computers.

## Arbeitsumgebung

Logisim ist ein Logiksimulator, der dem Nutzer das Entwerfen und Simulieren von logischen Schaltungen ermöglicht. Angewendet wird das Programm häufig in Universitäten zu Vorlesungs- und Lehrzwecken. Neben den einfachen Grundbausteinen wie Gatter und Flipflops verfügt Logisim auch über komplexere Bausteine wie Auswahlschaltungen, Rechenwerke oder Bauteile zur Ein-und Ausgabe. Die wichtigsten benutzten Komponenten sind auf folgendem Bild zu sehen:
 
## Grundlegende Funktionsweise
Ein Prozessor ist eine in der Regel frei programmierbare elektrische Schaltung, die Daten basierend auf den programmierten Algorythmen verarbeitet und diese anschließend an weitere Komponenten übergiebt oder Peripheriegeräte ansteuert(z.B. Bildschirm etc.). Neben der zahlreichen Verwendung als Mikrocontroller in eingebetteten Systemen(Waschmaschienen, CD-Spieler) stellt er in einem Computer als Hauptprozessor die zentrale Recheneinheit da (englisch: CPU = *Central Processing Unit*).

Grundsätzlich verknüpft man mit einem Prozessor Recheneinheiten, Speicherbausteine und diverse Kontrolllogik so miteinander, dass diese durch den Programmierer koordinierbar werden. So können komplexe Programme über mehrere Schritte hinweg abgearbeitet werden,die weit über einfache arithmetische Aufgaben hinaus reichen. Jeder Prozessor lässt sich dazu in Assemblersprache, einer sehr maschienennahen Sprache, programmieren (siehe Abschnitt #Programmierung)

### Funktioneller Überblick

![main](https://user-images.githubusercontent.com/31915930/32954671-0dd3043e-cbb4-11e7-8f57-0f6844a8839f.PNG)
Der Prozessor bildet den gesammten oberen Teil. Bildschirm und Eingabe gehören streng genommen nicht dazu.
#### ROM (Befehlsspeicher) und RAM (Datenspeicher)

Auf dem Befehlsspeicher befinden sich die auszuführenden Programme in Form einzelner Befehle. Daten, die durch Berechnungen oder Nutzereingaben anfallen, werden auf dem Datenspeicher abgelegt. Der Befehlsspeicher ist hier als *Festwertspeicher* konzipiert (*ROM* = *Read Only Memory*, freie Adressierung, jedoch kein Beschreiben möglich), während den Datenspeicher ein *Direktzugriffsspeicher/RAM* bildet (*RAM* = *Random-Access Memory*, freie Adressierung sowie lesen und schreiben möglich). Die bauliche Trennung von Daten- und Befehlsspeicher entspricht der *Harvard-Architektur*(siehe Abschnitt #Einordnung)

#### ALU (arithmetic logic unit)

Die zu deutsch *arithmetisch-logische Einheit* stellt das Rechenwerk des Prozessors da. Je nach Umfang lassen sich mit ihr sowohl arithmetische Operationen(z.B Grundrechenarten), logische Operationen(z.B. Konjunktion, Negation, Vergleichsoperation etc.) als auch Bitmanipulationen(z.B einzelne Bits/Bitgruppen verändern) durchführen. Weiterhin werden zusätzliche Informationen über die Berechnungen(z.B Auftreten eines Übertrags, Division durch 0 etc.) an die Statusregister weitergegeben.

#### Statusregister/Flagregister

Die Statusregister oder kurz Flags sind einzelne Flipflops, die bestimmte Zustände des Prozessors abspeichern. Wie erwähnt können entsprechende Informationen von der ALU, aber auch von anderen Komponenten wie dem Ein-und Ausgabecontroller stammen. Sinn der Statusregister ist die Ausführung bedingter Sprungbefehle. Dabei springt der Prozessor unter einer bestimmten Bedingung zu einer anderen Programmadresse.Eine nähere Beschreibung erfogt im Abschnitt #Detailierte Übersicht#Statusregister.

#### Register

Alle Daten, die sich in unmittelbarer Bearbeitung befinden, werden für den schnellen Abruf in einzelnen Speichern, den sog. Registern zwischengespeichert. Es existieren neben den *GPRs* (*General Purpose Register*, universell nutzbar) auch Spezialregister wie einem Adress-Register(siehe Abschnitt #DÜ#Reg)

#### Stack

Der Stack oder zu deutsch Stapelspeicher funktioniert nach dem LIFO-Prinzip(Last In – First Out, d.h. die Daten, die als letztes eingespeichert wurden, können beim Lesen zuerst wieder ausgegeben werden. Der Stack kann zum Beispiel dazu verwendet werden, Register freizuräumen, wenn kurzeitig ein Unterprogramm ausgeführt werden muss. In diesem Prozessor findet sich der Stack nahe der ROM.

#### Eingabecontroller

Die Nutzereingaben werden in einem Puffer zwischengespeichert. Der Controller steuert das Auslesen des Puffers, wandelt die Eingaben um und gibt sie an den Computer weiter.

#### Framebuffer

Der Framebuffer ist ein Speicher, der eine digitale Kopie des Bildschirms beinhaltet. Notwendig ist dieser, um das bestehende Bild zwischen den Bearbeitungsphasen zu erhalten. 

#### Steuerwerk

Das Steuerwerk ist für die Decodierung und Interpretation der Befehle zuständig. Dazu spricht es die betreffendden Bauteile über Kontrollleitungen an. In meiner Prozessorimplementierung existiert kein zentrales Steuerwerk, dagegen befindet sich die nötige Kontrolllogik direkt bei den zugehörigen Teilschaltungen.

#### Bussysteme

Alle Teilschaltungen sind neben einzelnen Steuerleitungen über sog. Busse miteinander verknüpft. Ein *Bus* wird aus mehreren gekoppelten Bits gebildet, dadurch können größerer Zahlenwerte oder Adressen übermittelt werden. Folgende Busse existieren in meinem Prozessor:

**Befehlsbus:** ist in 4 Unterbusse aufgeteilt und übermittelt die Programmbefehle an die Steuerwerke der Bauteile

**Datenbus:** Auf ihm findet der gesammte Datenaustausch zwischen den Bauteilen statt. Die Besonderheit des Busses liegt in seiner bidirektionalen Nutzung: Daten können von einem beliebigen Bauteil in alle Richtung zu anderen Bauteilen transferiert werden. Alle anderen Busse sind unidirektional konzipiert und besitzen damit eine feste Datenquelle.

**Adressbus:** Dieser dient zur Adressierung von RAM und ROM
Eine nähere Beschreibung erfogt im #Technischen Überblick

## Technischer Überblick

### Struktur des Befehlssatzes

Wie erwähnt werden die 
Zum detaillierten Verständnis der gesammten Schaltung ist es notwendig, die spezifische Befehlssatzstruktur des Prozessors zu kennen. Mit dem Befehlssatz wird die Gesamtheit aller gültigen Maschienen-Befehle bezeichnet. Die einzelnen Befehle aus dem Befehlssatz werden in dem 23-Bit breiten Befehlsbus übermittelt, wobei dieser funktionell in mehrere Bitgruppen gegliedert ist, wie im Folgenden zu sehen:
```
0000 0       0000    0000      0000000000
Opcode+ACB   Drain   Source    immidiate value
```
**Opcode:** in diesem wird der Hauptbefehl angegeben(z.B "Verschiebe einen Wert")

**Drain und Source/Dr,So:** Fast alle Speicher im Prozessor lassen sich entweder als *Senke*(Drain) oder *Quelle*(Source)ansprechen. Mit beiden Werten kann der Opcode wie im folgenden Beispiel weiter konkretisiert werden: "Verschiebe nach Register A den Wert von Register B". Register A ist in diesem Fall die Senke, Register B die Quelle (Es wird in der Formulierung immer zuerst die Senke genannt). Der Datentransfer findet über den Datenbus statt: Der Wert von Register B wird auf den Datenbus gelegt, während in Register A der Wert vom Datenbus gespeichert wird. Bei einem ALU-Befehl wird das Ergebnis über den Datenbus zurückgeschrieben, daher sind 2 Direktbusse für die Operanden A und B notwendig, die aus den Registern gewählt werden können.

**Immediate value/ImV:** In vielen Fällen muss der Programmierer feste Werte, Adressen oder Pixelkoordinaten angeben können. Diese werden in den letzten 10 Bits des Befehlsbus übermittelt. *immediate value* kann als Quelle angesprochen werden und wird in diesem Fall auf den Datenbus gelegt. Damit wäre die Anweisung "Schreibe den Wert X in das Register A" möglich. Weiterhin werden die Bits bei bestimmten Befehlen für die Übergabe zusätzlicher Argumente verwendet (beim ALU-Befehl: Auswahl der Operation und optional zweiter Quelle, beim Reset-Befehl den zu löschenden Speicher).

**Adressbus Controll Bit/ACB:** Das letzte Bit des Opcodes ist für eine Spezialfunktion reserviert. Ein Setzen des Bits bewirkt, das *immediate value* auf den Adressbus gelegt wird. Bei nicht gesetztem Bit wird als Quelle fur den Adressbus das Adressregister ausgewählt. Der Programmierer kann dadurch direkt aus bestimmten Adressen der RAM speichern bzw laden oder explizite Sprungadressen für die ROM angegeben. Folgende Befehle sind damit möglich: "Verschiebe nach Reggister A den Wert aus Adresse 17 der RAM" oder "springe zu Adresse 42 der ROM"(bedeutet den Sprung zu einem anderen Programmteil).

Es folgt nun eine Übersicht des Befehlssatzes. Die Kennnummern der ALU-Operationen kann man der 2. Tabelle entnehmen. Die Kennnummern aller Speicher sind in der 3. Tabelle "Memory-Table" vermerkt.

### Takt

Zeitlich wird der Prozessor über einen Takt koordiniert. In jedem Taktzyklus,bestehend aus steigender und fallender Taktflanke, wird ein neuer Befehl geladen, decodiert und ausgeführt.  

### ROM

Bild

Die eigentliche ROM (1) besitzt links einen Adresseingang und rechts einen Datenausgang. Die Adressen stammen vom *Instruction Pointer*(2) (Befehlszähler). Im Normalbetrieb zählt dieser Aufwärts und ruft nach der Ausführung des alten Befehls bei steigender Taktflanke den neuen Befehl auf. Bei der Ausführung von Sprungbefehlen durch den Pin "jump" wird der alte Zählerwert durch den Wert vom Adressbus überschrieben.
Rechts findet eine Aufsplittung des Maschienenbefehls in ihre Funktionen statt. Weiterhin kann hier bei Auswahl von *immidiate value* als Quelle (3) dessen Bits auf den Datenbus gelegt werden.

### RAM und Stack

Der obere Speicherbaustein stellt den RAM (1) dar. Adressiert wird er über den Adressbus. Der Stack (2) wird hingegen über den Stackpointer adressiert, der beim Speichern inkrementiert und beim Lesen dekrementiert wird, um das bereits erwähnte FIFO-Prinzip zu gewährleisten. Wie der RAM besitzt wie der Stack einen gemeinsamen Speicher-und Ladeport, der mit dem Datenbus verbunden ist. Ob und welches der Bauteile als Senke oder Quelle angesprochen wird, ist durch den Drain-und Sourcebus festgelegt. Die Ansteuerung von RAM und Stack erfolgt über die Kontrollogik, die sich im linken Teil der Schaltung befindet. 
### Register

Es sind in meinem Prozessor momentan 5 *General Purpose Register* Register (1-5) (AX-EX, das X steht für die Verwendung als GPR) sowie ein Adressregister (6) (P für *Pointer-Register*) vorhanden. Zusätzlich kann ein Zufallsgenerator (7) als Quelle angesprochen werden. Für die Auswahl des Quellregisters ist der 1. Multiplexer (8) verantwortlich, die Steuerung erfolgt mit dem Source-Bus. Mit der Auswahlschaltung kann ein beliebiges Register auf den Datenbus gelegt werden. Bei einem ALU- Befehl allerdings wird der Ausgang zum Datenbus gesperrt, sodass der Wert ausschließlich auf den 1. Direktbus (9) zur ALU gelegt wird. Das Sperren ist notwendig, da das Berechnungsergebnis über den Datenbus zurückgeschrieben wird. Mit dem 2. Multiplexer (10) kann ein 2. ALU-Operator über den weiteren Direktbus ausgewählt werden. Er wird beim ALU-Befehl über die unteren 5 Bits von *immidiate value* angesteuert.  
Jedes Register verfügt neben dem Datenausgang über einen Dateneingang sowie einem Takt- und Enable-Eingang. Um in die Register zu speichern, wird das durch den Decoder (11) "enabelte" Register bei eintreffendem Takt mit dem Wert aus dem Datenbus überschrieben. Der Dekoder wird über den Drain-bus gesteuert.
Der Multiplexer am Pointer-Register (12) wird über das 5. Opcode-Bit angesteuert und wählt entweder das Register selber oder *immidiate value* als Quelle für den Adressbus aus.


### ALU

Alle verfügbaren Funktionen der ALU sind durch entsprechende Bausteine realisiert, die untereinander angeordnet sind und die Operanden A und B verrechnen (bzw nur A modifizieren). Die Auswahl der Funktion geschieht durch einen Multiplexer (1), der durch die oberen 5 Bits von *immediate value* angesteuert wird. Bei der Multiplikation kann ein maximal 10 Bit großer Übertrag entstehen, bei der Division dagegen ein Rest. Diese werden in 2 separaten Speichern (2) abgelegt, die bei jeder Multiplikation/Division aktualisiert werden und über den Multipexer abrufbar sind. Weiterhin werden alle für die Statusregister wichtigen Zustände der ALU über einzelne Kontrolleitungen rechts aus der Teilschaltung geführt. Das Steuerwerk der ALU (3) befindet sich links oben.
Der Aufbau der ALU ist eher demonstrativ und weniger realitätsnah. Operationen wie die Addition und die Subtraktion lassen sich beispielsweise in einer einzigen Schaltung realisieren. Weiterhin wurde hier nur auf Fertigbausteine von Logisim zurückgegriffen. Die Ausarbeitung der Bausteine auf Gatterebene ist zum Teil schon erfolgt und wird im zweiten Teil der Arbeit ausführlich erläutert.

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

Jedes der einzelnen Flipflops stellt ein Statusregister dar, welches seine Information durch die entsprechende Kontrollleitung erhält. Die meisten Informationen stammen von der ALU, aber auch vom Inputbuffer (KEY-Flag, für "Keyboard") und dem Screenbuffer (PIX-Flag). Alle ALU-Flags werden ausschließlich bei einem ALU-Befehl aktualisiert (Takteingang durch UND-Gatter gesichert (1) ). Dagegen wird das PIX- und Key-Flag in jedem Taktzyklus aktualisiert (Bei ersterem kann dadurch der TPI-Befehl gespart werden, wenn die bedingte Sprunganweisung unmittelbar auf einen Befehl folgt, der die Pixelkoordinate auf dem Datenbus gelegt hat). Die Flags der Vergleichsoperationen (GR, EQ, SM) werden beim COMP-Befehl normal über den Komparator der ALU gesetzt. Bei einer sonstigen ALU-Operation allerdings wird das Berechnungsergebnis gegen 0 verglichen (2) und die Flags werden entsprechend gesetzt. Die Doppelnutzung spart zusätzliche Statusregister und in vielen Fällen auch einen expliziten COMP-Befehl. Das letzte Flag ist Teil der Realtimeclock (3). Diese setzt nach einer (bis jetzt manuell) definierten Anzahl von Taktzyklen das RLT-Flag. Gelöscht wird es durch Abfragen. 
Jedes Flag-Register besitzt einen positiven Ausgang Q und einen negativen Ausgang Q Strich. Alle positiven und alle negativen Ausgänge sind in jeweils einem Multiplexer zusammengeführt. Bei einem JMT-Befehl wird der erste Multiplexer (4) aktiviert, ein gesetztes Flag hat bei seiner Auswahl eine 1 am Jump-Pin zur Folge. Bei einem JMF-Befehl (zweiter Multiplexer (5) )führt dagegen das gewählte Flag zu einer Sprunganweisung, wenn es nicht gesetzt ist. Das Steuerwerk ist der Übersicht halber in einer Teilschaltung zusammengefasst.



## Assemblerprogrammierung

Es folgt zunächt eine Begriffsabgrenzung: "Verständlich" für den Prozessor selbst ist lediglich **Maschienensprache** (binäre Folge aus einsen und nullen). Da diese für den Menschen schwer zu händeln ist, werden Befehle mit **Mnemonics** abgekürzt, die zusammen mit weiteren mnemonischen Abkürzungen für die zusätzlichen Argumente eine **Assemblersprache** bilden (auch kurz **Assembler** genannt). Die Umwandlung des Assembler-Codes in Maschienensprache übernimmt ein spezielles Programm, der **Assembler**.
## Einordnung

Die bauliche Trennung von Daten- und Befehlsspeicher entspricht der Harvard-Architektur. In der Von-Neumann-Architektur dagegen liegen Daten und Befehle auf einem gemeinsamen Speicher vor. Letztere zeichnen sich durch eine hohe Universalität, weshalb fast alle modernen Hauptprozessoren dieser Gattung angehören. sind jedoch in ihrer Impementierung komplizierter. Erstere dagegen werden heutzutage in
 Vorteil ist, dass Befehle und benötigte Daten gleichzeitig statt hintereinander geladen und verarbeitet werden können.

## Erarbeitungsweise und -Ablauf

Vor dem Informatikunterricht: 
* Aneignung von Grundlagennwissen und Erfahrung über Handyspiel
* Entwurf kleinerer Schaltungen
* 
Ab dem Informatikunterricht: 
* der Prozessor wird in etwas modifizierter Form zuendegebaut, um wieder mit der Materie vertraut zu werde

Wie anfangs erwähnt, 


