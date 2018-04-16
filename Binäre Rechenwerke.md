# Binäre Rechenwerke

## Inhaltsverzeichnis
1. Addier- und Subtrahierwerk
2. Multiplikationswerk
3. Divisionswerk
4. Komparator
5. Befehlszähler
6. Anmerkung

## Addier- und Subtrahierwerk
Grundlage dieses Rechenwerks ist ein Carry-Ripple-Addierer (Der Übertrag (Carry) "rieselt" (ripple) von der kleinsten zur größten Stelle, in diesem Fall also von rechts nach links). Jede Einheit besteht aus zwei Halbaddierern, jeweils bestehend aus einem AND- und XOR-Gate. Zusammen bilden die Halbaddierer einen Volladdierer, der die zwei Eingänge sowie einen Übertrag verrechnen kann. 

Will man Subtrahieren, so entspricht dies einer Addition mit einer negativen Zahl. Um den Subtrahend in eine negative Zahl umzuwandeln, wird von ihm das Zweierkomplement gebildet. Das Zweierkomplement ist eine Darstellungsweise von negativen Zahlen, die das Addierwerk ohne Modifikation verarbeiten kann. Es wird gebildet, indem die Zahl negiert und eine 1 addiert wird (Negation durch die vorgeschalteten XOR-Gates, Addition durch Anlegen einer 1 am ersten Carry-Eingang).

Sollte das Ergebnis negativ sein, wird es "natürlicherweise" als Zweierkomplement angezeigt. Das Ergebnis kann in dieser Form zur weiteren Verrechnung gespeichert werden, oder aber es wird durch erneute Bildung des Zweierkomplements in eine positive lesbare Zahl umgewandelt und ein entsprechendes Flag gesetzt. Bei ersterer Variante muss der obere Teil des Rechenwerks weggelassen werden.

Zu sehen ist die Rechnung: 3-6 = -3

![unbenannt](https://user-images.githubusercontent.com/31915930/38807241-493e7e3e-417c-11e8-9a72-9be19e7a92f2.PNG)

## Multiplikationswerk

Die hier gezeigte Form des Multiplizierwerks ist eine technische Umsetztung der schriftlichen binären Multiplikation. So, wie die Zwischenergebnisse immer versetzt untereinander geschrieben und anschließend addiert werden, findet das Prinzip auch in diesem Rechenwerk Geltung. Es besteht aus Selektoren, Halb- und Volladdieren. Da sich die Bitbreite verdoppelt, werden die oberen 10 Bits getrennt herausgeführt und in einem separaten Speicher hinterlegt.

Zu sehen ist die Rechnung: 788 * 584 = 460.192

![multi](https://user-images.githubusercontent.com/31915930/38807250-5012326e-417c-11e8-98fa-b34429a3f2db.PNG)

## Divisionswerk

Da ich bei meiner Recherche keine adäquaten Formen für ein Divisionswerk fand, konstruierte ich dieses komplett selbstständig. Dazu eignete ich mir die schriftliche binäre Division an, brach das Verfahren auf ein Wesentliches herunter, und entwarf auf dieser Basis das folgende Schaltnetz. Die Grundeinheiten bestehen aus "Conditional Subtractors", die entweder eine Subtraktion der Eingänge vollführen oder diese ungehindert in die nächste Ebene transportieren.

Zu sehen ist die Rechnung: 594 / 68 = 8, Rest 50

![div](https://user-images.githubusercontent.com/31915930/38807262-5486832c-417c-11e8-88f0-7f25bd58ad84.PNG)

## Komparator

Der Komparator vergleicht 2 Zahlen miteinander und zeigt an, ob A<B, A=B oder A>B ist. Jede Einheit besteht aus 2 AND-Gates mit unterschiedlich invertierten Eingängen und einem XOR-Gate. Die Zahl wird Bit für Bit, angefangen mit dem MSB (Most SIgnificant Bit), verglichen. Nur wenn die Bits sich gleichen, werden die nächst tieferen Einheiten aktiviert, bis irgendwann ein Unterschied festgestellt wird. 

Hier zu sehen ist folgender Vergleich: A=4, B=10 --> A<B

![komp](https://user-images.githubusercontent.com/31915930/38807266-566e660a-417c-11e8-8360-7c0d84e061d8.PNG)

## Befehlszähler

Bei dem Befehlszähler oder Program Counter handelt es sich um einen Binären Counter auf Basis von J-K-Flipflops. Letztere verhalten sich bei einer 1 an beiden Eingängen wie T-Flipflops, können aber wie taktflankengesteuerte S-R-Flipflops einzelnd manipuliert werden. Dadurch können, um Programmsprünge zu ermöglichen, in den Counter beliebige Ausgangswerte geschrieben werden. Der Schaltungsentwurf entstammt komplett eigenständigen Überlegungen auf Basis eines einfachen Binärcounters aus T-Flipflops. 

Hier zu sehen ist der Zählerstand 21. Der rechte Teil ist Abgeschnitten.

![count](https://user-images.githubusercontent.com/31915930/38807272-58681320-417c-11e8-8be7-7984b57e7c0a.PNG)

## Anmerkung

Sofern nicht anders angegeben, eignete ich mir die Schaltungskonzepte aus entsprechender Fachliteratur an und arbeitete anschließend ein eigenes Layout der Schaltnetze aus.
