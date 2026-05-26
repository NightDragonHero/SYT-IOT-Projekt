# Projekttitel

## Eventuell Untertitel

Verfasser: **Dominik Höbert, MSc**

Datum: **05.08.2022**

## 1. Einführung

Hier wird in einem Absatz eine kurze (zwei bis drei Sätze) Einführung zum Thema geschrieben. Zum Beispiel kann hier stehen, warum dieses Thema gewählt wurde und warum es wichtig ist. Grundsätzlich ist eine Dokumentation im Passiv zu verfassen. Es ist nicht wichtig, wer etwas getan hat, sondern was getan wurde.

Z. B.:

Es gibt unterschiedliche Methoden, um Daten zu speichern, zum Beispiel in Dateien auf einer Festplatte oder in einem Cloudstorage. Für Webanwendungen oder maschinelle Weiterverarbeitung eignet sich dies eher schlecht, dafür werden zumeist Datenbanken verwendet. In Informationssysteme der 3. Klasse wird sich vor allem mit den relationalen Datenbanken beschäftigt.

## 2. Projektbeschreibung

Hier wird das Projekt und die Ergebnisse kurz (zwei bis drei Sätze) beschrieben.

Z. B.:

Es wurde eine Schaltung realisiert, welche Flip Flop genannt wird. Diese Schaltung hat die Aufgabe, einen Zustand zu speichern. Die Schaltung wurde in Tinkercad simuliert und die Zustände überprüft und dokumentiert.

## 3. Theorie

Die notwendige Theorie, um das Projekt durchführen zu können, wird hier beschrieben. Diese kann auch ausführlicher sein. Hier sollen auch die Fragestellungen ausgearbeitet werden.

## 4. Arbeitsschritt

Die einzelnen Schritte sollen hier genauer beschrieben werden. Mithilfe dieser Dokumentation sollte jeder das Projekt mit demselben Ergebnis nachmachen können. Weitere Arbeitsschritte können hinzugefügt werden.

### Code

Sehr wichtig ist es, den verwendeten Code zu dokumentieren.

```c++
unsigned long myTime;

void setup() {
  Serial.begin(9600);
}

void loop() {
  Serial.print("Time: ");
  myTime = millis();
  Serial.println(myTime); // Gibt die Zeit seit dem Programmstart aus
  delay(1000);            // Eine Sekunde warten, um keine riesigen Datenmengen zu senden
}
```

Dieser Code muss natürlich auch beschrieben und kommentiert werden.

### Bilder und Schaltungen

<img src="https://i.pinimg.com/originals/22/99/fd/2299fd6a63810585136801a720768cde.jpg" alt="10 beste lustige Tierfotos für Freitag" style="zoom:33%;" />

Bilder können URLs aus dem Internet enthalten oder auch lokale Pfade einbinden. Der Lesende muss aber Zugriff auf das Verzeichnis haben, sonst wird nichts angezeigt.

![richtige Schaltung](data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEA...)

Es können auch [Base64 Encodierte Bilder](https://www.base64-image.de/) verwendet werden. Dies hat den Vorteil, dass Bilder direkt in Markdown gespeichert werden.

![schlechtes Beispiel](data:image/jpeg;base64,/9j/4AAQS.....)

Bildausschnitte sollten mit Bedacht gewählt werden. Schaltungen sollten ansprechend gezeichnet und verständlich dargestellt werden. Leitungsfarben können angepasst werden. Die Aussage des Bildes sollte immer auch besprochen werden.

### Tabellen

| Variable                             | n     | M     | SD   | 1      | 2      | 3     | 4     | 5    | 6     | 7    |
| ------------------------------------ | ----- | ----- | ---- | ------ | ------ | ----- | ----- | ---- | ----- | ---- |
| 1. Internal–external status a        | 3,697 | 0.43  | 0.49 | —      |        |       |       |      |       |      |
| 2. Manager job performance           | 2,134 | 3.14  | 0.62 | −.08** | —      |       |       |      |       |      |
| 3. Starting salary b                 | 3,697 | 1.01  | 0.27 | .45**  | −.01   | —     |       |      |       |      |
| 4. Subsequent promotion              | 3,697 | 0.33  | 0.47 | .08**  | .07**  | .04*  | —     |      |       |      |
| 5. Organizational tenure             | 3,697 | 6.45  | 6.62 | −.29** | .09**  | .01   | .09** | —    |       |      |
| 6. Unit service performance c        | 3,505 | 85.00 | 6.98 | −.25** | −.39** | .24** | .08** | .01  | —     |      |
| 7. Unit financial performance c      | 694   | 42.61 | 5.86 | .00    | −.03   | .12*  | −.07  | −.02 | .16** | —    |

Auch die Aussage der Tabelle muss ausformuliert werden.

### Text

*"Eine **Technische Dokumentation** (auch Technikdokumentation oder Produktdokumentation) umfasst alle Informationsprodukte, die ein technisches Erzeugnis beschreiben und zu seiner Nutzung, Wartung oder Reparatur anleiten. Sie bereitet die Informationen systematisch auf und strukturiert sie so, dass der jeweilige Zweck vollständig erfüllt wird."* [5]

Text aus anderen Quellen kann gerne verwendet werden, muss aber immer richtig zitiert werden, da das sonst als Plagiat gewertet wird. Die Aussage des zitierten Textausschnitts sollte auch immer zusätzlich besprochen werden.

## 5. Zusammenfassung

Das Projekt soll hier in wenigen Sätzen zusammengefasst werden. Auch Schwierigkeiten und Fehler bei der Durchführung sind wichtig zu dokumentieren. Wenn es gröbere Probleme gab, sollten diese schon vorher (zum Beispiel in den Arbeitsschritten), kleinere in der Zusammenfassung beschrieben werden.

Im nächsten Abschnitt sind noch die Quellen anzugeben. Alles, was nicht vom Autor selbst erzeugt wurde, ist in einer Dokumentation zu zitieren und in den Quellen anzugeben. Es ist wichtig, immer festzuhalten, woher eine Information stammt, um diese gegebenenfalls nachprüfen zu können. Die meisten Informationen stammen in dieser Zeit aus dem Internet. Hier reicht es aber nicht nur, die URL anzugeben. Titel, Autor und vor allem das Datum, wann die URL das letzte Mal überprüft wurde, sind wichtig anzugeben (da sich Informationen im Internet auch ändern können).

Es gibt viele Standards, richtig zu zitieren; in der Technik wird jedoch der [IEEE Standard](https://ieeeauthorcenter.ieee.org/wp-content/uploads/IEEE-Reference-Guide.pdf) [6] bevorzugt. Wie hier sichtbar ist, wird dazu im Text eine eckige Klammer gesetzt, welche zu den Quellen am Ende führt. Der IEEE Standard ist sehr ausführlich, hier gibt es ein paar [Beispiele](https://pitt.libguides.com/citationhelp/ieee) [7]. Wir können uns das Leben aber mit diesem [Citation Generator](https://www.citethisforme.com/) [8] leichter machen.

## 6. Quellen

[1] Adam-P, “Markdown cheatsheet · Adam-P/markdown-here wiki,” *GitHub*. [Online]. Available: <https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet>. [Accessed: 05-Feb-2022].

[2] GitHub Docs, *Basic writing and formatting syntax - GitHub Docs*. [Online]. Available: <https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-syntax>. [Accessed: 05-Feb-2022].

[3] Markdowntutorial.com, *Markdown Tutorial*. [Online]. Available: <https://www.markdowntutorial.com/>. [Accessed: 05-Feb-2022].

[4] Markdownguide.org, *Markdown Guide*. [Online]. Available: <https://www.markdownguide.org/>. [Accessed: 05-Feb-2022].

[5] De.wikipedia.org, *Technische Dokumentation – Wikipedia*. [Online]. Available: <https://de.wikipedia.org/wiki/Technische_Dokumentation>. [Accessed: 05-Feb-2022].

[6] Ieeeauthorcenter.ieee.org, *IEEE Reference Guide*. [Online]. Available: <https://ieeeauthorcenter.ieee.org/wp-content/uploads/IEEE-Reference-Guide.pdf>. [Accessed: 05-Feb-2022].

[7] Pitt.libguides.com, *Citation Styles: IEEE Style*. [Online]. Available: <https://pitt.libguides.com/citationhelp/ieee>. [Accessed: 05-Feb-2022].

[8] Cite This For Me, *Save Time and Improve your Marks with CiteThisForMe*. [Online]. Available: <https://www.citethisforme.com/>. [Accessed: 05-Feb-2022].
