# Virtueller Bau eines Computers in Logisim (erster Teil)

## Motivation

Mein Interesse am (virtuellen) Entwurf von elektrischen Schaltungen stammt ursprünglich von einem Handy-Spiel, welches entsprechende Möglichkeiten zur Verfügung stellte. Nach dem Entwurf kleinerer, eher aufgabenspezifischer Schaltungen schien die Lösung zu noch leistungsfähigeren Schaltnetzen in der Prozessorbauweise zu liegen. Anschließend folgte der Umstieg auf die Entwicklungsumgebung Logisim, mit der der Entwurf größerer Schaltungen stark vereinfacht wurde. 
Mein Ziel der Arbeit war nicht der Nachbau eines bestehenden Prozessors, sondern das eigenständige Entwickeln unter Berücksichtigung gebräuchlicher Konzepte. Durch das Vorhandensein einer Ein- und Ausgabe (Tastatur und Bildschirm) hat das Projekt den Status eines vollwertigen Computers.

## Arbeitsumgebung

Logisim ist ein Logiksimulator, der dem Nutzer das Entwerfen und Simulieren von logischen Schaltungen ermöglicht. Angewendet wird das Programm häufig an Universitäten zu Vorlesungs- und Lehrzwecken. Neben den einfachen Grundbausteinen wie Gatter und Flipflops verfügt Logisim auch über komplexere Bausteine wie Auswahlschaltungen, Rechenwerke oder Bauteile zur Ein-und Ausgabe. Grundkenntnisse über  Logikgatter, Flipflops und dem Binär-/Hexadezimalsystem werden vorrausgesetzt. Andere, zum Teil Logisim-spezifische Teile/Konzepte werden kurz erläutert:

### Konzepte

**Teilschaltungen:** Größere Schaltungen lassen sich zu einer Teilschaltung zusammenfassen, dessen Aussehen frei bearbeitet werden kann. Der Datenaustausch erfolgt über...

**Ein- und Ausgangspins:** Sie können lediglich 1 Bit, aber auch mehrere Bits groß sein.

**Leitungsbündel:** Der Übersicht halber können mehrere Bits zu einem Leitungsündel zusammengefasst werden. Viele der vorgefertigten Logisim-Bausteine unterstützen das Verarbeiten von Leitungsbündeln. Im Gegensatz zu den grünen Einzelleitungen sind sie schwarz dargestellt.

### Einige wichtige Bauteile

**Multiplexer:** Wählt über eine Auswahlleitung eines von mehreren Eingängen aus und gibt ihn am Ausgang aus.

**Decoder:** Besitzt einen Ausgang für jede mögliche Kombination der binären Kontrollleitung und schaltet diesen bei Auswahl auf 1.

**Register:** Können einen Wert in definierter Bitbreite speichern

**RAM, ROM:** Sie verfügen über mehrere Speicheradressen mit beliebiger Bitbreite.

**Tastatur:** Der Nutzer kann beim Anklicken des Bauteils über seine physische Tastatur Zeichen eingeben, die am Datenausgang in ASCII-Codierung (7-Bit) bereitliegen. Im Bauteil ist ein Eingabepuffer integriert.
 
## Grundlegende Funktionsweise

Ein Prozessor ist eine in der Regel frei programmierbare elektrische Schaltung, die Daten basierend auf den programmierten Algorithmen verarbeitet und diese anschließend an weitere Komponenten übergibt oder Peripheriegeräte ansteuert (z.B. Bildschirm etc.). Neben der zahlreichen Verwendung als Mikrocontroller in eingebetteten Systemen (Waschmaschinen, CD-Spieler) stellt er in einem Computer als Hauptprozessor die zentrale Recheneinheit dar (englisch: CPU = *Central Processing Unit*).

Grundsätzlich verknüpft man mit einem Prozessor Recheneinheiten, Speicherbausteine und diverse Kontrolllogik so miteinander, dass diese durch den Programmierer koordinierbar werden. So können komplexe Programme über mehrere Schritte hinweg abgearbeitet werden, die weit über einfache arithmetische Aufgaben hinaus reichen. Jeder Prozessor lässt sich dazu in Assemblersprache, einer sehr maschinennahen Programmiersprache, programmieren (siehe Abschnitt #Programmierung).

### Funktioneller Überblick

![main](https://user-images.githubusercontent.com/31915930/33040662-0af28c64-ce3c-11e7-9382-dcde2b0931cf.PNG)

Der Prozessor bildet den gesammten oberen Teil. Bildschirm und Eingabe gehören streng genommen nicht dazu.

#### ROM (Befehlsspeicher) und RAM (Datenspeicher)

Auf dem Befehlsspeicher befinden sich die auszuführenden Programme in Form einzelner Befehle. Daten, die durch Berechnungen oder Nutzereingaben anfallen, werden auf dem Datenspeicher abgelegt. Der Befehlsspeicher ist hier als *Festwertspeicher* konzipiert (*ROM* = *Read Only Memory*, freie Adressierung, jedoch kein Beschreiben möglich), während den Datenspeicher ein *Direktzugriffsspeicher/RAM* bildet (*RAM* = *Random-Access Memory*, freie Adressierung sowie Lesen und Schreiben möglich). Die bauliche Trennung von Daten- und Befehlsspeicher entspricht der *Harvard-Architektur* (siehe Abschnitt #Einordnung)

#### ALU (arithmetic logic unit)

Die zu deutsch *arithmetisch-logische Einheit* stellt das Rechenwerk des Prozessors dar. Je nach Umfang lassen sich mit ihr sowohl arithmetische Operationen (z.B Grundrechenarten), logische Operationen (z.B. Konjunktion, Negation, Vergleichsoperation etc.) als auch Bitmanipulationen (z.B einzelne Bits/Bitgruppen verändern) durchführen. Weiterhin werden zusätzliche Informationen über die Berechnungen (z.B Auftreten eines Übertrags, Division durch 0 etc.) an die Statusregister weitergegeben.

#### Statusregister/Flagregister

Die Statusregister oder kurz Flags sind einzelne Flipflops, die bestimmte Zustände des Prozessors abspeichern. Wie erwähnt, können entsprechende Informationen von der ALU, aber auch von anderen Komponenten wie dem Ein-und Ausgabecontroller, stammen. Sinn der Statusregister ist die Ausführung bedingter Sprungbefehle. Dabei springt der Prozessor unter einer bestimmten Bedingung zu einer anderen Programmadresse. Eine nähere Beschreibung erfogt im Abschnitt #Detailierte Übersicht#Statusregister.

#### Register

Alle Daten, die sich in unmittelbarer Bearbeitung befinden, werden für den schnellen Abruf in einzelnen Speichern, den sog. Registern zwischengespeichert. Es existieren neben den *GPRs* (*General Purpose Register*, universell nutzbar) auch Spezialregister, wie einem Adress-Register (siehe Abschnitt #DÜ#Reg).

#### Stack

Der Stack oder zu deutsch Stapelspeicher funktioniert nach dem LIFO-Prinzip (Last In – First Out). Die Daten, die als letztes eingespeichert wurden, können beim Lesen zuerst wieder ausgegeben werden. Der Stack kann zum Beispiel dazu verwendet werden, Register freizuräumen, wenn kurzeitig ein Unterprogramm ausgeführt werden muss. In diesem Prozessor findet sich der Stack nahe der RAM.

#### Eingabecontroller

Die Nutzereingaben werden in einem Puffer zwischengespeichert. Der Controller steuert das Auslesen des Puffers, wandelt die Eingaben um und gibt sie an den Computer weiter.

#### Framebuffer

Der Framebuffer ist ein Speicher, der eine digitale Kopie des Bildschirms beinhaltet. Notwendig ist dieser, um das bestehende Bild zwischen den Bearbeitungsphasen zu erhalten. 

#### Steuerwerk

Das Steuerwerk ist für die Decodierung und Interpretation der Befehle zuständig. Dazu spricht es die betreffenden Bauteile über Kontrollleitungen an. In meiner Prozessorimplementierung existiert kein zentrales Steuerwerk, dagegen befindet sich die nötige Kontrolllogik direkt bei den zugehörigen Teilschaltungen.

#### Bussysteme

Alle Teilschaltungen sind neben einzelnen Steuerleitungen über sog. Busse miteinander verknüpft. Ein *Bus* wird aus mehreren gekoppelten Bits gebildet, dadurch können größerer Zahlenwerte oder Adressen übermittelt werden. Folgende Busse existieren in meinem Prozessor:

**Befehlsbus:** ist in 4 Unterbusse aufgeteilt und übermittelt die Programmbefehle an die Steuerwerke der Bauteile.

**Datenbus:** Auf ihm findet der gesamte Datenaustausch zwischen den Bauteilen statt. Die Besonderheit des Busses liegt in seiner bidirektionalen Nutzung: Daten können von einem beliebigen Bauteil in alle Richtung zu anderen Bauteilen transferiert werden. Alle anderen Busse sind unidirektional konzipiert und besitzen damit eine feste Datenquelle.

**Adressbus:** Dieser dient zur Adressierung von RAM und ROM.
Eine nähere Beschreibung erfogt im #Technischen Überblick.

## Technischer Überblick

### Struktur des Befehlssatzes

Zum detaillierten Verständnis der gesamten Schaltung ist es notwendig, die spezifische Befehlssatzstruktur des Prozessors zu kennen. Mit dem Befehlssatz wird die Gesamtheit aller gültigen Maschinen-Befehle bezeichnet. Die einzelnen Befehle aus dem Befehlssatz werden in dem 23-Bit breiten Befehlsbus übermittelt, wobei dieser funktionell in mehrere Bitgruppen gegliedert ist, wie im Folgenden zu sehen:
```
0000 0       0000    0000      0000000000
Opcode+ACB   Drain   Source    immidiate value
```
**Opcode:** in diesem wird der Hauptbefehl angegeben (z.B "Verschiebe einen Wert")

**Drain und Source/Dr,So:** Fast alle Speicher im Prozessor lassen sich entweder als *Senke*(Drain) oder *Quelle*(Source) ansprechen. Mit beiden Werten kann der Opcode wie im folgenden Beispiel weiter konkretisiert werden: "Verschiebe nach Register A den Wert von Register B". Register A ist in diesem Fall die Senke, Register B die Quelle (es wird in der Formulierung immer zuerst die Senke genannt). Der Datentransfer findet über den Datenbus statt: Der Wert von Register B wird auf den Datenbus gelegt, während in Register A der Wert vom Datenbus gespeichert wird. Bei einem ALU-Befehl wird das Ergebnis über den Datenbus zurückgeschrieben, daher sind 2 Direktbusse für die Operanden A und B notwendig, die aus den Registern gewählt werden können.

**Immediate value/ImV:** In vielen Fällen muss der Programmierer feste Werte, Adressen oder Pixelkoordinaten angeben können. Diese werden in den letzten 10 Bits des Befehlsbusses übermittelt. *immediate value* kann als Quelle angesprochen werden und wird in diesem Fall auf den Datenbus gelegt. Damit wäre die Anweisung "Schreibe den Wert X in das Register A" möglich. Weiterhin werden die Bits bei bestimmten Befehlen für die Übergabe zusätzlicher Argumente verwendet (beim ALU-Befehl: Auswahl der Operation und optional zweiter Quelle, beim Reset-Befehl den zu löschenden Speicher).

**Adressbus Controll Bit/ACB:** Das letzte Bit des Opcodes ist für eine Spezialfunktion reserviert. Ein Setzen des Bits bewirkt, das *immediate value* auf den Adressbus gelegt wird. Bei nicht gesetztem Bit wird als Quelle für den Adressbus das Adressregister ausgewählt. Der Programmierer kann dadurch direkt aus bestimmten Adressen der RAM speichern bzw. laden oder explizite Sprungadressen für die ROM angegeben. Folgende Befehle sind damit möglich: "Verschiebe nach Register A den Wert aus Adresse 17 der RAM" oder "springe zu Adresse 42 der ROM" (bedeutet den Sprung zu einem anderen Programmteil).

Es folgt nun eine Übersicht des Befehlssatzes. Die Kennnummern der ALU-Operationen kann man der 2. Tabelle entnehmen. Die Kennnummern aller Speicher sind in der 3. Tabelle "Memory-Table" vermerkt.

![befehlssatz](https://user-images.githubusercontent.com/31915930/33040313-f5e55bd6-ce3a-11e7-8c86-6dc4edc5bf69.PNG)

![zusatztabelen](https://user-images.githubusercontent.com/31915930/33040388-3037d908-ce3b-11e7-8592-46de94005915.PNG)
### Takt

Zeitlich wird der Prozessor über einen Takt koordiniert. In jedem Taktzyklus, bestehend aus steigender und fallender Taktflanke, wird ein neuer Befehl geladen, decodiert und ausgeführt.  

### ROM

![rom](https://user-images.githubusercontent.com/31915930/33040379-2bbcba92-ce3b-11e7-990b-44ff67598a37.jpg)

Die eigentliche ROM (1) besitzt links einen Adresseingang und rechts einen Datenausgang. Die Adressen stammen vom *Instruction Pointer*(2) (Befehlszähler). Im Normalbetrieb zählt dieser Aufwärts und ruft nach der Ausführung des alten Befehls bei steigender Taktflanke den neuen Befehl auf. Bei der Ausführung von Sprungbefehlen durch den Pin "jump" wird der alte Zählerwert durch den Wert vom Adressbus überschrieben. Mit einer Adressbreite von 10 Bit können hier 1024 Byte adressiert werden (1 Byte ist hier in seiner alternativen Bedeutung als "kleinste adressierbare Einheit" definiert, also als 10 Bit (durch ALU-Breite bestimmt)).
Rechts findet eine Aufsplittung des Maschinenbefehls in ihre Funktionen statt. Weiterhin kann hier bei Auswahl von *immidiate value* als Quelle (3) dessen Bits auf den Datenbus gelegt werden. 

### RAM und Stack

![ram_stack](https://user-images.githubusercontent.com/31915930/33040350-18820afe-ce3b-11e7-8bec-6a195453a189.jpg)

Der obere Speicherbaustein stellt den RAM (1) mit einer Speicherkapazität von ebenfalls 1024 Byte dar. Adressiert wird er über den Adressbus. Der Stack (2) wird hingegen über den Stackpointer adressiert, der beim Speichern inkrementiert und beim Lesen dekrementiert wird, um das bereits erwähnte FIFO-Prinzip zu gewährleisten. Wie der RAM besitzt der Stack einen gemeinsamen Speicher- und Ladeport, der mit dem Datenbus verbunden ist. Ob und welches der Bauteile als Senke oder Quelle angesprochen wird, ist durch den Drain-und Sourcebus festgelegt. Die Ansteuerung von RAM und Stack erfolgt über die Kontrolllogik, die sich im linken Teil der Schaltung befindet. 

### Register

![reg](https://user-images.githubusercontent.com/31915930/33040355-1c3fb18c-ce3b-11e7-9238-b068fe6a7a3a.jpg)

Es sind in meinem Prozessor momentan 5 *General Purpose Register* Register (1) (AX-EX, das X steht für die Verwendung als GPR) sowie ein Adressregister (2) (P für *Pointer-Register*) vorhanden. Zusätzlich kann ein Zufallsgenerator (3) als Quelle angesprochen werden. Für die Auswahl des Quellregisters ist der 1. Multiplexer (4) verantwortlich, die Steuerung erfolgt mit dem Source-Bus. Mit der Auswahlschaltung kann ein beliebiges Register auf den Datenbus gelegt werden. Bei einem ALU-Befehl allerdings wird der Ausgang zum Datenbus gesperrt, sodass der Wert ausschließlich auf den 1. Direktbus (5) zur ALU gelegt wird. Das Sperren ist notwendig, da das Berechnungsergebnis über den Datenbus zurückgeschrieben wird. Mit dem 2. Multiplexer (6) kann ein 2. ALU-Operator über den weiteren Direktbus ausgewählt werden. Er wird beim ALU-Befehl über die unteren 5 Bits von *immidiate value* angesteuert.  Ein dritter Direktbus führt aus dem Register EX zum Framebuffer (Shift-Operator, siehe Framebuffer)
Jedes Register verfügt neben dem Datenausgang über einen Dateneingang sowie einen Takt- und Enable-Eingang. Um in die Register zu speichern, wird das durch den Decoder (7) "enabelte" Register bei eintreffendem Takt mit dem Wert aus dem Datenbus überschrieben. Der Decoder wird über den Drain-Bus gesteuert.
Der Multiplexer am Pointer-Register (8) wird über das 5. Opcode-Bit angesteuert und wählt entweder das Register selber oder *immidiate value* als Quelle für den Adressbus aus.


### ALU

![alu](https://user-images.githubusercontent.com/31915930/33040308-f1efa572-ce3a-11e7-8c0c-a4d63efa3ce2.jpg)

Alle verfügbaren Funktionen der ALU sind durch entsprechende Bausteine realisiert, die untereinander angeordnet sind und die Operanden A und B verrechnen (bzw. nur A modifizieren). Die Auswahl der Funktion geschieht durch einen Multiplexer (1), der durch die oberen 5 Bits von *immediate value* angesteuert wird. Bei der Multiplikation kann ein maximal 10 Bit großer Übertrag entstehen, bei der Division dagegen ein Rest. Diese werden in 2 separaten Speichern (2) abgelegt, die bei jeder Multiplikation/Division aktualisiert werden und über den Multipexer abrufbar sind. Weiterhin werden alle für die Statusregister wichtigen Zustände der ALU über einzelne Kontrolllleitungen rechts aus der Teilschaltung geführt. Das Steuerwerk der ALU (3) befindet sich links oben. 
Der Aufbau der ALU ist eher demonstrativ und weniger realitätsnah. Operationen wie die Addition und die Subtraktion lassen sich beispielsweise in einer einzigen Schaltung realisieren. Weiterhin wurde hier nur auf Fertigbausteine von Logisim zurückgegriffen. Die Ausarbeitung der Bausteine auf Gatterebene ist zum Teil schon erfolgt und wird im zweiten Teil der Arbeit ausführlich erläutert.

### Statusregister

![flag](https://user-images.githubusercontent.com/31915930/33040320-fb7cc8ae-ce3a-11e7-8b06-fc37ca97cdee.jpg)

Jedes der einzelnen Flipflops stellt ein Statusregister dar, welches seine Information durch die entsprechende Kontrollleitung erhält. Die meisten Informationen stammen von der ALU, aber auch vom Inputbuffer (KEY-Flag, für "Keyboard") und dem Screenbuffer (PIX-Flag). Alle ALU-Flags werden ausschließlich bei einem ALU-Befehl aktualisiert (Takteingang durch UND-Gatter gesichert (1) ). Dagegen wird das PIX- und Key-Flag in jedem Taktzyklus aktualisiert (Bei ersterem kann dadurch der TPI-Befehl gespart werden, wenn die bedingte Sprunganweisung unmittelbar auf einen Befehl folgt, der die Pixelkoordinate auf dem Datenbus gelegt hat). Die Flags der Vergleichsoperationen (GR, EQ, SM) werden beim COMP-Befehl normal über den Komparator der ALU gesetzt. Bei einer sonstigen ALU-Operation allerdings wird das Berechnungsergebnis gegen 0 verglichen (2) und die Flags werden entsprechend gesetzt. Die Doppelnutzung spart zusätzliche Statusregister und in vielen Fällen auch einen expliziten COMP-Befehl. Das letzte Flag ist Teil der Realtimeclock (3). Diese setzt nach einer (bis jetzt manuell) definierten Anzahl von Taktzyklen das RLT-Flag. Gelöscht wird es durch Abfragen. 
Jedes Flag-Register besitzt einen positiven Ausgang Q und einen negativen Ausgang Q Strich. Alle positiven und alle negativen Ausgänge sind in jeweils einem Multiplexer zusammengeführt. Bei einem JMT-Befehl wird der erste Multiplexer (4) aktiviert, ein gesetztes Flag hat bei seiner Auswahl eine 1 am Jump-Pin zur Folge. Bei einem JMF-Befehl (zweiter Multiplexer (5) )führt dagegen das gewählte Flag zu einer Sprunganweisung, wenn es nicht gesetzt ist. Das Steuerwerk ist der Übersicht halber in einer Teilschaltung zusammengefasst.

### Framebuffer

![frb](https://user-images.githubusercontent.com/31915930/33040329-0700e0d4-ce3b-11e7-9c43-fb46832c80fa.jpg)

Der hier verwendete Bildschirm von Logisim umfasst 32*\32 Pixel und wird zeilenweise gesteuert, sodass sich 32 Eingänge mit einer Bitbreite von 32 Bit ergeben. Um jedes der insgesamt 1024 Pixel unabhängig steuern zu können, bedarf es ebenso 1024 Speicherzellen (da der Bildschirm nur zwei Farben darstellen kann, genügen D-FLipflops). Jede Zeile wird durch eine Teilschaltung (1) gesteuert, die wiederum 32 D-Flipflops beinhaltet. Jedes der Flipflops innerhalb einer Teilschaltung kann durch die zugehörige Kontrollleitung aktiviert werden, die dem Leitungsbündel vom ersten Decoder (2) entspringt. Dieser wird durch die oberen Bits von *immediate value* gesteuert und bestimmt die X-Koordinate. Gleichzeitig wird jedoch durch den zweiten Decoder (3) (über untere Bits von *ImV* gesteuert) immer nur eine der Teilschaltungen aktiviert. Somit wird die Y-Koordinate bestimmt. Das nun aktivierte Flipflop speichert bei Eintreffen des Taktes den Wert, der am allgemeinen Dateneingang (4) der Schaltung anliegt und über das Steuerwerk bestimmt wird (bei SPI eine 1, bei CLP eine 0). Zusätzlich besitzt die Schaltung eine Status-Leitung, die bei anliegender Pixelkoordinate den Status des gewählten Flipflops wiedergibt. Dieser wird direkt an das PIX-FLag weitergegeben.

Ursprünglich für die Textausgabe konzipiert, kann auf den Wert vom Datenbus der Wert aus Register EX aufaddiert werden (5). Damit lassen sich ganze Pixelgruppen beim Setzen um eine konstante Weite und Höhe verschieben, ohne sie vorher umrechnen zu müssen. Die Funktion wird nur beim SPS- und CPS-Befehl aktiviert.

### Eingabecontroller

![inp ctrl](https://user-images.githubusercontent.com/31915930/33040337-0d5c8f8c-ce3b-11e7-9a52-86dd688f9f8e.jpg)

Dieser wandelt die Eingaben vom Keyboard, die in ASCII-Codierung vorliegen, mittels eines RAM in kontinuierliche Zahlen um (momentan von 0-9, A-Z). Außerdem steuert er, wann ein Zeichen vom Keyboard-Buffer gelöscht werden muss. ALLAS IN CORÜPRATIO 

### Reset-Controller

![reset](https://user-images.githubusercontent.com/31915930/33040373-247ccde4-ce3b-11e7-9472-074d34dccdc9.jpg)

Alle Speicher besitzen asynchrone (nicht vom Takt abhängige) Rese-Eingänge. Der Reset-Controller ist der Einfachheit halber zentral angelegt. Er wird über die Opcode-Bits und *ImV* gesteuert und setzt die entsprechenden Bauteile über einzelne Kontrollleitungen zurück (in der Hauptschaltung sind diese in einem Leitungsbünedel zusammengefasst). Der Nutzer kann von außen durch den Reset-Taster den gesamten Computer zurücksetzen.

### Steuerlogik

Auf die Steuerlogik wurde in der bisherigen Dokumentation nur in dem Maße eingegangen, wie es zum grundlegenden Verständnis notwendig ist. Besonders in dem Arbeitsschritt der Fehleranalyse stellten sich viele Probleme heraus, die erst im Zusammenspiel mit den anderen Komponenten ersichtlich wurden und teiweise sehr spezielle Anforderungen an die Kontrolllogik stellten. So ist zum Beispiel an drei Stellen (Eingabecontroller, Realtimeclock, Framebuffer) der Einsatz einer "zeitlichen Prioritätsschaltung" (ZP) erforderlich (unteres Bild). Sie besitzt 2 Eingänge und schaltet nur dann eine 1 am Ausgang , wenn der Erste vor dem Zweiten eintrifft (hier: der Erste immer Steuerleitung, der zweite immer der Takt) . Ein weiteres Beispiel stellt die Implementierung des bidirektionalen Datenbusses dar, bei dem spezielle Tristate-Puffer notwendig waren. 

![zp](https://user-images.githubusercontent.com/31915930/33040383-2db4335c-ce3b-11e7-8dc8-f7a0da6e747c.jpg)

## Assemblerprogrammierung

Es folgt zunächt eine Begriffsabgrenzung: "Verständlich" für den Prozessor selbst ist lediglich **Maschinensprache** (binäre Folge aus Einsen und Nullen). Da diese für den Menschen schwer zu durchschauen ist, werden Befehle mit **Mnemonics** abgekürzt, die zusammen mit weiteren mnemonischen Abkürzungen für die zusätzlichen Argumente eine **Assemblersprache** bilden (auch kurz **Assembler** genannt). Die Umwandlung des Assembler-Codes in Maschinensprache übernimmt ein spezielles Programm, der eigentliche **Assembler**. 
Die Assemblersprache kann je nach verwendetem Assembler bei gleicher Maschinensprache unterschiedlich ausfallen.

Für die Assemblersprache meines Assemblers gelten grob folgende Regeln: 
* Es müssen alle optionalen Argumente eines Befehls angegeben werden, außer fehlende ImV-Werte
* Für die Reihenfolge gilt Folgendes:
```Opcode; 0 oder 1 für das DAB, Drain und/oder Source, Festwert```
* Wenn kein Drain oder Source benötigt wird, muss anstelle eine 0 angegeben werden
* Bei einem ALU-Befehl wird lediglich das Operationskürzel, gefolgt von Dr, So1 und So2, angegeben
Durch einen höheren Programmieraufwand könnte die Sprache noch einfacher und intuitiver gestaltet werden. Der Code für den Assembler ist in folgendem Bildausschnitt zu sehen. Er gliedert sich grob in den oberen Datenabschnitt und den unteren Programmabschnitt. Dem Datenabschnitt fehlen noch einige Komponenten.

![assembler](https://user-images.githubusercontent.com/31915930/33040499-8dddf70e-ce3b-11e7-8bb8-1cfb061f42e5.PNG)

Folgendes kleines Programm soll den Umgang mit Assemblersprache demonstrieren. Die mit $-Zeichen angegebenen Adressen müssten noch durch ablsolute dezimale Adresswerte ausgetauscht werden. Der Code diente als Testprogramm für Snake und bewegt eine Schlange von links nach rechts, die durch eine beliebige Nutzereingabe um einen Pixel verlängert werden kann. Das Verschiebeverfahren ist zwar nicht sonderlich effizient, dafür ist die Verlängerung des Wurms aber sehr einfach zu bewerkstelligen. 


```
spi 0 dv 130			   # Die drei Ausgangspixel werden gesetzt
spi 0 dv 98
spi 0 dv 66
movi 0 ram dv 130		   # Die drei Pixelkoordinaten werden in die RAM gelegt
movi 0 ram dv 98
mov 0 ram dv 66
mov 0 cx dv 2			   # In CX wird die Ausgangslänge des Wurmes -1 geschrieben

mov 1 ax ram 0		  $1	   # Der "Kopf" der Schlage wird aus Adresse 0 der RAM nach AX verschoben
+x ax ax 0			   # Erhöhung der X-Koordinate ; anschließendes Setzen des "neuen Kopfes" und löschen des 
spi 0 ax			     letzten "Schwanz-Pixels"
mov 0 p dx 
clp 0 ram

mov 0 p dv 0			   # Erhöhung der restlichen Pixelkoordinaten: Alle außer der letzten Koordinate werden auf den Stack 
				     geschoben und anschließend um 1 versetzt wieder in die ROM zurückgespeichert.
movi 0 stk ram		  $2		# Auslesen in Schleifenform
cmp 0 p dx					
jmf 1 eq $2

mov 0 ex dv 0

movd 0 ram stk		  $3		# Zurückspeichern in Schleifenform
cmp 0 p ex 
jmf 1 eq $3

mov 1 ram ax 0		     	    # Der vorher berechnete "neue Kopf" wird in Adresse 0 der RAM gelegt

jmf 1 key $4			    # Wenn eine Nutzereingabe vorhanden ist, werden die folgenden 2 Befehle bearbeitet, ansonsten   					      übersprungen (if-Statement)                          		      
inc dx dx 0			    # Inkrementieren des Registers mit der Wurmänge ; löschen des Inputbuffers
res ipb

jmp 1 0 $1		  $4 	    # Wiederhole die Schleife

```
## Einordnung

Die bauliche Trennung von Daten- und Befehlsspeicher entspricht der *Harvard-Architektur*. In der *Von-Neumann-Architektur* dagegen liegen Daten und Befehle auf einem gemeinsamen Speicher vor. Da Programme sowohl viele Daten und wenig Befehle beinhalten können, oder aber umgekehrt, sind Prozessoren der Von-Neumann-Architektur universeller einsetzbar. Deshalb gehören fast alle modernen Hauptprozessoren dieser Gattung an, allerdings ist ihre Impementierung komplizierter. Vorteil der Harvard-Architektur ist, dass Befehle und benötigte Daten gleichzeitig statt hintereinander geladen und verarbeitet werden können. In eingebetteten Systemen findet dieser Prozessortyp häufig Anwendung als Signalprozessor, wenn bei bekannter Datenbreite Signale möglichst schnell verarbeitet werden müssen.
Weiterhin lässt sich der Prozessor der RISC (Reduced Instruction Set Computer)- Designphilosophie zuordnen. Im Gegensatz zu CISC (Complex Instruction Set Computer)- Prozessoren verfügt er über einen vergleichsweise kleinen Befehlssatz. Dafür können (fast) alle Befehle innerhalb eines Taktes bearbeitet werden. Ein CISC-Prozessor verfügt über mehr und weitaus komplexere Befehle, ferner kann jedem Befehl (Makroprogramm) ein sog. Mikroprogramm zugeordnet werden, welches in mehreren Taktzyklen bearbeitet wird. Moderne Prozessoren sind in der Regel Mischformen.

Bei der Angabe der Bitbreite eines Prozessors bezieht man sich vor allem auf die Bitbreite der ALU. Demnach handelt es sich hier um einen 10-Bit-Prozessor. Die übliche Bitbreite als Zweierpotenz (16 Bit, 32 Bit, 64 Bit) wurde vor allem mit dem Aufkommen der Hochsprachen eingeführt, es gab früher aber durchaus Prozessoren mit davon abweichender Bitbreite. 

## Erarbeitungsweise und -Ablauf

Bis zum Informatikunterricht:

Wie in der Einleitung erwähnt, eignete ich mir durch ein Handyspiel (Survivalcraft)  viel Grundlagenwissen und Erfahrung über die Konstruktion logischer Schaltungen an. Den ersten Prozessor in Logisim baute ich auf Grundlage einer (unfertigen) Videoserie auf Youtube, in dem ich mir die Funktionsweise notierte und den Prozessor anhand meiner Notizen nachbaute. Anschließend startete ich vor etwa einem Jahr mit der Fertigstellung und Modifikation, das Projekt  wurde jedoch nicht fortgeführt. Nötige Programmierkenntnisse in Python erarbeitete ich mir ebenfalls zu der Zeit.

Ab dem Informatikunterricht:

Um wieder in die Materie einzusteigen, entschied ich mich, vorerst die alte modifizierte Version funktionstüchtig zu machen. Danach startete ich mit der Ausarbeitung eines eigenen Designs: Mit dem Ziel im Hinterkopf, den Hauptprozessor mit einem Graphikprozessor zu erweitern, arbeitete über mehrere Tage hinweg den jetzigen Befehlssatz aus, der wesentlich universeller als der vorige gestaltet ist.
Kernstück des neuen Befehlssatzes war dabei das Konzept des bidirektionalen Datenbusses. Erst danach begann ich mit der technischen Ausarbeitung und der Programmierung eines passenden Assemblers. Einen großen Zeitraum nahm die anschließende Fehleranalyse in Anspruch, bei der diverse Testprogramme auf den Prozessor angesetzt wurden, nachdem alle Fehler im Assembler behoben waren. 

Folgende Teilprojekte wurden zwar bearbeitet, aber bis jetzt nicht oder nur teilweise fertiggestellt:
* Graphikprozessor
* Programmierung von Snake in Assemblersprache
* Rechenwerke (alle Grundrechenarten)
* Befehlszähler
* Pixelalphabet zur Textausgabe auf dem Bildschirm und das Programmkonzept

Bei der Arbeit habe ich, ausgenommen von den Rechenwerken, nur auf Literatur zurückgegriffen, die sich mit gängigen Konzepten um das Thema befasst ( welche Arten von Bussystemen gibt es, wie könnte ein Opcode strukturiert sein etc.). Die technische Ausarbeitung des Assemblers, des Befehlssatzes und des Prozessors erfolgte komplett eigenständig und baute auf meiner bisherigen Arbeit auf.










