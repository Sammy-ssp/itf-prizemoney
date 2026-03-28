# PrizeFlow — Officials Module

> **Zweck dieses Dokuments:** Abstimmung mit dem DTSV zu Rollen, Berechtigungen und dem Abrechnungsprozess für Turnieroffizielle. Feedback erwünscht — offene Fragen am Ende.

---

## 1. Überblick

Das Officials-Modul ermöglicht die digitale Abrechnung von Turnieroffiziellen (Schiedsrichter, Oberschiedsrichter etc.) innerhalb von PrizeFlow. Jedes Turnier hat seine eigenen Offiziellen. Der Prozess umfasst:

- **Anmeldung** des Offiziellen zum Turnier (Anreise-/Abreisetag)
- **Rechnungsstellung** nach DTSV-Vorlage (5 Positionen)
- **Unterschrift & Gegenzeichnung** (Official → Supervisor)
- **Auszahlung** durch den Turnierdirektor via SEPA-Überweisung (XML-Export)

---

## 2. Rollen & Berechtigungen

| Berechtigung                                      | Official | Supervisor | Turnierdirektor (TD) | DTSV Admin |
|---------------------------------------------------|:--------:|:----------:|:--------------------:|:----------:|
| Eigene Rechnung ausfüllen (DTSV-Vorlage)          |    ✅    |     ✅     |          —           |     —      |
| Belege hochladen                                  |    ✅    |     ✅     |          —           |     —      |
| Eigene Rechnung e-signieren                       |    ✅    |     ✅     |          —           |     —      |
| Eigene Rechnungen & Profil einsehen               |    ✅    |     ✅     |          —           |     —      |
| Rechnungen anderer Offizieller gegenzeichnen      |    —     |     ✅     |          —           |     ✅      |
| Übersicht aller Offiziellen & Kosten pro Turnier  |    —     |     —      |          ✅          |     ✅     |
| SEPA-XML für Sammelüberweisung herunterladen       |    —     |     —      |          ✅          |     —      |
| Supervisor dem Turnier zuweisen / bestätigen      |    —     |     —      |          -         |     ✅     |
| Tagegeld-Matrix pflegen (Badge-Level × Kategorie) |    —     |     —      |          —           |     ✅     |
| Alle Rechnungen filtern (Kategorie, Datum, Status)|    —     |     —      |          —           |     ✅     |
| Zertifizierungen / Badge-Level verwalten          |    —     |     —      |          —           |     ✅     |
| Richtlinien & Vorlagen für alle Offiziellen setzen|    —     |     —      |          —           |     ✅     |

### Rollenbeschreibung

**Official**
- Füllt die vom DTSV vorgegebene Rechnungsvorlage aus (5 Positionen)
- Lädt Belege hoch und unterschreibt elektronisch
- Kann nur eigene Daten sehen

**Supervisor**
- Ist selbst Official und durchläuft denselben Abrechnungsprozess
- Zusätzlich verantwortlich für die Gegenzeichnung aller anderen Offiziellen-Rechnungen seines Turniers
- Erst nach Supervisor-Unterschrift geht die Rechnung an den TD

**Turnierdirektor (TD)**
- Sieht alle Offiziellen und deren Kosten für sein Turnier
- Lädt SEPA-XML herunter und veranlasst die Sammelüberweisung über sein Bankportal
- Bestätigt oder lehnt Zuweisungen von Offiziellen ab

**DTSV Admin**
- Pflegt die Tagegeld-Matrix (Tagessatz abhängig von Badge-Level und Turnierkategorie)
- Hat Zugriff auf alle Rechnungen über alle Turniere mit Filtermöglichkeiten
- Verwaltet Zertifizierungen und Badge-Level der Offiziellen
- Gibt Richtlinien und Vorlagen vor

---

## 3. Abrechnungs-Workflow

```
┌─────────────────────────────────────────────────────────┐
│  1. Official meldet sich zum Turnier an                 │
│     (Anreisedatum, Abreisedatum)                        │
├─────────────────────────────────────────────────────────┤
│  2. Entwurfsrechnung wird automatisch erstellt          │
│     (5 Positionen, Tagegeld vorausgefüllt)              │
├─────────────────────────────────────────────────────────┤
│  3. Official füllt Rechnung aus                         │
│     → Reisekosten, Verpflegung, Sonstiges eintragen    │
│     → Belege hochladen                                  │
│     → Elektronische Unterschrift                        │
├─────────────────────────────────────────────────────────┤
│  4. Supervisor prüft & gegenzeichnet                    │
│     → Status wechselt zu "approved"                     │
├─────────────────────────────────────────────────────────┤
│  5. Turnierdirektor sieht genehmigte Rechnungen         │
│     → Lädt SEPA-XML (pain.001.001.03) herunter          │
│     → Veranlasst Sammelüberweisung im Bankportal        │
├─────────────────────────────────────────────────────────┤
│  6. Auszahlung erfolgt                                  │
└─────────────────────────────────────────────────────────┘
```

### Status-Übergänge

| Status       | Beschreibung                                           |
|-------------|--------------------------------------------------------|
| `draft`     | Rechnung erstellt, Official füllt aus                   |
| `submitted` | Official hat unterschrieben und eingereicht             |
| `approved`  | Supervisor hat gegengezeichnet                          |
| `paid`      | TD hat Zahlung veranlasst (nach SEPA-Export)            |

---

## 4. Rechnungspositionen (DTSV-Vorlage)

Jede Rechnung enthält genau **5 standardisierte Positionen**:

| Pos. | Bezeichnung             | Berechnung / Eingabe                                                        |
|------|-------------------------|-----------------------------------------------------------------------------|
| 1    | **Tagegeld**            | Automatisch: Anzahl Tage × Tagessatz (aus DTSV-Matrix: Badge-Level × Turnierkategorie) |
| 2    | **Reisekosten**         | Manuell: Betrag, Strecke, Transportmittel (Bahn, PKW, Flug etc.)           |
| 3    | **Verpflegung (ganztags)** | Manuell: Anzahl × Tagessatz                                             |
| 4    | **Verpflegung (halbtags)** | Manuell: Anzahl × Halbtagessatz                                         |
| 5    | **Sonstiges**           | Manuell: Freie Eingabe mit Beleg-Upload                                     |

**Abzug:** Eventuell erhaltene Vorschüsse werden vom Gesamtbetrag abgezogen.

**Gesamtbetrag** = Summe aller Positionen − Vorschuss

---

## 5. Aktueller Implementierungsstand

| Feature                                       | Status         |
|-----------------------------------------------|----------------|
| Official-Onboarding (Profil, Badge, Bank)     | ✅ Fertig       |
| Rechnungseditor (5 Positionen, Belege)        | ✅ Fertig       |
| Elektronische Unterschrift (Zeichnen/Tippen)  | ✅ Fertig       |
| Supervisor-Gegenzeichnung                     | ✅ Fertig       |
| PDF-Generierung (DTSV-Branding)               | ✅ Fertig       |
| SEPA-XML-Export für TD                        | ✅ Fertig       |
| Tagegeld-Matrix (DTSV Admin)                  | ✅ Fertig       |
| TD-Übersicht: Offizielle & Kosten             | ✅ Fertig       |
| DTSV Admin: Rechnungsfilter über alle Turniere| 🔧 In Arbeit   |
| Zertifizierungs-Verwaltung (Badge-Upload)     | ✅ Fertig       |
| Benachrichtigungen (Rechnung eingereicht etc.) | 📋 Geplant     |
| Audit-Log für Unterschriften                  | 📋 Geplant     |

---

## 6. Offene Fragen an den DTSV

> Bitte um Feedback zu folgenden Punkten:

### Rollen & Berechtigungen

1. **Supervisor-Zuweisung:** Wer legt fest, welcher Supervisor für welches Turnier zuständig ist? Der TD oder der DTSV Admin? -> 1 zentraler account für dtb/dtsv
2. **Mehrere Supervisoren:** Kann ein Turnier mehrere Supervisoren haben, oder gibt es immer genau einen? -> 1 supervisor pro turner
4. **TD als Supervisor:** Darf der Turnierdirektor gleichzeitig als Supervisor fungieren? -> nein (bei ITF, national möglich)
5. **DTSV-Admin-Zugriff:** Soll der DTSV Admin Rechnungen bearbeiten/ablehnen können, oder nur lesen? -> nur lesen, kein Eingriff

### Abrechnungsprozess

5. **Vorschüsse:** Wie werden Vorschüsse (Abschlagszahlungen) erfasst? Trägt der TD diese ein? -> ignore
6. **Ablehnungen:** Was passiert, wenn der Supervisor eine Rechnung ablehnt? Zurück an den Official zur Korrektur? -> ja, "ablehnen & Rückgabe zur Korrektur"
7. **Fristen:** Gibt es eine Frist, bis wann der Official seine Rechnung einreichen muss? -> nein
8. **Stornierungen:** Können genehmigte Rechnungen storniert werden? Wenn ja, von wem? -> zwischen supervisor schließt ab und TD zahlt (TD + DTSV admin)

### Tagegeld & Sätze

9. **Verpflegungspauschalen:** Sind die Sätze für Verpflegung (ganztags/halbtags) bundesweit einheitlich oder turnierabhängig? turnierabhängig (Supervisor trägt Satz ein)
10. **Reisekostenpauschale:** Gibt es eine km-Pauschale für PKW-Fahrten, oder werden nur tatsächliche Kosten erstattet? Bahn 2. Klasse, nicht 0,3 pro km, sondern vergleichbare Bahnfahrt, max 175€ (Distanz Haustür zu Anlage) 
11. **Höchstbeträge:** Gibt es Obergrenzen für einzelne Positionen (z.B. max. Reisekosten)? nein 

### Sonstiges

12. **Steuerliche Behandlung:** Sind die Zahlungen an Offizielle umsatzsteuerpflichtig? Müssen Offizielle eine Steuernummer angeben? -> siehe Excel
13. **Datenschutz:** Welche Daten dürfen turnierübergreifend sichtbar sein (DTSV Admin)? -> 
14. **Archivierung:** Wie lange müssen Rechnungen und Belege aufbewahrt werden? -> keine Belege
15. Bahncard Bezuschussung -> Bezuschussungen (siehe Mail)

---

*Dokument erstellt: März 2026 — PrizeFlow Officials Module v1.0*
*Feedback bitte an das PrizeFlow-Team senden.*
