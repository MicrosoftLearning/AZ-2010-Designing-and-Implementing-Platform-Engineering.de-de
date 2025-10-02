---
lab:
  title: Implementieren der Echtzeitüberwachung mit Azure Monitor
  module: Observability and Continuous Improvement
---

# Lab 02: Implementieren der Echtzeitüberwachung mit Azure Monitor

## Geschätzte Zeit: 20 Minuten

## Voraussetzungen

- Azure-Abonnement: Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
- Grundlegende Kenntnisse der Konzepte der Überwachung und des Einblicks.
- Grundkenntnisse in Bezug auf Azure-Dienste.

## Ziele

- Erstellen einer Beispiel-Web-App
- Aktivieren und Konfigurieren von Azure Monitor und Application Insights
- Erstellen benutzerdefinierter Dashboards zum Visualisieren von Anwendungsmetriken
- Einrichten von Warnungen, um die Beteiligten über Leistungsanomalien zu benachrichtigen
- Analysieren von Leistungsdaten für potenzielle Verbesserungen

## Übung 1: Erstellen einer einfachen Webanwendung

Als Fachkraft in der Entwicklung müssen Sie sicherstellen, dass die auf Ihrer Plattform ausgeführten Anwendungen feststellbar und kontinuierlich überwacht werden können. In dieser Übung erstellen Sie eine Beispielwebanwendung für Azure, konfigurieren Azure Monitor und Application Insights, richten benutzerdefinierte Dashboards ein und erstellen Warnungen zum Nachverfolgen der Anwendungsleistung.

### Aufgabe 1: Erstellen einer Azure-Web-App

1. Öffnen Sie das Azure-Portal.
1. Geben Sie in der Suchleiste „App Services“ ein und wählen Sie den Eintrag aus.
1. Klicken Sie auf „+ Erstellen“ und wählen Sie „Web-App“ aus.
1. Auf der Registerkarte Grundlagen:
   - Abonnement: Wählen Sie Ihr Azure-Abonnement.
   - Ressourcengruppe: Klicken Sie auf „Neue erstellen“, geben Sie **`monitoringlab-rg`** ein und klicken Sie auf „OK“.
   - Name: Geben Sie einen eindeutigen Namen ein, z. B. **`monitoringlab-webapp`**.
   - Veröffentlichen: Wählen Sie Code aus.
   - Runtime-Stack: Wählen Sie .NET 8 (LTS) aus.
   - Region: Wählen Sie eine Region in Ihrer Nähe aus.
1. Klicken Sie auf „Überprüfen + erstellen“ und anschließend auf „Erstellen“.
1. Warten Sie, bis die Bereitstellung abgeschlossen ist, und klicken Sie dann auf „Zur Ressource wechseln“.
1. Klicken Sie auf der Registerkarte „Übersicht“ auf die URL, um zu überprüfen, ob die Web-App ausgeführt wird.

### Aufgabe 2: Überprüfen von Application Insights

1. Navigieren Sie in der Web-App-Ressource zum Abschnitt „Überwachung“.
1. Klicken Sie auf „Application Insights“.
1. Application Insights ist bereits für diese Web-App aktiviert. Klicken Sie auf den Link, um die Application Insights-Ressource zu öffnen.
1. Klicken Sie in der Application Insights-Ressource im Anwendungs-Dashboard, um die Leistungsdaten anzuzeigen, die im Standard-Dashboard bereitgestellt werden.

   > **Hinweis:** Es kann einige Minuten dauern, bis auf dem Dashboard alle Leistungsdaten vollständig geladen und angezeigt werden. Für eine optimale Benutzererfahrung in nachfolgenden Übungen warten Sie, bis das Dashboard vollständig gerendert ist, bevor Sie fortfahren.

## Übung 2: Konfigurieren von Azure Monitor und Dashboards

### Aufgabe 1: Zugriff auf Azure Monitor

1. Suchen Sie im Azure-Portal nach „Überwachen“ und klicken Sie darauf.
1. Klicken Sie im linken Bedienfeld auf „Metriken“.
1. Wählen Sie im Abschnitt „Geltungsbereich“ die Web-App unter dem Abonnement und der Ressourcengruppe aus, in der Sie die Web-App bereitgestellt haben.
1. Klicken Sie auf „Übernehmen“, und beobachten Sie die für die Web-App verfügbaren Metriken.

### Aufgabe 2: Hinzufügen wichtiger Metriken zum Dashboard

1. Wählen Sie im Bereich Ihre Web-App (monitoringlab-webapp) aus.
1. Wählen Sie unter „Metrik“ die Option „Antwortzeit“ aus.
1. Legen Sie die Aggregation auf den mittleren Wert fest, und klicken Sie auf „+Metrik hinzufügen“.
1. Wiederholen Sie den Vorgang für weitere Metriken:
   - CPU-Zeit (Anzahl)
   - Anforderungen (Mittelwert)
1. Wählen Sie im Metrikdiagrammbereich in der oberen rechten Ecke des Diagramms die Schaltfläche **An Dashboard anheften** (Anheftsymbol).
1. Gehen Sie im daraufhin angezeigten Dialogfeld **An Dashboard anheften** wie folgt vor:
   - Wählen Sie **Neu** aus, um ein neues Dashboard zu erstellen.
   - Geben Sie einen Dashboardnamen ein: **`MonitoringLab Dashboard`**
   - Wählen Sie das entsprechende Abonnement aus.
   - Wählen Sie den Dashboardtyp:
     - **Öffentlich**: Das Dashboard ist für alle Benutzer in der Organisation sichtbar. Dies ist für Szenarien geeignet, in denen die Freigabe der Sichtbarkeit erforderlich ist.
     - **Privat**: Das Dashboard ist nur für Sie sichtbar. Dies wird für Labübungen oder Szenarien empfohlen, in denen die Datensichtbarkeit eingeschränkt werden sollte.
   - Wählen Sie **Erstellen und anheften** aus.
1. Nachdem das Dashboard erstellt wurde und die Metriken angeheftet sind, wählen Sie im oberen Menü **Speichern**, um das Dashboard zu speichern.
1. Navigieren Sie zum Dashboard, indem Sie im linken Bereich des Azure-Portals das Symbol **Dashboard** wählen oder in der oberen Suchleiste nach „Dashboard“ suchen.
1. Wählen Sie Ihr neu erstelltes **MonitoringLab-Dashboard** aus der Dashboardliste aus.
1. Überprüfen Sie, ob die Metriken im Dashboard angezeigt werden und in Echtzeit aktualisiert werden.

## Übung 3: Erstellen von Warnungen

### Aufgabe 1: Definieren von Warnungsbedingungen und Aktionen

1. Klicken Sie in Azure Monitor auf „Warnungen“.
1. Wählen Sie „+ Erstellen“ und anschließend „Warnungsregel“ aus.
1. Wählen Sie unter „Geltungsbereich“ Ihre Web-App (monitoringlab-webapp) aus und klicken Sie auf „Übernehmen“.
1. Klicken Sie unter „Bedingung“ in das Feld „Signalname“ und wählen Sie „Antwortzeit“ aus.
1. Konfigurieren Sie die Warnungsregel:
   - Schwellenwerttyp: Dynamisch
   - Aggregationstyp: Durchschnitt
   - Wert ist: Größer oder kleiner als
   - Schwellenwertempfindlichkeit: Hoch
   - Bewertungszeitpunkt: Im Minutentakt und fünf Minuten in die Vergangenheit.
1. Wählen Sie unter „Aktionen“ die Option „Schnellaktionen verwenden“ aus.
1. Geben Sie Folgendes ein:
   - Aktionsgruppenname: WebAppMonitoringAlerts
   - Anzeigename: WebAlert
   - E-Mail: Geben Sie Ihre E-Mail-Adresse ein.
1. Klicken Sie auf Speichern.
1. Klicken Sie auf „Weiter: Details“.
1. Geben Sie den Namen `WebAppResponseTimeAlert` ein und wählen Sie den Schweregrad „Ausführlich“ aus.
1. Klicken Sie auf Überprüfen + erstellen und danach auf Erstellen.

   > **Hinweis:** Ihre Warnungsregel wird jetzt erstellt und löst eine E-Mail-Benachrichtigung aus, wenn die Reaktionszeit den Schwellenwert überschreitet. Sie können erzwingen, dass die Warnung ausgelöst wird, indem Sie eine große Anzahl von Anforderungen an die Web-App senden. Sie können beispielsweise Azure Load Testing oder ein Tool wie Apache JMeter verwenden.

1. Kehren Sie zu „Azure Monitor > Warnungen“ zurück.
1. Klicken Sie auf „Warnungsregeln“, und Sie sollten die von Ihnen erstellte Warnungsregel sehen.

## Übung 4: Analysieren von Leistungsdaten

### Aufgabe 1: Überprüfen gesammelter Metriken

1. Navigieren Sie im Azure-Portal zu Application Insights.
1. Klicken Sie auf das Anwendungsdashboard.
1. Klicken Sie auf die Kachel „Leistung“, um die Antwortzeiten und das Laden des Servers zu analysieren. Sie können auch die Anzahl der Anforderungen und fehlgeschlagenen Anforderungen anzeigen.
1. Klicken Sie auf „Mit Arbeitsmappen analysieren“ und wählen Sie „Leistungsindikatoranalyse“.
1. Klicken Sie auf „Arbeitsmappen“.
1. Wählen Sie die Arbeitsmappenleistungsanalyse unter „Leistung“ aus.
1. Sie können die Leistungsdaten für die Web-App anzeigen.

> **Hinweis:** Sie können die Arbeitsmappe so anpassen, dass sie zusätzliche Metriken und Filter enthält.
