---
lab:
  title: Implementieren von Microsoft Dev Box
  module: Implement Developer Self-Service
---

# Lab 01 – Implementieren von Microsoft Dev Box

## Geschätzte Zeit: 60 Minuten

## Voraussetzungen

- Ein Microsoft Entra-Mandant mit 3 vorab erstellten Benutzerkonten (und optional 3 vorab erstellten Microsoft Entra-Gruppen), die 3 verschiedene Rollen in Microsoft Dev Box-Bereitstellungen repräsentieren. Aus Gründen der Übersichtlichkeit stimmen die Benutzer- und Gruppennamen in den Lab-Anweisungen mit den Informationen in der folgenden Tabelle überein:

| Benutzer              | Group                        | Role                  |
| ----------------- | ---------------------------- | --------------------- |
| platformegineer01 | DevCenter_Platform_Engineers | Plattformtechniker*in     |
| devlead01         | DevCenter_Dev_Leads          | Leitung des Entwicklungsteams |
| devuser01         | DevCenter_Dev_Users          | Entwickler             |

- Ein Azure-Abonnement zum Hosten von Microsoft Dev Box-Ressourcen, die dem Microsoft Entra-Mandanten zugeordnet sind, der die Benutzer- und Gruppenkonten hostet
- Ein Microsoft Intune-Abonnement, das demselben Microsoft Entra-Mandanten zugeordnet ist wie das Azure-Abonnement
- Eine Microsoft Intune-Lizenz, die den drei vorab erstellten Microsoft Entra-Benutzerkonten zugewiesen ist
- Sie benötigen ein GitHub-Konto und ein Repository.
  - Zum Erstellen eines GitHub-Kontos folgen Sie den Anweisungen im Artikel [Erstellen eines Kontos auf GitHub](https://docs.github.com/get-started/quickstart/creating-an-account-on-github).
  - Um ein GitHub-Repository zu erstellen, befolgen Sie die Anweisungen im Artikel [Erstellen eines neuen Repositorys](https://docs.github.com/repositories/creating-and-managing-repositories/creating-a-new-repository).
- Ein GitHub-Repository, das als Fork von https://github.com/microsoft/devcenter-catalog erstellt wurde
- Ein GitHub-Repository, das als Fork von https://github.com/MicrosoftLearning/contoso-co-eShop erstellt wurde

## Ziele

Nach Abschluss dieses Workshops sind Sie zu Folgendem in der Lage:

- Implementieren einer Microsoft Dev Box-Umgebung
- Anpassen einer Microsoft Dev Box-Umgebung

## Übung 1: Implementieren einer Microsoft Dev Box-Umgebung

In dieser Übung nutzen Sie eine Reihe von Features, die von Microsoft bereitgestellt werden, um eine Microsoft Dev Box-Umgebung zu implementieren. Dieser Ansatz konzentriert sich auf die Minimierung des Aufwands für die Erstellung einer funktionsfähigen Self-Service-Lösung für Fachkräfte in der Entwicklung.

Die Übung umfasst die folgenden Aufgaben:

- Aufgabe 1: Erstellen eines Dev Centers
- Aufgabe 2: Überprüfen der Dev Center-Einstellungen
- Aufgabe 3: Erstellen einer Dev Box-Definition
- Aufgabe 4: Erstellen eines Projekts
- Aufgabe 5: Erstellen eines Dev Box-Pools
- Aufgabe 6: Konfigurieren von Berechtigungen
- Aufgabe 7: Auswerten einer Dev Box

### Aufgabe 1: Erstellen eines Dev Centers

In dieser Aufgabe erstellen Sie ein Azure Dev Center, das während der ersten Übung dieses Labs verwendet wird. Ein Dev Center ist ein Plattform-Engineering-Service, der die Erstellung und Verwaltung skalierbarer, vorkonfigurierter Entwicklungs- und Bereitstellungsumgebungen zentralisiert und so die Zusammenarbeit und Ressourcennutzung für Softwareentwicklungsteams optimiert.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zum Azure-Portal unter `https://portal.azure.com`.
1. Wenn Sie zur Authentifizierung aufgefordert werden, melden Sie sich mit Ihrem Microsoft Entra-Benutzerkonto an.
1. Suchen Sie im Azure-Portal im Textfeld **Suchen** nach **`Dev centers`** und wählen Sie es aus.
1. Wählen Sie auf der Seite **Dev Centers** die Option **+ Erstellen** aus.
1. Legen Sie auf der Registerkarte **Grundlagen** der Seite **Dev Center erstellen** die folgenden Einstellungen fest und wählen Sie dann **Weiter: Einstellungen**:

   | Einstellung                                                                 | Wert                                                        |
   | ----------------------------------------------------------------------- | ------------------------------------------------------------ |
   | Subscription                                                            | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden |
   | Resource group                                                          | Der Name einer **neuen** Ressourcengruppe **rg-devcenter-01**     |
   | Name                                                                    | **devcenter-01**                                             |
   | Location                                                                | **(USA) USA, Osten**                                             |
   | Schnellstartkatalog anhängen – Definitionen der Azure-Bereitstellungsumgebung | Aktiviert                                                      |
   | Schnellstartkatalog anhängen – Dev Box-Anpassungsaufgaben              | Disabled                                                     |

   > **Hinweis:** In der zweiten Übung dieses Labs werden Sie einen Katalog mit aktivierten Anpassungsaufgaben konfigurieren.

1. Legen Sie auf der Registerkarte **Einstellungen** der Seite **Dev Center erstellen** die folgenden Einstellungen fest und wählen Sie dann **Überprüfen + Erstellen**:

   | Einstellung                                    | Wert   |
   | ------------------------------------------ | ------- |
   | Kataloge pro Projekt aktivieren                | Aktiviert |
   | Von Microsoft gehostetes Netzwerk in Projekten zulassen | Aktiviert |
   | Azure Monitor-Agent-Installation aktivieren    | Aktiviert |

   > **Hinweis:** Standardmäßig sind Ressourcen aus Katalogen, die dem Dev Center zugeordnet sind, für alle Projekte verfügbar. Die Einstellung **Kataloge pro Projekt aktivieren** ermöglicht es, zusätzliche Kataloge auch an beliebig ausgewählte Projekte anzuhängen.

   > **Hinweis:** Dev Boxes können entweder mit einem virtuellen Netzwerk in Ihrer eigenen Azure-Abonnement oder einem von Microsoft gehosteten Netzwerk verbunden werden, je nachdem, ob sie mit Ressourcen in Ihrer Umgebung kommunizieren müssen. Wenn dies nicht erforderlich ist, können Sie durch Aktivieren der Einstellung **Von Microsoft gehostetes Netzwerk in Projekten zulassen** die Option zum Verbinden von Dev Boxes mit einem von Microsoft gehosteten Netzwerk aktivieren, wodurch der Verwaltungs- und Konfigurationsaufwand effektiv minimiert wird.

   > **Hinweis:** Die Einstellung **Azure Monitor-Agent-Installation aktivieren** triggert automatisch die Installation des Azure Monitor-Agents auf allen Dev Boxes im Dev Center.

1. Warten Sie auf der Registerkarte **Überprüfen + Erstellen**, bis die Überprüfung abgeschlossen ist und wählen Sie dann **Erstellen**.

   > **Hinweis:** Warten Sie, bis das Projekt bereitgestellt wird. Die Projekterstellung kann etwa 1 Minute dauern.

1. Wählen Sie auf der Seite **Bereitstellung ist abgeschlossen** die Option **Zur Ressource wechseln**.

### Aufgabe 2: Überprüfen der Dev Center-Einstellungen

In dieser Aufgabe überprüfen Sie die grundlegende Konfigurationseinstellung des Dev Centers, das Sie in der vorherigen Aufgabe erstellt haben.

1. Erweitern Sie in dem Webbrowser, der das Azure-Portal anzeigt, auf der Seite **devcenter-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Umgebungskonfiguration** und wählen Sie **Kataloge**.
1. Auf der Seite **devcenter-01 \|Kataloge** sehen Sie, dass das Dev Center mit dem Katalog **quickstart-environment-definitions** konfiguriert ist, der auf das GitHub-Repository `https://github.com/microsoft/devcenter-catalog.git` verweist.
1. Überprüfen Sie, ob die Spalte **Status** den Eintrag **Synchronisierung erfolgreich** enthält. Wenn dies nicht der Fall ist, verwenden Sie die folgende Abfolge von Schritten, um den Katalog neu zu erstellen:

   1. Aktivieren Sie das Kontrollkästchen neben dem automatisch generierten Katalogeintrag **quickstart-environment-definitions** und wählen Sie dann in der Symbolleiste **Löschen**.
   1. Auf der Seite **devcenter-01 \| Kataloge** wählen Sie **+ Hinzufügen**.
   1. Im Bereich **Katalog hinzufügen** geben Sie im Textfeld **Name** **`quickstart-environment-definitions-fix`** ein, im Abschnitt **Katalogspeicherort** wählen Sie **GitHub**, im Feld **Authentifizierungstyp**, wählen Sie **GitHub-App**, lassen Sie das Kontrollkästchen **Diesen Katalog automatisch synchronisieren** aktiviert und wählen Sie dann **Mit GitHub anmelden**.
   1. Geben Sie im Fenster **Mit GitHub anmelden** die GitHub-Anmeldedaten ein und wählen Sie **Anmelden**.

      > **Hinweis:** Diese GitHub-Zugangsdaten ermöglichen Ihnen den Zugriff auf ein GitHub-Repository, das als Kopie von <https://github.com/microsoft/devcenter-catalog> erstellt wurde.

   1. Wenn Sie dazu aufgefordert werden, wählen Sie im Fenster **Microsoft DevCenter autorisieren** die Option **Microsoft DevCenter autorisieren**.
   1. Zurück im Bereich **Katalog hinzufügen**, wählen Sie in der Dropdown-Liste **Repository** die Option **devcenter-catalog**, in der Dropdownliste **Branch** akzeptieren Sie den Eintrag **Standardbranch**, in der Option **Ordnerpfad** geben Sie **`Environment-Definitions`** ein und wählen dann **Hinzufügen**.
   1. Überprüfen Sie auf der Seite **devcenter-01 \|Kataloge**, ob die Synchronisierung erfolgreich abgeschlossen wurde, indem Sie den Eintrag in der Spalte **Status** beobachten.

1. Wählen Sie auf der Seite **devcenter-01 \|Kataloge** den Eintrag **quickstart-environment-definitions-fix**.
1. Auf der Seite **quickstart-environment-definitions-fix** sehen Sie sich die Liste der vordefinierten Umgebungsdefinitionen an.

   > **Hinweis:** Jeder Eintrag stellt eine Definition einer Azure-Bereitstellungsumgebung dar, die in einem entsprechenden Unterordner des Ordners **Environment-Definitions** des GitHub-Repositorys `https://github.com/microsoft/devcenter-catalog.git` definiert ist.

   > **Hinweis:** Eine Bereitstellungsumgebung ist eine Sammlung von Azure-Ressourcen, die in einer Vorlage definiert sind, die als Umgebungsdefinition bezeichnet wird. Fachkräfte in der Entwicklung können diese Definitionen nutzen, um eine Infrastruktur einzurichten, die als Host für ihre Lösungen dient. Weitere Informationen zu Azure-Bereitstellungsumgebungen erfahren Sie im Microsoft Learn-Artikel [Was sind Azure-Bereitstellungsumgebungen?](https://learn.microsoft.com/azure/deployment-environments/overview-what-is-azure-deployment-environments)

### Aufgabe 3: Erstellen einer Dev Box-Definition

In dieser Aufgabe erstellen Sie eine Dev Box-Definition. Der Zweck besteht darin, das Betriebssystem, die Tools, die Einstellungen und die Ressourcen zu definieren, die als Blaupause für die Erstellung konsistenter und maßgeschneiderter Entwicklungsumgebungen (sogenannte Dev Boxes) dienen.

1. Erweitern Sie in dem Webbrowser, der das Azure-Portal anzeigt, auf der Seite **devcenter-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Dev Box-Konfiguration** und wählen Sie **Dev Box-Definitionen**.
1. Wählen Sie auf der Seite **devcenter-01 \| Dev Box-Definitionen** die Option **+ Erstellen**.
1. Legen Sie auf der Seite **Dev Box-Definition erstellen** die folgenden Einstellungen fest und wählen Sie dann **Erstellen**:

   | Einstellung            | Wert                                                                                |
   | ------------------ | ------------------------------------------------------------------------------------ | ----------------------- |
   | Name               | **devbox-definition-01**                                                             |
   | Abbildung              | \*\*Visual Studio 2022 Enterprise unter Windows 11 Enterprise + Microsoft 365 Apps 24H2 | Ruhezustand unterstützt\*\* |
   | Imageversion      | **Neueste**                                                                           |
   | Compute            | **8 vCPU, 32 GB RAM**                                                                |
   | Storage            | **256 GB SSD**                                                                       |
   | Ruhezustand aktivieren | Aktiviert                                                                              |

   > **Hinweis:** Warten Sie, bis die Dev Box-Definition erstellt wurde. Das sollte weniger als eine Minute dauern.

### Aufgabe 4: Erstellen eines Dev Center-Projekts

In dieser Aufgabe erstellen Sie ein Dev Center-Projekt. Ein Dev-Center-Projekt entspricht in der Regel einem Entwicklungsprojekt innerhalb Ihrer Organisation. Sie können z. B. ein Projekt für die Entwicklung einer Branchenanwendung und ein anderes Projekt für die Entwicklung der Unternehmenswebsite erstellen. Alle Projekte in einem Dev Center verwenden dieselben Dev-Box-Definitionen, Netzwerkverbindung, Kataloge und Computekataloge. Sie können das Erstellen mehrerer Dev Center-Projekte in Betracht ziehen, wenn Sie über mehrere Entwicklungsprojekte verfügen, die über separate Projektadministrierende und Zugriffsberechtigungsanforderungen verfügen.

1. Erweitern Sie in dem Webbrowser, der das Azure-Portal anzeigt, auf der Seite **devcenter-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Verwaltung** und wählen Sie **Projekte**.
1. Auf der Seite **devcenter-01 \| Projekte** wählen Sie **+ Erstellen**.
1. Legen Sie auf der Registerkarte **Grundlagen** der Seite **Ein Projekt erstellen** die folgenden Einstellungen fest und wählen Sie dann **Weiter: Dev Box-Verwaltung**:

   | Einstellung        | Wert                                                        |
   | -------------- | ------------------------------------------------------------ |
   | Subscription   | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden |
   | Resource group | **rg-devcenter-01**                                          |
   | Dev Center     | **devcenter-01**                                             |
   | Name           | **devcenter-project-01**                                     |
   | Beschreibung    | **devcenter-project-01**                                     |

1. Legen Sie auf der Registerkarte **Dev Box-Verwaltung** der Seite **Ein Projekt erstellen** die folgenden Einstellungen fest und wählen Sie dann **Weiter: Kataloge**:

   | Einstellung                 | Wert |
   | ----------------------- | ----- |
   | Dev Box-Grenzwerte aktivieren   | Ja   |
   | Dev Boxes pro Entwickler | **2** |

1. Auf der Registerkarte **Kataloge** der Seite **Projekt erstellen** legen Sie die folgenden Einstellungen fest und wählen dann **Prüfen + Erstellen**:

   | Einstellung                            | Wert   |
   | ---------------------------------- | ------- |
   | Definitionen der Bereitstellungsumgebung | Aktiviert |
   | Imagedefinitionen                  | Aktiviert |

   > **Hinweis:** Da Sie beim Erstellen des Dev Centers Kataloge auf Projektebene aktiviert haben, bestimmen diese Einstellungen, welche Katalogelemente bei der Synchronisierung in das Projekt importiert werden.

1. Auf der Registerkarte **Prüfen + Erstellen** der Seite **Dev Center erstellen**, wählen Sie **Erstellen**:

   > **Hinweis:** Warten Sie darauf, dass das Projekt erstellt wird. Das sollte weniger als eine Minute dauern.

1. Wählen Sie auf der Seite **Bereitstellung ist abgeschlossen** die Option **Zur Ressource wechseln**.

### Aufgabe 5: Erstellen eines Dev Box-Pools

In dieser Aufgabe erstellen Sie einen Dev Box-Pool im Dev Center-Projekt, das Sie im vorherigen Vorgang erstellt haben. Dev Box-Pools werden von Benutzenden von Dev Boxes zum Erstellen von Dev Boxes verwendet. Ein Dev-Box-Pool verknüpft eine Dev-Box-Definition mit einer Netzwerkverbindung. Im Allgemeinen können Sie zwischen von Microsoft gehosteten Verbindungen und Ihren eigenen Azure-Netzwerkverbindungen wählen. Die Netzwerkverbindung bestimmt den Standort einer Dev Box und deren Zugriff auf andere Cloud- und lokale Ressourcen. Erwägen Sie, einen Dev-Box-Pool mit einer Netzwerkverbindung zu erstellen, die den Dev-Box-Benutzern am nächsten liegt. Um die Betriebskosten für Dev Boxes zu senken, können Sie einen Dev Box-Pool so konfigurieren, dass diese täglich zu einer festgelegten Zeit heruntergefahren werden.

1. Erweitern Sie in dem Webbrowser, der das Azure-Portal anzeigt, auf der Seite **devcenter-project-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Verwalten** und wählen Sie **Dev Box-Pools**.
1. Auf der Seite **devcenter-project-01 \| Dev Box-Pools** wählen Sie **+ Erstellen**.
1. Legen Sie auf der Registerkarte **Grundlagen** der Seite **Erstellen eines Dev Box-Pools** die folgenden Einstellungen fest und wählen Sie dann **Erstellen**:

   | Einstellung                                                                                                           | Wert                                    |
   | ----------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
   | Name                                                                                                              | **devcenter-project-01-devbox-pool-01**  |
   | Definition                                                                                                        | **devbox-definition-01**                 |
   | Netzwerkverbindung                                                                                                | **In ein von Microsoft gehostetes Netzwerk bereitstellen** |
   | Region                                                                                                            | **(USA) USA, Osten**                         |
   | Einmaliges Anmelden aktivieren                                                                                             | Aktiviert                                  |
   | Erstellerberechtigungen für Dev Box                                                                                        | **Lokaler Admin**                  |
   | Automatisches Beenden im Zeitplan aktivieren                                                                                      | Aktiviert                                  |
   | Endzeit                                                                                                         | **19:00 Uhr**                             |
   | Zeitzone                                                                                                         | Ihre aktuelle Zeitzone                   |
   | Ruhezustand beim Trennen aktivieren                                                                                    | Aktiviert                                  |
   | Karenzzeit in Minuten                                                                                           | **60**                                   |
   | Ich bestätige, dass meine Organisation über Lizenzen für Azure Hybrid Benefits verfügt, die für alle Dev Boxes in diesem Pool gelten. | Aktiviert                                  |

   > **Hinweis:** Warten Sie darauf, dass der Dev Box-Pool erstellt wird. Dies kann etwa zwei Minuten dauern.

### Aufgabe 6: Konfigurieren von Berechtigungen

In dieser Aufgabe weisen Sie den drei Microsoft Entra-Sicherheitsprinzipalen, die in Ihrer Lab-Umgebung eingerichtet wurden, geeignete Microsoft Dev-Box-bezogene Berechtigungen zu. Diese Sicherheitsprinzipien entsprechen den typischen Rollen im Bereich Plattform-Engineering:

| Benutzer              | Group                        | Role                  |
| ----------------- | ---------------------------- | --------------------- |
| platformegineer01 | DevCenter_Platform_Engineers | Plattformtechniker*in     |
| devlead01         | DevCenter_Dev_Leads          | Leitung des Entwicklungsteams |
| devuser01         | DevCenter_Dev_Users          | Entwickler             |

Microsoft Dev Box nutzt die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf Funktionen auf Projektebene zu steuern. Technische Fachkräfte sollten die volle Kontrolle über die Erstellung und Verwaltung von Entwicklungszentren, ihren Katalogen und Projekten haben. Hierfür benötigen Sie die Besitzer- oder Teilnehmerrolle, je nachdem, ob Sie auch die Möglichkeit benötigen, Berechtigungen an andere zu delegieren. Entwicklungsteamleitenden sollte die Rolle „Projektadministrator“ für das Dev Center zugewiesen werden, die die Ausführung administrativer Aufgaben in Microsoft Dev Box-Projekten ermöglicht. Dev Box-Benutzende müssen in der Lage sein, ihre eigenen Dev Boxes zu erstellen und zu verwalten, die mit der Rolle „Dev Box-Benutzer“ verknüpft sind.

> **Hinweis:** Zunächst weisen Sie der Microsoft Entra-Gruppe, die die Benutzerkonten der technischen Fachkräfte enthalten soll, Berechtigungen zu.

1. Navigieren Sie in dem Webbrowser, der das Azure-Portal anzeigt, zur Seite **devcenter-01** und wählen Sie im vertikalen Navigationsmenü auf der linken Seite **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie auf der Seite **devcenter-01 \| Zugriffssteuerung (IAM)** die Option **+ Hinzufügen** und in der Dropdownliste die Option **Rollenzuweisung hinzufügen**.
1. Auf der Registerkarte **Rolle** der Seite **Rollenzuweisung hinzufügen** wählen Sie die Registerkarte **Privilegierte Administratorrolle**, in der Liste der Rollen wählen Sie **Besitzer** und wählen schließlich **Weiter**.
1. Stellen Sie auf der Registerkarte **Mitglieder** der Seite **Rollenzuweisung hinzufügen** sicher, dass die Option **Benutzer, Gruppe oder Dienstprinzipal** ausgewählt ist und klicken Sie auf **+ Mitglieder auswählen**.
1. Suchen Sie im Bereich **Mitglieder auswählen** nach **`DevCenter_Platform_Engineers`** und klicken Sie dann auf **Auswählen**.
1. Zurück auf der Registerkarte **Mitglieder** der Seite **Rollenzuweisung hinzufügen** **Weiter** auswählen.
1. Wählen Sie auf der Registerkarte **Bedingungen** der Seite **Rollenzuweisung hinzufügen** im Abschnitt **Was Benutzer tun können** die Option **Benutzer alle Rollen zuweisen lassen (hoch privilegiert)** und wählen Sie dann **Weiter**.
1. Auf der Registerkarte **Überprüfen + zuweisen** der Seite **Rollenzuweisung hinzufügen** **Überprüfen + zuweisen** auswählen.

   > **Hinweis:** Als Nächstes weisen Sie der Microsoft Entra-Gruppe Berechtigungen zu, die Entwicklungsteam-Leadbenutzerkonten enthalten sollen.

1. Kehren Sie zur Seite **devcenter-01 \| Zugriffssteuerung (IAM)** zurück, erweitern Sie im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Verwalten**, wählen Sie **Projekte** und wählen Sie in der Liste der Projekte **devcenter-project-01** aus.
1. Wählen Sie auf der Seite **devcenter-project-01** im vertikalen Navigationsmenü auf der linken Seite **Zugriffssteuerung (IAM)**.
1. Wählen Sie auf der Seite **devcenter-project-01 \| Zugriffssteuerung (IAM)** die Option **+ Hinzufügen** und in der Dropdownliste die Option **Rollenzuweisung hinzufügen**.
1. Stellen Sie auf der Registerkarte **Rolle** der Seite **Rollenzuweisung hinzufügen** sicher, dass die Registerkarte **Stellenfunktionsrollen** ausgewählt ist, wählen Sie in der Liste der Rollen **DevCenter Projekt-Admin** und wählen Sie **Weiter**.
1. Stellen Sie auf der Registerkarte **Mitglieder** der Seite **Rollenzuweisung hinzufügen** sicher, dass die Option **Benutzer, Gruppe oder Dienstprinzipal** ausgewählt ist und klicken Sie auf **+ Mitglieder auswählen**.
1. Suchen Sie im Bereich **Mitglieder auswählen** nach **`DevCenter_Dev_Leads`** und klicken Sie dann auf **Auswählen**.
1. Zurück auf der Registerkarte **Mitglieder** der Seite **Rollenzuweisung hinzufügen** **Weiter** auswählen.
1. Auf der Registerkarte **Überprüfen + zuweisen** der Seite **Rollenzuweisung hinzufügen** **Überprüfen + zuweisen** auswählen.

   > **Hinweis:** Zuletzt weisen Sie der Microsoft Entra-Gruppe, die die Entwicklerbenutzerkonten enthalten soll, Berechtigungen zu.

1. Zurück auf der Seite **devcenter-project-01 \| Zugriffssteuerung (IAM)** wählen Sie **+ Hinzufügen** und in der Dropdown-Liste wählen Sie **Rollenzuweisung hinzufügen** aus.
1. Vergewissern Sie sich auf der Registerkarte **Rolle** der Seite **Rollenzuweisung hinzufügen**, dass die Registerkarte **Stellenfunktionsrollen** ausgewählt ist, wählen Sie in der Liste der Rollen **DevCenter Dev Box-Benutzer** und wählen Sie **Weiter**.
1. Stellen Sie auf der Registerkarte **Mitglieder** der Seite **Rollenzuweisung hinzufügen** sicher, dass die Option **Benutzer, Gruppe oder Dienstprinzipal** ausgewählt ist und klicken Sie auf **+ Mitglieder auswählen**.
1. Suchen Sie im Bereich **Mitglieder auswählen** nach **`DevCenter_Dev_Users`** und klicken Sie dann auf **Auswählen**.
1. Zurück auf der Registerkarte **Mitglieder** der Seite **Rollenzuweisung hinzufügen** **Weiter** auswählen.
1. Auf der Registerkarte **Überprüfen + zuweisen** der Seite **Rollenzuweisung hinzufügen** **Überprüfen + zuweisen** auswählen.

### Aufgabe 7: Auswerten einer Dev Box

In dieser Aufgabe bewerten Sie eine Dev Box-Funktionalität mithilfe eines Microsoft Entra-Entwicklerbenutzerkontos.

1. Starten Sie einen Webbrowser im Inkognito-/In-Private-Modus und navigieren Sie zum Microsoft Dev Box-Entwicklerportal unter `https://aka.ms/devbox-portal`.
1. Wenn Sie zur Anmeldung aufgefordert werden, geben Sie die Anmeldedaten des Benutzerkontos **devuser01** ein.
1. Wählen Sie auf der Seite **Willkommen, devuser01** des Microsoft Dev Box-Entwicklerportals die Option **+ Neue Dev Box**.
1. Geben Sie im Bereich **Dev Box hinzufügen** in das Textfeld **Name** **`devuser01box01`** ein.
1. Prüfen Sie weitere Informationen, die im Fenster **Dev Box hinzufügen** angezeigt werden, z.B. den Projektnamen, die Spezifikationen des Dev Box-Pools, den Status der Ruhezustand-Unterstützung und den geplanten Zeitpunkt des Herunterfahrens. Beachten Sie außerdem die Option zum Anwenden von Anpassungen und den Hinweis, dass die Erstellung der Entwicklungsumgebung bis zu 65 Minuten dauern kann.

   > **Hinweis:** Dev Box-Namen müssen innerhalb eines Projekts eindeutig sein.

1. Wählen Sie im Bereich **Dev Box hinzufügen** die Option **Erstellen**.

   > **Hinweis:** Warten Sie nicht, bis die Dev Box erstellt wurde. Fahren Sie stattdessen direkt mit der nächsten Übung dieses Labs fort. Erwägen Sie jedoch, zu dieser Übung zurückzukehren und den Rest dieser Aufgabe zu durchlaufen, sobald Sie die nächste Übung abgeschlossen haben.

1. Sobald die Dev Box vollständig eingerichtet ist und läuft, verbinden Sie sich mit ihr, indem Sie die Option **Über App verbinden** wählen.

   > **Hinweis:** Die Verbindung zu einer Dev Box kann über eine Remotedesktop Windows-Anwendung, einen Remotedesktop Client (mstsc.exe) oder direkt in einem Webbrowser-Fenster hergestellt werden.

1. Wählen Sie in dem Popupfenster mit dem Titel **Diese Seite versucht, das Microsoft Remote Connection Center zu öffnen** die Option **Öffnen**. Dadurch wird automatisch eine Remotedesktopsitzung in die Dev Box initiiert.
1. Wenn Sie zur Eingabe Ihrer Anmeldedaten aufgefordert werden, authentifizieren Sie sich mit dem Benutzernamen und dem Kennwort des Kontos **devuser01**.
1. Überprüfen Sie in der Remotedesktopsitzung in der Dev Box, ob die Konfiguration eine Installation von Visual Studio 2022- und Microsoft 365-Apps enthält.

   > **Hinweis:** Sie können die Dev Box direkt aus dem Microsoft Dev Box-Entwicklerportal als Dev-Benutzer herunterfahren, indem Sie zunächst das Ellipsen-Symbol in der Oberfläche **Ihre Dev Box** auswählen und dann **Herunterfahren** aus dem überlappenden Menü auswählen. Alternativ können Sie als technische Fachkraft oder Führungskraft eines Entwicklungsteams den Lebenszyklus der Dev Boxen im Abschnitt **Dev Box-Pools** des entsprechenden Dev Center-Projekts steuern.

## Übung 2: Anpassen einer Microsoft Dev Box-Umgebung

In dieser Übung passen Sie die Funktionalität der Microsoft Dev Box-Umgebung an. Dieser Ansatz konzentriert sich auf den Umfang der Änderungen, die Sie bei der Implementierung einer benutzerdefinierten Self-Service-Lösung für Fachkräfte in der Entwicklung vornehmen können.

Die Übung umfasst die folgenden Aufgaben:

- Aufgabe 1: Erstellen einer Azure Compute Gallery und Anfügen an das Dev Center
- Aufgabe 2: Konfigurieren der Authentifizierung und Autorisierung für Azure Image Builder
- Aufgabe 3: Erstellen eines benutzerdefinierten Images mithilfe von Azure VM Image Builder
- Aufgabe 4: Erstellen einer Azure Dev Center-Netzwerkverbindung
- Aufgabe 5: Hinzufügen von Imagedefinitionen zu einem Azure Dev Center-Projekt
- Aufgabe 6: Erstellen eines benutzerdefinierten Dev Box-Pools
- Aufgabe 7: Auswerten einer benutzerdefinierten Dev Box

### Aufgabe 1: Erstellen einer Azure Compute Gallery und Anfügen an das Dev Center

In dieser Aufgabe erstellen Sie eine Azure Compute Galerie und fügen sie dem Dev Center hinzu, das Sie in der vorherigen Übung dieses Labs erstellt haben. Eine Galerie ist ein Speicherort in einem Azure-Abonnement, mit dem Sie eine Struktur und Organisation für benutzerdefinierte Images erstellen können. Nachdem Sie eine Compute Galery an ein Dev Center angehängt und mit Images gefüllt haben, können Sie Dev Box-Definitionen auf der Grundlage der in der Compute Galery gespeicherten Images erstellen.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zum Azure-Portal unter `https://portal.azure.com`.
1. Wenn Sie zur Authentifizierung aufgefordert werden, melden Sie sich mit Ihrem Microsoft-Konto an.
1. Suchen Sie im Azure-Portal im Textfeld **Suchen** nach **`Azure compute galleries`** und wählen Sie es aus.
1. Wählen Sie auf der Seite **Azure Compute Galleries** die Option **+ Erstellen**.
1. Geben Sie auf der Registerkarte **Allgemeine Informationen** auf der Seite **Azure Compute Gallery erstellen** die folgenden Einstellungen an und wählen Sie dann **Weiter: Freigabemethode** aus:

   | Einstellung        | Wert                                                        |
   | -------------- | ------------------------------------------------------------ |
   | Subscription   | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden |
   | Resource group | **rg-devcenter-01**                                          |
   | Name           | **compute_gallery_01**                                       |
   | Region         | **(USA) USA, Osten**                                             |

1. Stellen Sie auf der Registerkarte **Freigabemethode** der Seite **Azure Compute Gallery erstellen** sicher, dass die Option **Rollenbasierte Zugriffssteuerung (RBAC)** ausgewählt ist und wählen Sie dann **Überprüfen + Erstellen**:
1. Warten Sie auf der Registerkarte **Überprüfen + Erstellen**, bis die Überprüfung abgeschlossen ist und wählen Sie dann **Erstellen**.

   > **Hinweis:** Warten Sie, bis das Projekt bereitgestellt wird. Die Erstellung von Azure Compute Gallery sollte weniger als eine Minute dauern.

1. Suchen Sie im Azure-Portal nach **`Dev centers`** und wählen Sie es aus. Wählen Sie auf der Seite **Dev Centers** die Option **devcenter-01** aus.
1. Erweitern Sie auf der Seite **devcenter-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Dev Box-Konfiguration** und wählen Sie **Azure Compute Galleries**.
1. Auf der Seite **devcenter-01 \| Azure Compute Galleries** wählen Sie **+ Hinzufügen**.
1. Wählen Sie im Bereich **Azure Compute Gallery hinzufügen** in der Dropdown-Liste **Gallery** die Option **compute_gallery_01** und wählen Sie dann **Hinzufügen**.

   > **Hinweis:** Wenn Sie folgende Fehlermeldung erhalten: „_Dieses Dev Center hat keine vom System oder vom Benutzer zugewiesene Identität. Galerien können erst hinzugefügt werden, wenn eine Identität zugewiesen wurde._“, dann müssen Sie dem Dev Center eine vom System zugewiesene Identität zuweisen.
   > Wählen Sie dazu im Azure-Portal auf der Seite **devcenter-01** im vertikalen Navigationsmenü auf der linken Seite unter Einstellungen die Option **Identität**, legen Sie auf der Registerkarte **System zugewiesen** den Schalter **Status** auf **Ein** fest und wählen Sie dann **Speichern**.

### Aufgabe 2: Konfigurieren der Authentifizierung und Autorisierung

In dieser Aufgabe erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität, die von Azure Image Builder verwendet wird, um Images zur Azure Compute Gallery hinzuzufügen, die Sie in der vorherigen Aufgabe erstellt haben. Außerdem konfigurieren Sie die erforderlichen Berechtigungen, indem Sie eine benutzerdefinierte rollenbasierte Zugriffssteuerungsrolle (RBAC) erstellen und der verwalteten Identität zuweisen. Auf diese Weise können Sie Azure Image Builder in der nächsten Aufgabe verwenden, um ein benutzerdefiniertes Image zu erstellen.

1. Wählen Sie im Azure-Portal das Symbol **Cloud Shell** in der Symbolleiste, um den Bereich Cloud Shell zu öffnen. Wählen Sie bei Bedarf **Zu PowerShell wechseln**, um eine PowerShell-Sitzung zu starten, und klicken Sie im Dialogfeld **Zu PowerShell in Cloud Shell wechseln** auf **Bestätigen**.

   > **Hinweis:** Wenn Sie die Cloud Shell zum ersten Mal öffnen, wählen Sie im Dialogfeld **Willkommen in der Azure Cloud Shell** die Option **PowerShell**, im Bereich **Erste Schritte** die Option **Kein Speicherkonto erforderlich** und in der Dropdownliste **Abonnement** den Namen des Azure-Abonnements aus, das Sie in diesem Lab verwenden.

1. Führen Sie in der PowerShell-Sitzung des Cloud Shell-Bereichs die folgenden Befehle aus, um sicherzustellen, dass alle erforderlichen Ressourcenanbieter registriert sind:

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
   Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
   Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
   Register-AzResourceProvider -ProviderNamespace Microsoft.Network
   ```

1. Führen Sie den folgenden Befehl aus, um die erforderlichen PowerShell-Module zu installieren (wenn Sie dazu aufgefordert werden, geben Sie **A** ein und drücken die **Entertaste**):

   ```powershell
   'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
   ```

1. Führen Sie die folgenden Befehle aus, um Variablen festzulegen, auf die während des gesamten Image-Erstellungsprozesses Bezug genommen werden soll:

   ```powershell
   $currentAzContext = Get-AzContext
   # the target Azure subscription ID
   $subscriptionID=$currentAzContext.Subscription.Id
   # the target Azure resource group name
   $imageResourceGroup='rg-devcenter-01'
   # the target Azure region
   $location='eastus'
   # the reference name assigned to the image created by using the Azure Image Builder service
   $runOutputName="aibWinImg01"
   # image template name
   $imageTemplateName="templateWinVSCode01"
   # the Azure compute gallery name
   $computeGallery = 'compute_gallery_01'
   ```

1. Führen Sie die folgenden Befehle aus, um eine benutzerseitig zugewiesene verwaltete Identität zu erstellen (VM Image Builder verwendet die von Ihnen angegebene Benutzeridentität, um Images in der Azure Compute Gallery zu speichern):

   ```powershell
   # Install the Azure PowerShell module to support AzUserAssignedIdentity
   Install-Module -Name Az.ManagedServiceIdentity
   # Generate a pseudo-random integer to be used for resource names
   $timeInt=$(get-date -UFormat "%s")

   # Create an identity
   $identityName='identityAIB' + $timeInt
   New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Location $location
   $identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
   $identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
   ```

1. Führen Sie die folgenden Befehle aus, um der neu erstellten benutzerseitig zugewiesene verwaltete Identität die erforderlichen Berechtigungen zum Speichern von Images in der Ressourcengruppe **rg-devcenter-01** zu erteilen:

   ```powershell
   # Set variables
   $imageRoleDefName = 'Custom Azure Image Builder Image Def' + $timeInt
   $aibRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
   $aibRoleImageCreationPath = 'aibRoleImageCreation.json'

   # Customize the role definition file
   Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace '<subscriptionID>', $subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

   # Create a role definition
   New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

   # Assign the role to the VM Image Builder user-assigned managed identity within the scope of the **rg-devcenter-01** resource group
   New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
   ```

### Aufgabe 3: Erstellen eines benutzerdefinierten Images mithilfe von Azure VM Image Builder

In dieser Aufgabe erstellen Sie mit Azure Image Builder ein benutzerdefiniertes Image basierend auf einer vorhandenen Azure Resource Manager (ARM)-Vorlage, die ein Windows 11 Enterprise-Image mit automatisch installiertem Choco und Visual Studio Code definiert. Der Azure VM Image Builder vereinfacht die Definition und Bereitstellung von VM-Images erheblich. Es basiert auf einer Imagekonfiguration, die Sie angeben, um eine automatisierte Imageerstellungspipeline zu konfigurieren. Anschließend können Fachkräfte in der Entwicklung diese Images für die Bereitstellung ihrer Dev Boxes verwenden.

1. Führen Sie in der PowerShell-Sitzung des Cloud Shell-Fensters die folgenden Befehle aus, um eine Image-Definition zu erstellen, die der Azure Compute Galery hinzugefügt werden soll, die Sie in der ersten Aufgabe dieser Übung erstellt haben:

   ```powershell
   # ensure that the image definition security type property is set to 'TrustedLaunch'
   $securityType = @{Name='SecurityType';Value='TrustedLaunch'}
   $features = @($securityType)
   # Image definition name
   $imageDefName = 'imageDefDevBoxVSCode01'

   # Create the image definition
   New-AzGalleryImageDefinition -GalleryName $computeGallery -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'Contoso' -Offer 'vscodedevbox' -Sku '1-0-0' -Feature $features -HyperVGeneration 'V2'
   ```

   > **Hinweis:** Ein Dev Box-Image muss eine Reihe von Anforderungen erfüllen, einschließlich der Verwendung von Generation 2, Hyper-V v2 und Windows 10 oder 11 Enterprise, Version 20H2 oder höher. Die vollständige Liste finden Sie im Microsoft Learn-Artikel [Konfigurieren der Azure Compute Gallery für Microsoft Dev Box](https://learn.microsoft.com/en-us/azure/dev-box/how-to-configure-azure-compute-gallery).

1. Führen Sie die folgenden Befehle aus, um eine leere Datei mit dem Namen „template.json“ zu erstellen, die eine ARM-Vorlage enthält, die ein Windows 11 Enterprise-Image mit automatisch installiertem Choco und Visual Studio Code definiert:

   ```powershell
   Set-Location -Path ~
   $templateFile = 'template.json'
   Set-Content -Path $templateFile -Value ''
   ```

1. Verwenden Sie in der PowerShell-Sitzung von Cloud Shell den Nano-Text-Editor, um der neu erstellten Datei den folgenden Inhalt hinzuzufügen:

   > **Anmerkung:** Um den Texteditor nano zu öffnen, führen Sie den Befehl `nano ./template.json` aus. Um die Änderungen zu speichern und den Nano-Texteditor zu verlassen, drücken Sie **Strg + X**, dann **Y** und anschließend die **Eingabetaste**.

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "imageTemplateName": {
         "type": "string"
       },
       "api-version": {
         "type": "string"
       },
       "svclocation": {
         "type": "string"
       }
     },
     "variables": {},
     "resources": [
       {
         "name": "[parameters('imageTemplateName')]",
         "type": "Microsoft.VirtualMachineImages/imageTemplates",
         "apiVersion": "[parameters('api-version')]",
         "location": "[parameters('svclocation')]",
         "dependsOn": [],
         "tags": {
           "imagebuilderTemplate": "win11multi",
           "userIdentity": "enabled"
         },
         "identity": {
           "type": "UserAssigned",
           "userAssignedIdentities": {
             "<imgBuilderId>": {}
           }
         },
         "properties": {
           "buildTimeoutInMinutes": 100,
           "vmProfile": {
             "vmSize": "Standard_DS2_v2",
             "osDiskSizeGB": 127
           },
           "source": {
             "type": "PlatformImage",
             "publisher": "MicrosoftWindowsDesktop",
             "offer": "Windows-11",
             "sku": "win11-21h2-ent",
             "version": "latest"
           },
           "customize": [
             {
               "type": "PowerShell",
               "name": "Install Choco and Vscode",
               "inline": [
                 "Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))",
                 "choco install -y vscode"
               ]
             }
           ],
           "distribute": [
             {
               "type": "SharedImage",
               "galleryImageId": "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>",
               "runOutputName": "<runOutputName>",
               "artifactTags": {
                 "source": "azureVmImageBuilder",
                 "baseosimg": "win11multi"
               },
               "replicationRegions": ["<region1>", "<region2>"]
             }
           ]
         }
       }
     ]
   }
   ```

1. Führen Sie die folgenden Befehle aus, um Platzhalter in der template.json durch die für Ihre Azure-Umgebung spezifischen Werte zu ersetzen:

   ```powershell
   $replRegion2 = 'eastus2'
   $templateFilePath = '.\template.json'
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<subscriptionID>', $subscriptionID | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<rgName>', $imageResourceGroup | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<runOutputName>', $runOutputName | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<imageDefName>', $imageDefName | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<sharedImageGalName>', $computeGallery | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<region1>', $location | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<region2>', $replRegion2 | Set-Content -Path $templateFilePath
   ((Get-Content -Path $templateFilePath -Raw) -Replace '<imgBuilderId>', $identityNameResourceId) | Set-Content -Path $templateFilePath
   ```

1. Führen Sie den folgenden Befehl aus, um die Vorlage an den Azure Image Builder-Dienst zu übermitteln (der Dienst verarbeitet die übermittelte Vorlage, indem er alle abhängigen Artefakte, z. B. Skripts, herunterlädt und sie in einer Staging-Ressourcengruppe speichert, deren Name das Präfix **IT\_** enthält, um ein benutzerdefiniertes Image für einen virtuellen Computer zu erstellen:

   ```powershell
   New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -Api-Version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location
   ```

1. Führen Sie den folgenden Befehl aus, um den Image-Erstellungsprozess aufzurufen:

   ```powershell
   Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
   ```

1. Führen Sie den folgenden Befehl aus, um den Status der Imagebereitstellung zu ermitteln:

   ```powershell
   Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup | Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
   ```

   > **Hinweis:** Die folgende Ausgabe gibt an, dass der Buildprozess erfolgreich abgeschlossen wurde:

   ```powershell
   Name                LastRunStatusRunState LastRunStatusMessage ProvisioningState
   ----                --------------------- -------------------- -----------------
   templateWinVSCode01 Succeeded                                  Succeeded
   ```

1. Verwenden Sie alternativ zum Überwachen des Buildfortschritts das folgende Verfahren:

   1. Suchen Sie im Azure-Portal nach **`Image templates`** und wählen Sie es aus.
   1. Auf der Seite **Imagevorlagen** wählen Sie **templateWinVSCode01** aus.
   1. Beachten Sie auf der Seite **templateWinVSCode01** im Abschnitt **Essentials** den Wert des Eintrags **Buildausführungsstatus**.

   > **Hinweis:** Der Buildvorgang kann etwa 30 Minuten dauern. Warten Sie nicht auf die Fertigstellung, sondern fahren Sie mit der nächsten Aufgabe dieser Übung fort.

1. Sobald der Vorgang abgeschlossen ist, suchen Sie im Azure-Portal nach **`Azure compute galleries`** und wählen es aus.
1. Wählen Sie auf der Seite **Azure Compute Galleries** den Eintrag **compute_gallery_01**.
1. Stellen Sie auf der Seite **compute_gallery_01** sicher, dass die Registerkarte **Definitionen** ausgewählt ist und wählen Sie in der Liste der Definitionen **imageDefDevBoxVSCode**.
1. Wählen Sie auf der Seite **imageDefDevBoxVSCode** die Registerkarte **Versionen** und überprüfen Sie, ob der Eintrag **1.0.0 (neueste Version)** in der Liste erscheint und der **Bereitstellungsstatus** auf **Erfolgreich** gesetzt ist.
1. Wählen Sie den Eintrag **1.0.0 (neueste Version)** aus.
1. Überprüfen Sie auf der Seite **1.0.0 (compute_gallery_01/imageDefDevBoxVSCode/1.0.0)** die Einstellungen für die VM-Image-Version.

### Aufgabe 4: Erstellen einer Azure Dev Center-Netzwerkverbindung

In dieser Aufgabe konfigurieren Sie das Azure Dev Center-Netzwerk für die Verwendung in einem Szenario, das private Konnektivität mit Ressourcen erfordert, die in einem virtuellen Azure-Netzwerk gehostet werden. Im Gegensatz zum von Microsoft gehosteten Netzwerk, das Sie in der ersten Übung dieses Labs verwendet haben, unterstützen virtuelle Netzwerkverbindungen auch Hybridszenarien (Bereitstellung von Konnektivität zu lokalen Ressourcen) und die hybride Microsoft Entra-Verknüpfung von Azure-Entwicklungsrechnern (zusätzlich zur Unterstützung für die Microsoft Entra-Verknüpfung).

1. Suchen Sie in dem Webbrowser, der das Azure-Portal anzeigt, im Textfeld **Suchen** nach **`Virtual networks`** und wählen Sie es aus.
1. Wählen Sie auf der Seite **Virtuelle Netzwerke** die Option **+ Erstellen** aus.
1. Legen Sie auf der Registerkarte **Grundlagen** der Seite **Virtuelles Netzwerk erstellen** die folgenden Einstellungen fest und wählen Sie anschließend **Weiter**:

   | Einstellung        | Wert                                                        |
   | -------------- | ------------------------------------------------------------ |
   | Subscription   | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden |
   | Resource group | **rg-devcenter-01**                                          |
   | Name           | **vnet-01**                                                  |
   | Location       | **(USA) USA, Osten**                                             |

1. Auf der Registerkarte **Sicherheit** der Seite **Virtuelles Netzwerk erstellen** legen Sie die vorhandenen Einstellungen fest, ohne die Standardwerte zu ändern, und wählen dann **Weiter**.
1. Überprüfen Sie auf der Registerkarte **IP-Adressen** der Seite **Virtuelles Netzwerk erstellen** die vorhandenen Einstellungen, ohne deren Standardwerte zu ändern, und wählen Sie dann **Überprüfen + Erstellen**.
1. Auf der Registerkarte **Überprüfen + Erstellen** der Seite **Virtuelles Netzwerk erstellen** wählen Sie **Erstellen** aus.

   > **Hinweis:** Warten Sie, bis das virtuelle Netzwerk erstellt wurde. Das sollte weniger als eine Minute dauern.

1. Suchen Sie im Azure-Portal im Textfeld **Suchen** nach **`Network connections`** und wählen Sie es aus.
1. Wählen Sie auf der Seite **Netzwerkverbindungen** die Option **+ Erstellen** aus.
1. Geben Sie auf der Registerkarte **Grundlagen** der Seite **Netzwerkverbindung erstellen** die folgenden Einstellungen an und wählen Sie dann **Überprüfen + Erstellen**:

   | Einstellung         | Wert                                                        |
   | --------------- | ------------------------------------------------------------ |
   | Subscription    | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden |
   | Resource group  | **rg-devcenter-01**                                          |
   | Name            | **network-connection-vnet-01**                               |
   | Virtuelles Netzwerk | **vnet-01**                                                  |
   | Subnet          | **default**                                                  |

1. Auf der Registerkarte **Überprüfen + Erstellen** der Seite **Virtuelles Netzwerk erstellen** wählen Sie **Erstellen** aus.

   > **Hinweis:** Warten Sie, bis die Netzwerkverbindung hergestellt ist. Dies kann etwa eine Minute dauern.

1. Suchen Sie im Azure-Portal nach **`Dev centers`** und wählen Sie es aus. Wählen Sie auf der Seite **Dev Centers** die Option **devcenter-01** aus.
1. Erweitern Sie auf der Seite **devcenter-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Dev Box-Konfiguration** und wählen Sie **Netzwerk**.
1. Auf der Seite **devcenter-01 \| Netzwerk** wählen Sie **+ Hinzufügen**.
1. Wählen Sie im Bereich **Netzwerkverbindung hinzufügen** in der Dropdownliste **Netzwerkverbindung** die Option **network-connection-vnet-01** und wählen Sie dann **Hinzufügen**.

   > **Hinweis:** Warten Sie nicht, bis die Netzwerkverbindung erstellt wurde, sondern fahren Sie stattdessen mit der nächsten Aufgabe fort. Das Hinzufügen einer Netzwerkverbindung kann etwa 1 Minute dauern.

### Aufgabe 5: Hinzufügen von Imagedefinitionen zu einem Azure Dev Center-Projekt

In dieser Aufgabe fügen Sie Imagedefinitionen zu einem Azure Dev Center-Projekt hinzu, das Sie in der ersten Übung dieses Labs erstellt haben. Imagedefinitionen kombinieren einen Azure Marketplace oder ein benutzerdefiniertes Image mit konfigurierbaren Aufgaben, die zusätzliche Änderungen definieren, die auf das zugrunde liegende Image angewendet werden sollen. Eine Imagedefinition kann verwendet werden, um ein neues Image zu erstellen (das alle Änderungen enthält, einschließlich der durch Aufgaben angewendeten) oder um Dev Box-Pools direkt zu erstellen. Das Erstellen eines wiederverwendbaren Images minimiert den Zeitaufwand für die Bereitstellung von Dev Boxes.

Um die Imageerstellung für Microsoft Dev Box-Teamanpassungen zu konfigurieren, müssen Kataloge auf Projektebene aktiviert sein (die Sie bereits in der ersten Übung dieses Labs abgeschlossen haben). In dieser Aufgabe konfigurieren Sie die Katalogsynchronisierungseinstellungen für das Projekt. Dies umfasst das Anfügen eines Katalogs, der Imagedefinitionsdateien enthält.

1. Suchen Sie in dem Webbrowser, der das Azure-Portal anzeigt, im Textfeld **Suchen** nach **`Dev centers`** und wählen Sie es aus.
1. Auf der Seite **Dev Centers** wählen Sie **devcenter-01**.
1. Erweitern Sie auf der Seite **devcenter-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Verwalten** und wählen Sie **Projekte**.
1. Wählen Sie auf der Seite **devcenter-01 \| Projekte** in der Liste der Projekte **devcenter-project-01**.
1. Erweitern Sie auf der Seite **devcenter-project-01** im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Einstellungen** und wählen Sie **Kataloge**.
1. Auf der Seite **devcenter-project-01 \| Kataloge** wählen Sie **+ Hinzufügen**.
1. Im Bereich **Katalog hinzufügen** geben Sie im Textfeld **Name** **`image-definitions-01`** ein, im Abschnitt **Katalogquelle** wählen Sie **GitHub**, im Feld **Authentifizierungstyp**, wählen Sie **GitHub-App**, lassen Sie das Kontrollkästchen **Diesen Katalog automatisch synchronisieren** aktiviert und wählen Sie dann **Mit GitHub anmelden**.
1. Wenn Sie dazu aufgefordert werden, geben Sie im Fenster **Mit GitHub anmelden** Ihre GitHub-Anmeldedaten ein und wählen **Anmelden**.

   > **Hinweis:** Sie müssen das Repository https://github.com/MicrosoftLearning/contoso-co-eShop in Ihr GitHub-Konto verzweigen, bevor Sie diesen Schritt ausführen können.

1. Wenn Sie dazu aufgefordert werden, wählen Sie im Fenster **Microsoft DevCenter autorisieren** die Option **Microsoft DevCenter autorisieren**.
1. Zurück im Bereich **Katalog hinzufügen**, wählen Sie in der Dropdown-Liste **Repo** die Option **contoso-co-eShop**, in der Dropdownliste **Branche** akzeptieren Sie den Eintrag **Standardbranche**, in der Zeile **Ordnerpfad** geben Sie **`.devcenter/catalog/image-definitions`** ein und wählen dann **Hinzufügen**.
1. Überprüfen Sie auf der Seite **devcenter-project-01 \|Kataloge**, ob die Synchronisierung erfolgreich abgeschlossen wurde, indem Sie den Eintrag in der Spalte **Status** beobachten.
1. Wählen Sie den Link **Synchronisierung erfolgreich** in der Spalte **Status**, sehen Sie sich den daraus resultierenden Benachrichtigungsbereich an, überprüfen Sie, ob 3 Objekte zum Katalog hinzugefügt wurden, und schließen Sie den Bereich, indem Sie das Symbol **x** in der oberen rechten Ecke wählen.
1. Zurück auf der Seite **devcenter-project-01 \|Kataloge**, wählen Sie **image-definitions-01** und überprüfen Sie, ob sie drei Einträge mit den Namen **ContosoBaseImageDefinition**, **backend-eng** und **frontend-eng** enthält.

   > **Hinweis:** In diesem Lab testen Sie die Funktionalität der Image-Definition **frontend-eng**, die den folgenden Inhalt hat:

   ```yaml
   $schema: "1.0"
   name: "frontend-eng"
   # Using the "Windows 11 Enterprise 24H2" image as the base
   image: microsoftwindowsdesktop_windows-ent-cpc_win11-24H2-ent-cpc
   description: "This definition is for the eShop frontend engineering environment"

   tasks:
     - name: ~/winget
       description: Install Visual Studio Code
       parameters:
         package: Microsoft.VisualStudioCode
         runAsUser: true
   ```

1. Navigieren Sie im Azure-Portal zurück zur Seite **devcenter-project-01**, erweitern Sie im vertikalen Navigationsmenü auf der linken Seite den Abschnitt **Verwalten**, wählen Sie **Imagedefinitionen** und überprüfen Sie, ob auf der Seite dieselben 3 Imagedefinitionen angezeigt werden, die Sie zuvor in dieser Aufgabe identifiziert haben.

### Aufgabe 6: Erstellen eines benutzerdefinierten Dev Box-Pools

In dieser Aufgabe verwenden Sie die neu bereitgestellten Imagedefinitionen, um einen Dev Box-Pool zu erstellen. Der Pool verwendet auch die Netzwerkverbindung, die Sie weiter oben in dieser Übung eingerichtet haben.

1. Wählen Sie im Azure-Portal auf der Seite **devcenter-project-01 \|Imagedefinitionen** im vertikalen Navigationsmenü links im Abschnitt **Verwalten** die Option **Dev Box-Pools**.
1. Auf der Seite **devcenter-project-01 \| Dev Box-Pools** wählen Sie **+ Erstellen**.
1. Legen Sie auf der Registerkarte **Grundlagen** der Seite **Erstellen eines Dev Box-Pools** die folgenden Einstellungen fest und wählen Sie dann **Erstellen**:

   | Einstellung                                                                                                           | Wert                                                 |
   | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
   | Name                                                                                                              | **devcenter-project-01-devbox-pool-02**               |
   | Definition                                                                                                        | **frontend-eng**                                      |
   | Compute                                                                                                           | **8 vCPU, 32 GB RAM**                                 |
   | Storage                                                                                                           | **256 GB SSD**                                        |
   | Netzwerkverbindung                                                                                                | **Bereitstellen auf einer Netzwerkverbindung in meiner Organisation** |
   | Netzwerkverbindungsname                                                                                           | **network-connection-vnet-01**                        |
   | Einmaliges Anmelden aktivieren                                                                                             | Aktiviert                                               |
   | Erstellerberechtigungen für Dev Box                                                                                        | **Lokaler Admin**                               |
   | Automatisches Beenden im Zeitplan aktivieren                                                                                      | Aktiviert                                               |
   | Endzeit                                                                                                         | **19:00 Uhr**                                          |
   | Zeitzone                                                                                                         | Ihre aktuelle Zeitzone                                |
   | Ruhezustand beim Trennen aktivieren                                                                                    | Aktiviert                                               |
   | Karenzzeit in Minuten                                                                                           | **60**                                                |
   | Ich bestätige, dass meine Organisation über Lizenzen für Azure Hybrid Benefits verfügt, die für alle Dev Boxes in diesem Pool gelten. | Aktiviert                                               |

   > **Hinweis:** Warten Sie darauf, dass der Dev Box-Pool erstellt wird. Dies kann etwa zwei Minuten dauern.

### Aufgabe 7: Auswerten einer benutzerdefinierten Dev Box

In dieser Aufgabe bewerten Sie die Funktionalität einer benutzerdefinierten Dev Box mithilfe eines Microsoft Entra-Entwicklerbenutzerkontos.

> **Hinweis:** Aufgrund des zusätzlichen Zeitaufwands ist die Durchführung dieser Aufgabe optional.

1. Starten Sie einen Webbrowser im Inkognito-/In-Private-Modus und navigieren Sie zum Microsoft Dev Box-Entwicklerportal unter `https://aka.ms/devbox-portal`.
1. Wenn Sie zur Anmeldung aufgefordert werden, geben Sie die Anmeldedaten des Benutzerkontos **devuser01** ein.
1. Wählen Sie auf der Seite **Willkommen, devuser01** des Microsoft Dev Box-Entwicklerportals die Option **+ Neue Dev Box**.
1. Geben Sie im Bereich **Dev Box hinzufügen** in das Textfeld **Name** **`devuser01box02`** ein.
1. Wählen Sie in der Dropdownliste **Dev Box-Pool** **devcenter-project-01-devbox-pool-02**.
1. Prüfen Sie weitere Informationen, die im Fenster **Dev Box hinzufügen** angezeigt werden, z.B. den Projektnamen, die Spezifikationen des Dev Box-Pools, den Status der Ruhezustand-Unterstützung und den geplanten Zeitpunkt des Herunterfahrens. Beachten Sie außerdem die Option zum Anwenden von Anpassungen und den Hinweis, dass die Erstellung der Entwicklungsumgebung bis zu 65 Minuten dauern kann.
1. Wählen Sie im Bereich **Dev Box hinzufügen** die Option **Erstellen**.
1. Sobald die Dev Box vollständig eingerichtet ist und läuft, verbinden Sie sich mit ihr, indem Sie die Option **Über App verbinden** wählen.
1. Wählen Sie in dem Popupfenster mit dem Titel **Diese Seite versucht, das Microsoft Remote Connection Center zu öffnen** die Option **Öffnen**. Dadurch wird automatisch eine Remotedesktopsitzung in die Dev Box initiiert.
1. Wenn Sie zur Eingabe Ihrer Anmeldedaten aufgefordert werden, authentifizieren Sie sich mit dem Benutzernamen und dem Kennwort des Kontos **devuser01**.
1. Überprüfen Sie in der Remotedesktop-Sitzung zur Entwicklungsumgebung, ob die Konfiguration die Installation von Visual Studio Code umfasst.

   > **Hinweis:** Sie können das Ergebnis der Anpassung auch überprüfen, indem Sie das Ellipsen-Symbol in der **Ihre Dev-Box**-Schnittstelle auswählen und dann **Anpassungen** aus dem überlappenden Menü wählen.
