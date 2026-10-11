---
name: security-audit-all-files
description: "Führt vollständige Security-Audits über alle Code-Dateien eines Projekts außerhalb von Punkt-Verzeichnissen durch, prüft jeden Finding-Kandidaten mehrfach und berichtet ausschließlich belegte P0-, P1-, P2- und P3-Findings. Verwenden bei angeforderten vollständigen Sicherheitsprüfungen; nicht für allgemeine Code-Reviews oder reine Diff-Reviews."
---

# Security Audit – alle Code-Dateien

Prüfe das gesamte angegebene Projekt auf konkrete Sicherheitslücken. „ALLE Dateien“ bedeutet in diesem Skill **ALLE Code-Dateien außerhalb von Punkt-Verzeichnissen** im vereinbarten Projektumfang. Die Vorgaben sind vollständige Code-Dateiabdeckung innerhalb dieses Umfangs, mehrfache Gegenprüfung und ausschließlich bestätigte Findings der Prioritäten P0, P1, P2 und P3. Ein Audit allein autorisiert keine Fehlerbehebung oder Veröffentlichung.

## Verbindlicher Verzeichnisausschluss

**Niemals Ordner betreten, durchsuchen oder ihre Inhalte prüfen, deren Name mit `.` beginnt.** Dieser Ausschluss gilt auf jeder Verzeichnisebene für den gesamten Unterbaum, beispielsweise bei `.git`, `.github`, `.venv` und `src/.cache`, sowie für Inventarisierung, Dateilesen, Kontextrecherche, Suchen, Scanner und Befundverifikation. Schließe diese Bäume vor dem Traversieren aus; umgehe den Ausschluss weder über explizite Dateipfade noch über Symlinks. Punkt-Verzeichnisse zählen nicht als Abdeckungslücken. Alle anderen Ordner, einschließlich Build-Ausgaben, bleiben für die Prüfung ihrer Code-Dateien im Umfang. Ein führender Punkt allein im Dateinamen ist kein Ausschluss, solange die Datei außerhalb solcher Ordner liegt. `.gitignore` und andere Ignore-Regeln begrenzen den verbleibenden Prüfbereich nicht. Weise den Ausschluss der Punkt-Verzeichnisse kurz im Bericht aus.

## Vollständige Code-Dateiabdeckung

- Bestimme Projektwurzel und untersuchten Stand einschließlich uncommitteter Änderungen. Fehlt ein eindeutiges Ziel, kläre es, bevor du ein beliebiges Verzeichnis prüfst.
- Erfasse die Dateien innerhalb des vereinbarten Umfangs, um daraus ein vollständiges Code-Inventar zu bilden. Beginne in der erlaubten Projektwurzel beispielsweise mit `rg --files --hidden --no-ignore --glob '!**/.*/**'`. Ergänze die Erfassung für Symlinks und Lesefehler unter Beachtung des Verzeichnisausschlusses. Git-Tracking und Ignore-Regeln begrenzen den übrigen Code-Umfang nicht. Bestimme Code anhand von Inhalt und Verwendung, nicht allein anhand einer Endung.
- Berücksichtige außerhalb von Punkt-Verzeichnissen sämtliche Quellcode-Dateien, Skripte, Tests, Beispiele, Migrationen, ausführbare Notebooks, Templates mit Code sowie Build-, CI/CD- und Infrastrukturcode. Auch Code in Punkt-Dateien, generierter oder vendorter Code und Code in eingebundenen Submodulen gehört innerhalb dieses Umfangs dazu. Keine Stichproben und keine weiteren pauschalen Ausschlüsse von Code nach Verzeichnis, Endung oder vermeintlicher Sicherheitsrelevanz.
- Scanne ausschließlich Code-Dateien. Reine Dokumentation, Medien, Datendateien, Lockfiles, Repository-Metadaten und kompilierte Binärartefakte sind keine Prüfziele und gehören nicht in Scannerläufe. Nicht-Code-Dateien dürfen gezielt als Kontext zum Verständnis oder zur Verifikation eines konkreten Code-Befunds gelesen werden; unterziehe sie keinem eigenständigen Audit. Enthält eine Datei Code, erfasse und prüfe den enthaltenen Code.
- Führe eine Liste aller Code-Dateien mit Bearbeitungsstatus und kurzer Prüfnotiz je Datei. Unterscheide inhaltlich geprüft, eingeschränkt geprüft und nicht prüfbar; halte die Abgrenzung zu Nicht-Code nachvollziehbar fest. Ein Suchtreffer, Scannerlauf oder Blick auf den Dateinamen gilt nicht als vollständige Inhaltsprüfung.
- Lies jede Code-Datei vollständig, bei Bedarf in Abschnitten; nutze für Containerformate wie Notebooks passende, sichere Werkzeuge, um sämtlichen enthaltenen Code zu erfassen. Erkenne identische Inhalte anhand ihres Inhalts und prüfe zusätzlich die unterschiedlichen Einbindungs- und Berechtigungskontexte. Dokumentiere Grenzen bei nicht lesbarem oder nicht zugänglichem Code.
- Erfasse Symlink-Ziele, folge keinen Schleifen und erweitere den vereinbarten Projektumfang nicht stillschweigend auf externe Verzeichnisse. Fehlende Submodule und externe Ziele sind ausdrücklich benannte Abdeckungslücken.
- Arbeite große Projekte in nachvollziehbaren Etappen ab. Beende die Prüfung nicht nach den ersten Findings. Gleiche zum Schluss Code-Inventar und Prüfstatus ab; berücksichtige zwischenzeitliche Dateiänderungen. Behaupte vollständige Code-Abdeckung nur, wenn sie belegt ist.

## Sicherheitsanalyse

Ermittle Einstiegspunkte, Vertrauensgrenzen, Rollen, sensible Daten und Deployment-Annahmen. Verfolge verdächtige Pfade über Dateien und Komponenten hinweg. Prüfe insbesondere Authentifizierung und Autorisierung, Mandantentrennung, Eingabe- und Ausgabeverarbeitung, Injection, Datei- und Netzwerkzugriffe, Deserialisierung, Geheimnisse, Kryptografie, Abhängigkeiten sowie Build- und Deployment-Rechte, soweit im Projekt vorhanden.

Nutze Scanner und Mustersuchen als Hinweise, nicht als Beweis. Verifiziere bei Abhängigkeitsmeldungen Version, maßgebliches Advisory und tatsächliche Betroffenheit; ein Paketname oder CVE-Treffer allein genügt nicht. Leite einen Befund aus dem konkreten Code und seiner Verwendung ab. Fehlende Best Practices, Stilfragen und normale Funktionsfehler ohne Sicherheitsauswirkung sind keine Findings.

## Jeden Kandidaten mehrfach überprüfen

Führe für jeden Kandidaten drei getrennte Prüfungen durch und halte die Ergebnisse in Arbeitsnotizen fest:

1. **Nachweis:** Rekonstruiere den vollständigen Pfad vom kontrollierbaren Eingang bis zur sicherheitsrelevanten Wirkung. Belege betroffene Stellen, Angreiferfähigkeiten, Voraussetzungen und erreichbaren Schaden. Trenne beobachtete Tatsachen von Annahmen.
2. **Widerlegungsversuch:** Lies den Pfad erneut aus der Gegenperspektive. Suche gezielt nach Middleware, Aufruferprüfungen, Berechtigungen, Validierung, Escaping, sicheren Bibliotheksvorgaben, Feature-Schaltern und Deployment-Grenzen, die den Angriff verhindern könnten. Berücksichtige Tests und tatsächliche Konfiguration. Passe den Kandidaten an oder verwerfe ihn, wenn Gegenbelege tragen.
3. **Bestätigung:** Prüfe die verbleibende Behauptung mit einem gezielten, isolierten Test oder einer ungefährlichen lokalen Reproduktion, wenn möglich. Andernfalls liefere einen präzisen statischen Nachweis und benenne die fehlende Laufzeitprüfung. Kontrolliere anschließend Fundstelle, Voraussetzungen, Auswirkung und Priorität erneut. Wiederhole nicht nur dieselbe Suchabfrage.

Ungeklärte Voraussetzungen, nicht nachvollziehbare Datenflüsse oder bloß theoretische Auswirkungen reichen nicht für ein bestätigtes Finding. Behalte solche Kandidaten in den Arbeitsnotizen; stelle sie nicht als erwiesene Lücken dar. Fehlende Testmöglichkeiten allein widerlegen einen klaren statischen Nachweis nicht. Erfinde keine ausgeführten Tests oder Reproduktionen.

Führe projektseitige Programme oder Tests nur nach Prüfung ihrer relevanten Nebenwirkungen aus. Verwende isolierte Testdaten; keine destruktiven Versuche oder Angriffe auf externe beziehungsweise produktive Systeme im Rahmen eines reinen Audits. Gib gefundene Geheimnisse nur maskiert wieder.

## Prioritäten und Bericht

Verwende für diesen Skill ausschließlich diese vier Berichtsstufen:

- **P0 – kritisch:** Nachgewiesene katastrophale, weitreichende Sicherheitswirkung ohne besondere Angriffsvoraussetzungen, etwa eine unmittelbar ausnutzbare vollständige Systemkompromittierung. Erfordert sofortige Behebung.
- **P1 – hoch:** Nachgewiesene schwerwiegende Sicherheitswirkung, die dringend behoben werden sollte, etwa weitreichender unberechtigter Zugriff, Codeausführung oder erheblicher Datenabfluss unter realistischen Voraussetzungen.
- **P2 – mittel:** Konkrete relevante Sicherheitslücke mit begrenzterem Schaden oder zusätzlichen realistischen Angriffsvoraussetzungen.
- **P3 – niedrig:** Belegte Sicherheitslücke mit geringer, aber tatsächlicher Auswirkung. Keine rein vorsorglichen Härtungsvorschläge.

P0 ist hier die höchste Berichtsstufe. Benenne kritische Auswirkungen ausdrücklich. Bewerte Priorität anhand von Wirkung und realistischen Voraussetzungen, nicht allein anhand eines Scanner-Scores.

Sortiere bestätigte Findings nach P0, P1, P2, P3 und fasse Duplikate derselben Ursache zusammen. Gib je Finding an:

- `[P0|P1|P2|P3]` und einen konkreten Titel;
- genaue Datei und eng begrenzte Zeilenstelle, bei Binärformaten eine passende Fundstellenangabe;
- Ursache, erreichbarer Angriffspfad, Voraussetzungen und Sicherheitswirkung;
- Nachweis sowie Ergebnis des Widerlegungsversuchs und der abschließenden Bestätigung, einschließlich relevanter Validierungsgrenzen;
- eine gezielte Empfehlung zur Behebung, ohne ungefragt Änderungen vorzunehmen.

Ergänze knapp den geprüften Stand, die Code-Dateiabdeckung und konkrete Einschränkungen. Abdeckungslücken sind keine Findings und müssen dennoch sichtbar bleiben. Berichte keine unbestätigten Verdachtsfälle, informativen Befunde oder zusätzlichen Prioritätsklassen. Gibt es keine bestätigten Findings, sage: „Keine bestätigten P0-, P1-, P2- oder P3-Findings im geprüften Umfang.“ Leite daraus keine garantierte Sicherheit ab.
