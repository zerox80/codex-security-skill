---
name: security-audit
description: Prüft Repositories und Codeänderungen auf Sicherheitslücken, verifiziert mögliche Findings mehrfach und berichtet ausschließlich bestätigte P1-, P2- und P3-Findings. Verwenden für Security-Audits und Sicherheitsreviews von Code; nicht für reine Stilreviews oder die Implementierung von Fixes.
---

# Security-Audit

Führe ein quellcodegestütztes Security-Audit im angefragten Umfang durch. Qualität und belastbare Belege gehen vor Anzahl der Findings. Antworte in der Sprache des Nutzers.

## Umfang und Arbeitsweise

- Ermittle Ziel, Revision bzw. lokalen Änderungsstand und vorhandene Sicherheitsvorgaben. Frage nur nach, wenn das Ziel aus dem Kontext nicht hervorgeht. Bei einem Diff prüfe die Änderungen samt notwendigem Aufruf- und Schutzkontext; erweitere ihn nicht ungefragt zu einem vollständigen Repository-Audit.
- Erfasse relevante Einstiegspunkte, nicht vertrauenswürdige Eingaben, Berechtigungen und Vertrauensgrenzen. Priorisiere tatsächlich erreichbare Pfade etwa zu Autorisierungsfehlern, Injection, Datenabfluss und Ressourcenmissbrauch entsprechend dem Projekt.
- Arbeite lesend. Eine Audit-Anfrage erlaubt keine Fixes, Veröffentlichungen oder Angriffe auf laufende Systeme. Führe erforderliche Reproduktionen ausschließlich lokal und isoliert mit synthetischen Daten durch; beachte Nebenwirkungen von Tests und Projekt-Skripten.
- Scanner-Ausgaben, verdächtige Muster und Versionshinweise sind Kandidaten, keine bestätigten Findings. Übernimm bei einem ausdrücklich angeforderten anderen Scan-Workflow dessen Ergebnisse als Ausgangsmaterial für die nachfolgenden Prüfungen, ohne ungefragt weitere Cloud-Scans oder Dienste zu starten.

## Jeden Kandidaten dreifach prüfen

Die drei Prüfungen müssen unterschiedliche Belege bzw. Gegenargumente untersuchen. Dreimal dieselbe Behauptung zu formulieren erhöht die Sicherheit nicht.

1. **Angriffspfad belegen.** Verfolge den konkreten Weg vom Angreifereinfluss über die Verarbeitung bis zur sicherheitsrelevanten Wirkung. Lies Aufrufer, Middleware und relevante Konfiguration. Belege Eingabekontrolle, Erreichbarkeit, Voraussetzungen, betroffene Schutzgrenze und Auswirkung an der geprüften Revision. Bei einem Diff kläre, ob die Änderung den Fehler einführt oder verschärft.
2. **Gezielt widerlegen.** Suche aktiv nach Gründen, aus denen der Angriff scheitert: vorherige Autorisierung, wirksame Validierung, sichere Framework-Semantik, Parameterbindung, nachgelagerte Kontrollen, unerreichbarer Code oder fehlende Angreiferrechte. Prüfe vorhandene Tests und den tatsächlichen Deployment-Kontext. Eine fehlende lokale Prüfung ist kein Finding, wenn die Schutzgrenze an anderer Stelle wirksam durchgesetzt wird.
3. **Wirkung gesondert validieren.** Verwende nach Möglichkeit eine minimale isolierte Reproduktion mit einer sinnvollen Negativkontrolle. Wenn Ausführung nicht möglich oder nicht vertretbar ist, prüfe den vollständigen Pfad erneut anhand konkreter Werte und der relevanten Implementierung. Trenne ausgeführte Tests von statischen Schlussfolgerungen. Ungeklärte entscheidende Voraussetzungen verhindern eine Bestätigung.

Nutze, wenn verfügbar und im Kontext erlaubt, einen unabhängigen Subagenten für die Gegenprüfung relevanter Kandidaten. Gib ihm Ziel, Scope, vermuteten Pfad und Rohbelege, aber weder die gewünschte Entscheidung noch eine angeblich bereits bestätigte Bewertung. Verlange eine eigene Prüfung von Erreichbarkeit, Schutzmaßnahmen und Auswirkung. Ohne Subagenten führe die Gegenprüfung selbst durch und behaupte keine unabhängige Bestätigung.

Halte pro Kandidat knappe Arbeitsnotizen zu den drei Prüfergebnissen und seinem Status: bestätigt, widerlegt oder ungeklärt. Diese Notizen dienen der Prüfung und werden nicht als zusätzliche Finding-Liste ausgegeben. Bei Widersprüchen untersuche die entscheidenden Belege gezielt. Bleibt ein Kandidat nach den Prüfungen ungeklärt, berichte ihn nicht als Finding; benenne gegebenenfalls die daraus entstehende Abdeckungslücke. Wiederhole Prüfungen nur bei neuen Belegen oder einem konkret auflösbaren Widerspruch.

## Prioritäten und Aufnahmefilter

Verwende für diesen Bericht ausschließlich die drei Prioritätsstufen:

- **P1:** Schwerwiegende Sicherheitsauswirkung mit realistischem Angriffspfad; dringend beheben. Dazu zählen auch kritische Auswirkungen, wenn ihre Voraussetzungen belegt sind.
- **P2:** Reale, relevante Sicherheitslücke mit begrenzterer Auswirkung oder zusätzlichen belegten Voraussetzungen; regulär beheben.
- **P3:** Reale Sicherheitslücke mit geringer, aber konkret nachweisbarer Auswirkung; nachrangig beheben.

Leite die Priorität aus belegter Auswirkung, Angreiferrechten, Erreichbarkeit und tatsächlichen Voraussetzungen ab. Vermeide theoretische Worst-Case-Einstufungen. P3 ist kein Auffangbecken für Vermutungen oder niedrige Konfidenz. Fehlende Belege führen zum Ausschluss, nicht zur Herabstufung.

Berichte ausschließlich bestätigte Sicherheitslücken. Lasse reine Best-Practice-Empfehlungen, Stilfragen, allgemeine Härtungsvorschläge, rein hypothetische Angriffe und Duplikate weg. Bündele dieselbe Ursache mit mehreren Fundstellen, sofern keine eigenständig zu behebenden Lücken vorliegen. Es gibt keine Mindestzahl an Findings.

## Ergebnis

Nenne kurz geprüften Scope und wesentliche Einschränkungen. Sortiere bestätigte Findings nach P1, P2, P3. Verwende pro Finding:

- **[P1/P2/P3] Konkreter Titel** und genaue Fundstelle mit Datei und Zeile.
- **Angriff und Auswirkung:** Wer kann unter welchen Voraussetzungen welche Sicherheitsgrenze verletzen?
- **Belege und Verifikation:** Konkreter Codepfad, Ergebnis der Gegenprüfung sowie tatsächlich ausgeführte Reproduktion oder ausdrücklich statische Validierung. Keine sensiblen Originaldaten ausgeben.
- **Behebung:** Kurzer, ursachenbezogener Vorschlag; keine automatische Implementierung.

Sind keine Findings bestätigt, sage: „Keine bestätigten P1-, P2- oder P3-Findings im geprüften Umfang.“ Benenne relevante Prüfgrenzen, ohne ungeklärte Kandidaten als Findings aufzuzählen. Behaupte weder Fehlerfreiheit noch vollständige Abdeckung, wenn sie nicht belegt ist.
