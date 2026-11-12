# Projektkonzept & Qualitätssicherung

> **Status:** Entwurf zur Freigabe · **Version:** 1.0 · **Projektart:** Schulprojekt / Webanwendung  


## 1. Executive Summary

**HA-Check** ist eine webbasierte Anwendung, mit der IT-Verantwortliche die organisatorischen und technischen Voraussetzungen für Hochverfügbarkeit systematisch prüfen, dokumentieren und priorisieren können. Statt unstrukturierter Tabellen bietet das System nachvollziehbare Checklisten, eine risikoorientierte Auswertung und eine Historie der Prüfergebnisse.

**Wichtige Abgrenzung:** HA-Check misst keine tatsächliche Verfügbarkeit und ersetzt weder Monitoring noch Failover-Tests. Die Anwendung dokumentiert den Stand der Vorsorgemaßnahmen auf Basis von Prüfangaben und Nachweisen.

## 2. Problemstellung und Zielsetzung

In IT-Umgebungen können einzelne Ausfallpunkte (Single Points of Failure), ungetestete Wiederherstellungsprozesse und unvollständige Dokumentation zu längeren Betriebsunterbrechungen führen. HA-Check schafft einen standardisierten Prüfprozess.

### Messbare Projektziele

| ID | Ziel | Abnahmekriterium |
|---|---|---|
| Z-01 | IT-Systeme verwalten | System anlegen, bearbeiten und archivieren möglich |
| Z-02 | Prüfungen durchführen | Checkliste pro System vollständig bearbeitbar |
| Z-03 | Ergebnisse nachvollziehen | Status, Kommentar, Prüfer und Zeitpunkt gespeichert |
| Z-04 | Risiken priorisieren | Kritische offene Prüfpunkte gesondert sichtbar |
| Z-05 | Prüfhistorie bereitstellen | Vergangene Prüfungen unverändert einsehbar |
| Z-06 | Bedienbarkeit sicherstellen | Kernablauf ohne technische Vorkenntnisse nachvollziehbar |

## 3. Projektumfang

### Muss-Anforderungen (MVP)

1. Anmeldung und rollenbasierte Zugriffskontrolle (mindestens Administrator und Prüfer).
2. Verwaltung von Systemen mit Name, Typ, Beschreibung und Kritikalität.
3. Versionierte Checklisten-Vorlagen für mindestens **Server**, **Netzwerk**, **Backup** und **Stromversorgung**.
4. Prüfdurchläufe mit den Zuständen **erfüllt**, **nicht erfüllt**, **nicht anwendbar** und **nicht geprüft**.
5. Kommentarfeld je Prüfpunkt; Nachweise zunächst als Referenztext oder URL.
6. Risikoorientierte Übersicht mit offenen, kritischen Feststellungen.
7. Dauerhafte Speicherung in einer relationalen Datenbank.
8. Automatisierte Tests für zentrale Fachlogik und Zugriffsregeln.

### Kann-Anforderungen

- PDF-Bericht und CSV-Export
- Vergleich zweier Prüfdurchläufe
- Maßnahmenverfolgung mit Verantwortlichen und Fristen
- Erweiterbare Checklisten-Vorlagen über die Benutzeroberfläche

### Nicht Bestandteil des MVP

- Automatische Infrastruktur-Scans und Netzwerkerkennung
- Aktives Failover oder Änderungen an Produktivsystemen
- Echtzeit-Monitoring, SLA-Messung oder Verfügbarkeitsgarantie
- Verarbeitung produktiver Zugangsdaten

## 4. Fachliche Prüfkategorien

| Kategorie | Beispielhafte Kontrollfrage | Bedeutung |
|---|---|---|
| Redundanz | Besteht für kritische Komponenten ein redundanter Betriebsweg? | Vermeidung einzelner Ausfallpunkte |
| Netzwerk | Sind zentrale Verbindungen und Netzwerkgeräte redundant ausgelegt? | Erreichbarkeit bei Teilstörungen |
| Backup | Sind Sicherungen vorhanden, überwacht und gegen unbefugte Änderungen geschützt? | Datenwiederherstellung |
| Recovery | Wurde die Wiederherstellung innerhalb definierter RTO/RPO-Ziele getestet? | Nachgewiesene Wiederanlaufbarkeit |
| Stromversorgung | Gibt es geeignete USV-/Stromkonzepte und dokumentierte Tests? | Schutz bei Stromausfällen |
| Betrieb | Sind Zuständigkeiten, Eskalation und Notfallabläufe dokumentiert? | Organisatorische Reaktionsfähigkeit |

**Bewertungslogik:** Ein nicht erfüllter Punkt erzeugt eine Feststellung. Die Priorität wird anhand von **Systemkritikalität** und **Prüfpunktgewichtung** bestimmt. „Nicht geprüft“ wird als unbekannter Zustand ausgewiesen und nicht als erfüllt gewertet. „Nicht anwendbar“ erfordert eine Begründung. Ein Prozentwert ist lediglich eine Checklisten-Kennzahl, keine Verfügbarkeitsquote.

## 5. Benutzerrollen und Kern-Use-Cases

| Rolle | Berechtigungen | Zentrale Use Cases |
|---|---|---|
| Administrator | Benutzer, Systeme und Vorlagen verwalten | UC-01 System anlegen, UC-02 Vorlage verwalten |
| Prüfer | Zugewiesene Systeme prüfen, Ergebnisse einsehen | UC-03 Prüfung starten, UC-04 Prüfpunkt bewerten |
| Leser (optional) | Ergebnisse ohne Änderung einsehen | UC-05 Prüfbericht ansehen |

### UC-04: Prüfpunkt bewerten

**Vorbedingung:** Prüfer ist angemeldet; ein Prüfdurchlauf wurde geöffnet.  
**Standardablauf:** (1) Prüfpunkt anzeigen → (2) Status auswählen → (3) Kommentar/Nachweis ergänzen → (4) Eingabe validieren → (5) Bewertung speichern → (6) Risikoübersicht aktualisieren.  
**Alternativen:** Bei „nicht anwendbar“ ist eine Begründung erforderlich. Bei ungültiger Eingabe bleibt der bisherige Datenstand erhalten und eine verständliche Fehlermeldung erscheint.  
**Nachbedingung:** Bewertung ist mit Zeitstempel und Benutzerbezug nachvollziehbar gespeichert.


**Vorgesehener Stack:** Python 3, Flask, HTML/CSS/JavaScript, SQLite, pytest, Git/GitHub und Mermaid. SQLite ist für den schulischen Prototyp ausreichend; für einen produktiven Mehrbenutzerbetrieb wäre die Datenbankwahl erneut zu bewerten.


**Datenintegrität:** Prüfergebnisse sollen nach Abschluss nicht stillschweigend überschrieben werden. Vorlagenänderungen dürfen alte Prüfdurchläufe nicht nachträglich verändern; hierfür sind unveränderliche Vorlagenversionen bzw. Snapshots vorzusehen.

## 6. Qualität, Sicherheit und Datenschutz

- Passwörter ausschließlich mit etabliertem Passwort-Hashing speichern; niemals im Klartext.
- Sämtliche Eingaben serverseitig validieren; SQL-Abfragen parametrisieren bzw. ORM verwenden.
- Formulare gegen CSRF schützen; Sitzungs-Cookies sicher konfigurieren.
- Berechtigungen bei jedem geschützten Backend-Zugriff prüfen, nicht nur im Frontend.
- Keine realen Kundendaten oder Zugangsdaten im Repository hinterlegen.
- `.env`, lokale Datenbanken, virtuelle Umgebungen und temporäre Dateien über `.gitignore` ausschließen.
- Fehler nachvollziehbar protokollieren, ohne Passwörter oder vertrauliche Inhalte zu loggen.
- Abhängigkeiten dokumentieren und vor Abgabe auf bekannte Sicherheitsprobleme prüfen.

## 7. Projektorganisation und Rollen

**Planungsannahme:** Vier Mitwirkende; die Aufgabenstellung nennt jedoch maximal drei Personen pro Gruppe. Die Gruppengröße muss daher **vorab von der Lehrkraft genehmigt** werden.

| Verantwortungsbereich | Primäre Zuständigkeit | Nachweis |
|---|---|---|
| Projektkoordination | Planung, Anforderungen, Dokumentation | Projektplan, Issues, Entscheidungen |
| Backend & Datenbank | API/Serverlogik, Datenmodell, Authentifizierung | Code, Migrationen, Tests |
| Frontend & UX | Wireframes, Oberfläche, Benutzerführung | Mockups, Komponenten, UI-Tests |
| Kritische Qualitätssicherung („Skeptiker“) | Annahmen prüfen, Risiken bewerten, Gegenbeispiele testen | Review-Kommentare, Testfälle, Fehlertickets |

Die skeptische Rolle ist **keine passive Beobachterrolle**: Sie besitzt konkrete Liefergegenstände, dokumentiert Einwände sachlich und prüft, ob Maßnahmen die Risiken tatsächlich reduzieren. Entscheidungen erfolgen anhand von Anforderungen, Tests und vereinbarten Abnahmekriterien.

### Zusammenarbeit auf GitHub

- Aufgaben als Issues mit Beschreibung, Verantwortlichem und Akzeptanzkriterien erfassen.
- Features auf separaten Branches entwickeln (`feature/...`, `fix/...`, `docs/...`).
- Änderungen per Pull Request integrieren; mindestens eine zweite Person prüft kritische Änderungen.
- Commit-Nachrichten aussagekräftig formulieren, z. B. `docs: define availability assessment scope`.
- Kanban-Status: **Backlog → In Progress → Review → Done**.
- Entscheidungen und offene Risiken regelmäßig im Team abgleichen.

## 8. Stakeholderanalyse

| Stakeholder | Erwartung | Befürchtung | Gegenmaßnahme |
|---|---|---|---|
| Lehrkräfte | Fachlich fundiertes, dokumentiertes Projekt | Fehlende Nachweise oder unrealistische Planung | Regelmäßige Zwischenstände, dokumentierte Freigaben |
| Projektteam | Faire Arbeitsteilung, funktionierender Prototyp | Zeitdruck, Integrationsprobleme | Klare Zuständigkeiten, frühe Integration |
| Skeptische Projektrolle | Plausible Anforderungen und überprüfbare Qualität | Überambitionierter Umfang, Scheinsicherheit | MVP-Begrenzung, Negativtests, transparente Bewertungslogik |
| Testgruppe | Verständliche, testbare Anwendung | Unklare Abläufe und fehlende Testdaten | Testleitfaden, Beispieldatensatz, Fehlerformular |
| Künftige Anwender | Schnelle Übersicht und nachvollziehbare Befunde | Falsches Sicherheitsgefühl | Klare Hinweise auf Grenzen und offene Prüfpunkte |

## 9. Risiken und Gegenmaßnahmen

| Risiko | Eintritt | Auswirkung | Maßnahme |
|---|---|---|---|
| Gruppengröße nicht genehmigt | Offen | Hoch | Vor Projektstart Lehrkraft befragen |
| Zeitaufwand unterschätzt | Mittel | Hoch | MVP priorisieren, optionale Features zurückstellen |
| Uneinheitliche Schnittstellen | Mittel | Mittel | Datenmodell und Schnittstellen früh festlegen |
| Bewertungslogik irreführend | Mittel | Hoch | Unbekannte Werte getrennt ausweisen, Fachreview |
| Sicherheitsfehler | Mittel | Hoch | Rollen-/Eingabetests, Code-Review |
| Unzureichende Fremdtests | Mittel | Mittel | Testfenster und Testdaten verbindlich reservieren |

## 10. Zeitplanung und Meilensteine

Die Vorgaben nennen **LF11 Block 3–6 mit jeweils ca. 10 Stunden**, zusätzliche Abzüge in **Block 2 und Block 4**, sowie **LF12 mit 22 Stunden pro Woche**, abzüglich benannter Theorie- und Präsentationszeiten. Da sich die Block-2-Angabe nicht eindeutig den verbleibenden LF11-Blöcken zuordnen lässt und die Anzahl der LF12-Unterrichtswochen fehlt, ist **noch keine belastbare Gesamtsumme** möglich.

**Verbindliche Planungsregeln:**

1. Acht Stunden für unvorhergesehene Ereignisse reservieren.
2. Zeit für den Test des zugewiesenen Fremdprojekts berücksichtigen.
3. LF12 Block 1: sechs Stunden Theorie abziehen.
4. LF12 Block 2: vier Stunden Theorie und vier Stunden Präsentationen abziehen.
5. LF12 Block 3: maximal 14 Stunden Entwicklung; acht Stunden Präsentation reservieren.
6. Teamkapazität als **Personenstunden** planen; nicht jede Aufgabe ist beliebig parallelisierbar.

| Meilenstein | Ergebnis | Freigabekriterium |
|---|---|---|
| M1 – Projektfreigabe | Genehmigte Idee, Team und Entwicklungsumgebung | Lehrkraft bestätigt Rahmen |
| M2 – Konzeption | Zeitplan, Stakeholder, Use Cases, Wireframes, UML, ER-Modell | Teamreview abgeschlossen |
| M3 – MVP | Anmeldung, Systeme, Checklisten, Speicherung | Kernablauf funktioniert |
| M4 – Qualitätssicherung | Automatisierte Tests, Fremdtest, Fehlerbehebung | Kritische Fehler behoben |
| M5 – Abgabe | Repository, Dokumentation, Präsentation | Abnahmekriterien geprüft |

## 11. Teststrategie und Abnahme

| Test-ID | Szenario | Erwartetes Ergebnis |
|---|---|---|
| T-01 | System mit gültigen Angaben anlegen | System erscheint in der Übersicht |
| T-02 | Pflichtfeld leer absenden | Verständliche Validierungsfehlermeldung |
| T-03 | Ohne Anmeldung geschützte Seite öffnen | Zugriff wird verweigert oder zur Anmeldung umgeleitet |
| T-04 | Prüfer versucht Admin-Funktion aufzurufen | Serverseitige Autorisierung verweigert Zugriff |
| T-05 | Prüfpunkt als „nicht erfüllt“ markieren | Offene Feststellung wird angezeigt |
| T-06 | Prüfpunkt als „nicht geprüft“ belassen | Ergebnis wird nicht als erfüllt gezählt |
| T-07 | „Nicht anwendbar“ ohne Begründung wählen | Speicherung wird mit Hinweis abgelehnt |
| T-08 | Prüfung abschließen und erneut öffnen | Ergebnisse und Zeitstempel bleiben nachvollziehbar |
| T-09 | Bestehende Vorlage ändern | Abgeschlossene Altprüfung bleibt unverändert |
| T-10 | Ungültige bzw. manipulierte Eingaben senden | Anwendung lehnt Eingabe sicher ab |
