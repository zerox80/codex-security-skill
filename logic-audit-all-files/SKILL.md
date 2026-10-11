---
name: logic-audit-all-files
description: "Prüft sämtliche Code-Dateien eines Projekts außerhalb von Punkt-Verzeichnissen auf konkrete Logikfehler und berichtet ausschließlich belegte P1- und P2-Findings. Verwenden für vollständige projektweite Logikprüfungen; nicht für reine Diff-Reviews, Stilprüfungen oder eigenständige Security-Audits."
---

# Logikprüfung aller Code-Dateien

Untersuche den gesamten angeforderten Projektumfang auf nachweisbare Fehler im Programmverhalten. Prüfe **alle Code-Dateien außerhalb von Punkt-Verzeichnissen**, nicht nur Änderungen oder ausgewählte Komponenten. Melde ausschließlich bestätigte P1- und P2-Findings. Eine Prüfanforderung allein umfasst keine Fehlerbehebung.

## Umfang und vollständige Abdeckung

- Bestimme die Projektwurzel und den untersuchten Stand einschließlich uncommitteter Änderungen. Ist kein eindeutiges Projekt angegeben oder aus dem Kontext erkennbar, kläre das Ziel.
- **Niemals Ordner betreten, durchsuchen oder ihre Inhalte prüfen, deren Name mit `.` beginnt.** Dieser Ausschluss gilt auf jeder Verzeichnisebene für den gesamten Unterbaum, beispielsweise bei `.git`, `.github`, `.venv` und `src/.cache`, sowie für Inventarisierung, Dateilesen, Kontextrecherche, Suchen, Scanner und Befundverifikation. Schließe diese Bäume vor dem Traversieren aus; umgehe den Ausschluss weder über explizite Dateipfade noch über Symlinks. Punkt-Verzeichnisse zählen nicht als Abdeckungslücken. Alle anderen Ordner, einschließlich Build-Ausgaben, bleiben für die Prüfung ihrer Code-Dateien im Umfang. Ein führender Punkt allein im Dateinamen ist kein Ausschluss, solange die Datei außerhalb solcher Ordner liegt. `.gitignore` und andere Ignore-Regeln begrenzen den verbleibenden Prüfbereich nicht.
- Erstelle ein vollständiges Dateiinventar außerhalb der ausgeschlossenen Ordner, beispielsweise aus der Projektwurzel mit `rg --files --hidden --no-ignore -g '!**/.*/**'`. Git-Tracking und Ignore-Regeln begrenzen den verbleibenden Prüfbereich nicht. Ordne Dateien anhand ihres Inhalts und ihrer Verwendung ein, nicht allein nach Endung.
- Zum Code gehören Quelltexte, Skripte, Tests, Beispiele, Migrationen, Code in Notebooks und Templates sowie ausführbare Build-, CI- und Infrastrukturdefinitionen. Berücksichtige auch generierten und vendorten Code, versteckte Code-Dateien sowie vorhandene Submodule, soweit sie außerhalb der ausgeschlossenen Ordner liegen. Nimm darüber hinaus keine pauschalen Ausschlüsse nach Verzeichnis oder vermeintlicher Wichtigkeit vor.
- Scanne ausschließlich Code-Dateien. Reine Dokumentation, Medien, Datendateien, Lockfiles, Repository-Metadaten und kompilierte Binärartefakte sind keine Prüfziele und gehören nicht in Scannerläufe. Nicht-Code-Dateien dürfen gezielt als Kontext zum Verständnis oder zur Verifikation eines konkreten Code-Befunds gelesen werden; unterziehe sie keinem eigenständigen Audit. Enthält eine Datei Code, erfasse und prüfe den enthaltenen Code.
- Führe Arbeitsnotizen mit Pfad, Prüfstatus und kurzer inhaltlicher Prüfnotiz je Code-Datei. Unterscheide vollständig geprüft, eingeschränkt geprüft und nicht geprüft. Dateiauflistungen, Suchtreffer, Scanner oder bestandene Tests ersetzen keine Inhaltsprüfung.
- Lies jede Code-Datei im zulässigen Prüfbereich vollständig, bei großen Dateien in Abschnitten. Extrahiere bei Containerformaten den gesamten enthaltenen Code mit geeigneten Werkzeugen. Identische Inhalte können gemeinsam analysiert werden; prüfe zusätzlich ihre unterschiedlichen Einbindungskontexte und dokumentiere die Zuordnung.
- Erfasse Symlinks ohne Schleifen. Erweitere den vereinbarten Umfang nicht stillschweigend auf externe Verzeichnisse. Benenne fehlende Submodule, unlesbare Dateien und nicht verfügbare externe Code-Ziele als Abdeckungslücken.
- Arbeite große Projekte in Etappen ab und setze die Prüfung nach gefundenen Fehlern fort. Gleiche abschließend Inventar und Prüfstatus ab. Prüfe zwischenzeitlich geänderte Dateien und betroffene Zusammenhänge erneut. Behaupte vollständige Abdeckung nur, wenn jede Code-Datei im zulässigen Prüfbereich inhaltlich geprüft ist; benenne sonst die verbleibenden Lücken. Nenne den Ausschluss der Punkt-Verzeichnisse kurz im Bericht.

## Logik und Zusammenhänge prüfen

Leite das erwartete Verhalten aus belastbaren Projektverträgen ab: Anforderungen, Schnittstellen, Aufrufern, Datenmodellen und Tests. Kommentare und Tests können selbst fehlerhaft sein; gleiche sie mit dem tatsächlichen Verwendungskontext ab. Erfinde keine Produktanforderungen. Eine ungewöhnliche Implementierung ist noch kein Fehler.

Verfolge Kontroll- und Datenflüsse über Datei- und Komponentengrenzen. Untersuche, soweit vorhanden:

- Verzweigungen, Schleifen, Abbruchbedingungen, Zustandsübergänge und Invarianten;
- Berechnungen, Einheiten, Vorzeichen, Rundung, Indizes, Grenzen sowie leere oder fehlende Werte;
- Persistenz, Transaktionen, Migrationen, Cache-Konsistenz und Zuordnung von Datensätzen;
- asynchrone Abläufe, Nebenläufigkeit, Reihenfolge, Wiederholungen, Idempotenz und Fehlerbehandlung;
- Schnittstellenverträge, Serialisierung, Zeit- und Datumslogik sowie Ressourcenlebenszyklen.

Prüfe auch die Logik von Tests und Hilfscode. Ein fehlerhafter Test kann ein Finding sein, wenn sein konkreter Effekt die P1/P2-Schwelle erreicht; fehlende Testabdeckung allein genügt nicht. Performanceprobleme zählen nur bei belegter funktionaler Auswirkung unter realistischen Bedingungen. Stil, Refactoring-Wünsche, allgemeine Best Practices und rein hypothetische Probleme sind keine Findings. Weite die Aufgabe nicht auf einen separaten Security-Audit aus; ein nachgewiesener Logikfehler darf dennoch eine Sicherheitsauswirkung haben.

## Kandidaten verifizieren

Belege für jeden Kandidaten das erwartete und tatsächliche Verhalten, einen konkreten auslösenden Zustand oder Input, den erreichbaren Ausführungspfad und die relevante Auswirkung. Prüfe aktiv Gegenbelege: Aufrufer, Validierung, vorgeschaltete Bedingungen, Framework-Semantik, Konfiguration, Feature-Schalter und bestehende Tests können den Verdacht widerlegen oder eingrenzen.

Bestätige verbleibende Kandidaten möglichst mit einer gezielten lokalen Reproduktion oder einem aussagekräftigen Test. Ein präziser statischer Nachweis genügt, wenn Ursache, Erreichbarkeit und Auswirkung eindeutig sind; kennzeichne dann die fehlende Laufzeitbestätigung. Führe Projektprogramme und Tests nur mit verstandenen Nebenwirkungen und geeigneten isolierten Daten aus. Verändere im Rahmen der Prüfung keine produktiven oder externen Systeme. Halte temporäre Reproduktionen getrennt von bestehenden Projektdateien.

Verwirf widerlegte Kandidaten. Ungeklärte Annahmen und bloße Verdachtsfälle gehören in Arbeitsnotizen, nicht in die Findings. Erfinde keine Testergebnisse. Prüfe vor der Meldung erneut die konkrete Fundstelle und ob die Priorität durch die belegte Auswirkung gerechtfertigt ist.

## Ausschließlich P1 und P2 berichten

- **P1 – dringend:** Ein nachgewiesener Logikfehler mit schwerwiegender Auswirkung, etwa erheblicher Datenverlust oder Datenkorruption, Ausfall zentraler Abläufe oder weitreichend falsche Ergebnisse unter realistischen Voraussetzungen.
- **P2 – relevant:** Ein nachgewiesener funktionaler Fehler mit begrenzterer Auswirkung oder engeren, realistischen Auslösebedingungen, der behoben werden sollte.

P1 ist in diesem Skill die höchste Berichtsstufe und umfasst auch Auswirkungen, die andere Konventionen als P0 bezeichnen. Benenne kritische Auswirkungen ausdrücklich. Melde keine P0-, P3-, P4- oder rein informativen Findings und stufe geringfügige Fehler nicht künstlich auf P2 hoch.

Sortiere nach P1 und P2 und fasse mehrere Erscheinungsformen derselben Ursache zusammen. Gib je Finding an:

- `[P1]` oder `[P2]` und einen konkreten Titel;
- Datei und eng begrenzte Zeilenstelle, bei Notebooks eine geeignete Zellangabe;
- Auslöser und Voraussetzungen, erwartetes gegenüber tatsächlichem Verhalten sowie Ursache und Auswirkung;
- den Nachweis, einschließlich relevanter Grenzen der Verifikation;
- einen gezielten Behebungsansatz, ohne ungefragt Änderungen vorzunehmen.

Ergänze knapp den geprüften Stand, die Abdeckung als Anzahl geprüfter gegenüber erfassten Code-Dateien und konkrete Einschränkungen. Abdeckungslücken sind keine Findings, müssen aber sichtbar bleiben. Gibt es keine bestätigten Findings, schreibe: „Keine bestätigten P1- oder P2-Logikfehler im geprüften Umfang.“ Leite daraus keine Fehlerfreiheit ab.
