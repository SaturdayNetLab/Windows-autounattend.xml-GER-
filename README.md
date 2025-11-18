# Windows-autounattend.xml-GER-
Diese XML wird einfach dem Root Verzeichnis deines Windows Installations USB Stickt beigefügt. 
# Windows 11 Unattended Setup (Autounattend.xml)

Diese `autounattend.xml`-Datei automatisiert die Installation und Erstkonfiguration eines Windows-Betriebssystems (basierend auf der Windows Pro Edition). Das Skript wurde mit Fokus auf deutsche Spracheinstellungen, Benutzerkonten-Erstellung sowie verschiedene Systemhärtungen und Shell-Anpassungen erstellt.

---

## ⚙️ Kern-Konfigurationen

Die folgenden Einstellungen werden in den verschiedenen Phasen der Installation angewendet.

### 1. Sprache und Region (`windowsPE`, `oobeSystem`)

| Einstellung | Wert | Beschreibung |
| :--- | :--- | :--- |
| **Benutzeroberfläche (UI)** | `de-DE` | Stellt die Sprache der Benutzeroberfläche auf Deutsch ein. |
| **Systemgebietsschema** | `de-DE` | Setzt das System-Locale auf Deutsch (Deutschland). |
| **Tastaturlayout** | `0407:00000407` | Deutsches Tastaturlayout. |

### 2. Installation und Lizenzierung (`windowsPE`)

| Einstellung | Wert | Beschreibung |
| :--- | :--- | :--- |
| **EULA akzeptieren** | `true` | Akzeptiert automatisch die Lizenzbedingungen. |
| **Produkt Key** | `VK7JG-NPHTM-C97JM-9MPGT-3V66T` | Generischer Product Key für Windows 10/11 Pro (dient nur zur Installation, nicht zur Aktivierung). |
| **Compact OS** | `false` | Die Funktion **Compact OS** (Systemkomprimierung zur Platzeinsparung) ist **deaktiviert**. |

### 3. Benutzerkonten und Anmeldung (`oobeSystem`)

| Konto | Gruppe | Passwort | AutoLogon |
| :--- | :--- | :--- | :--- |
| **Admin** | `Administrators` | `Willkommen2025!` | Ja (einmalig) |
| **User** | `Users` | `Willkommen2025` | Nein |

*Hinweis: Die automatische Anmeldung wird nach dem ersten Login des `Admin`-Kontos deaktiviert.*

### 4. Out-of-Box-Experience (OOBE)

Die OOBE-Phasen werden weitgehend automatisiert und übersprungen:

* **OOBE-Netzwerk-Bypass:** Wird per Registry-Eintrag aktiviert, um die Netzwerkeinrichtung zu umgehen.
* **EULA/Datenschutz:** Die EULA-Seite und die Express-Einstellungen zum "Schutz des PCs" werden übersprungen oder deaktiviert.
* **Wireless Setup:** Wird während der OOBE angezeigt.

---

## 🔨 Custom-PowerShell-Skripte (Extensions)

Die Konfiguration enthält mehrere eingebettete PowerShell-Skripte, die tiefgreifende Anpassungen an System und Benutzerprofilen vornehmen:

### `Specialize.ps1` (Systemweite Anpassungen)
Wird in der `specialize`-Phase ausgeführt.

* Aktiviert den **OOBE-Netzwerk-Bypass** (`BypassNRO`).
* Setzt die maximale **Passwortgültigkeitsdauer auf unbegrenzt**.
* Führt eine **Systemhärtung** durch (Entfernung von Standardberechtigungen für Nicht-Administratoren auf C:).
* **Deaktiviert News and Interests (Widgets)**.
* **Versteckt die Edge-Erstanwendungserfahrung**.

### `DefaultUser.ps1` (Standard-Benutzerprofil)
Konfiguriert die Registry des Standard-Benutzerprofils (gilt für alle neuen Benutzer).

* **Zeigt Dateierweiterungen** standardmäßig an.
* **Deaktiviert Suchvorschläge** in der Taskleiste.
* Plant das Skript `UserOnce.ps1` zur einmaligen Ausführung beim ersten Login.

### `UserOnce.ps1` (Einmalige Benutzerkonfiguration)
Läuft beim ersten Login für jeden Benutzer und konfiguriert die Shell:

* **Aktiviert das Klassische Kontextmenü** in Windows 11.
* Konfiguriert die angezeigten Ordner in der **Startmenü-Navigation**.
* Startet den Explorer neu, um die Änderungen anzuwenden.

### `FirstLogon.ps1` (Finale Bereinigung)
Läuft einmalig für das AutoLogon-Konto und schließt die Installation ab:

* **Deaktiviert die automatische Anmeldung** für zukünftige Neustarts.
* **Löscht das `Windows.old`-Verzeichnis**.
* **Entfernt Installationsartefakte** (wie die `unattend.xml` und die Skript-Dateien) vom System.
