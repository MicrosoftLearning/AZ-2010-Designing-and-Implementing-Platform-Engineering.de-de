---
lab:
  title: Implementieren der Self-Service-Infrastruktur mit Bicep
  module: Strategic Platform Road Mapping
---

# Lab 03: Implementieren der Self-Service-Infrastruktur mit Bicep

## Geschätzte Zeit: 20 Minuten

## Voraussetzungen

- Azure-Abonnement: Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
- Grundkenntnisse der Azure-Dienste und der Azure CLI.
- Installation von Visual Studio Code mit der Bicep-Erweiterung.
- Installation und Konfiguration der Azure CLI auf Ihrem lokalen Computer.
- Vertrautheit mit Infrastructure as Code (IaC)-Konzepten.

## Ziele

- Einrichten von Bicep in Ihrer Umgebung
- Erstellen einer Bicep-Vorlage, um Azure-Ressourcen zu definieren
- Bereitstellen einer Azure App Service-Instanz mit einem SQL-Datenbank-Back-End
- Erzwingen von Governance mithilfe von Tags und Richtlinien
- Implementieren der automatisierten Skalierung mit Bicep

## Übung 1: Erstellen einer Bicep-Vorlage zum Bereitstellen von Azure-Ressourcen

Fachkräfte in der Entwicklung benötigen eine Möglichkeit, die Infrastruktur konsistent und effizient bereitzustellen. Dieses Lab führt Sie durch die Verwendung von Bicep, einem Infrastructure as Code (IaC) Tool, um Azure-Ressourcen im Selbstbedienungsmodus bereitzustellen und zu verwalten. Sie werden eine Bicep-Vorlage erstellen, um eine Ressourcengruppe, einen Azure App Service, eine Azure SQL-Datenbank und ein Speicherkonto bereitzustellen und dabei die Governance mit Markierungen und Richtlinien durchzusetzen.

### Aufgabe 1: Installieren von Bicep-CLI

1. Öffnen Sie Ihr lokales Terminal.
1. Um zu überprüfen, ob Bicep installiert ist, führen Sie Folgendes aus:

   ```bash
   az bicep version
   ```

   Wenn Bicep nicht installiert ist, installieren Sie es mit:

   ```bash
   az bicep install
   ```

1. Bestätigen Sie die Installation, indem Sie Folgendes ausführen:

   ```bash
   az bicep version
   ```

   Sie sollten eine Ausgabe wie „Bicep CLI Version X.Y.Z.“ sehen.

   > **HINWEIS:** Stellen Sie sicher, dass Sie die [Azure CLI](https://learn.microsoft.com/cli/azure/install-azure-cli) installiert haben.

### Aufgabe 2: Erstellen einer Bicep-Vorlage

1. Erstellen Sie in Ihrem lokalen Terminal eine neue Datei:

   ```bash
   code main.bicep
   ```

1. Kopieren Sie den folgenden Bicep-Code und fügen Sie ihn in die Datei ein:

   ```bicep
   param location string = 'eastus2'
   param appServicePlanName string = 'bicepAppPlan'
   param webAppName string = 'bicep-webapp'
   param storageAccountName string = 'biceplabstorage'
   param sqlServerName string = 'bicep-sqlserver'
   param sqlDatabaseName string = 'bicepdb'
   param sqlAdminUser string
   @secure()
   param sqlAdminPassword string

   targetScope = 'resourceGroup'

   resource storage 'Microsoft.Storage/storageAccounts@2021-09-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: 'Standard_LRS'
     }
     kind: 'StorageV2'
   }

   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'F1'
       tier: 'Free'
     }
   }

   resource webApp 'Microsoft.Web/sites@2021-02-01' = {
     name: webAppName
     location: location
     properties: {
       serverFarmId: appPlan.id
     }
   }

   resource sqlServer 'Microsoft.Sql/servers@2022-05-01-preview' = {
     name: sqlServerName
     location: location
     properties: {
       administratorLogin: sqlAdminUser
       administratorLoginPassword: sqlAdminPassword
       version: '12.0'
     }
   }

   resource sqlDb 'Microsoft.Sql/servers/databases@2022-05-01-preview' = {
     name: sqlDatabaseName
     location: location
     parent: sqlServer
     properties: {
       collation: 'SQL_Latin1_General_CP1_CI_AS'
       maxSizeBytes: 2147483648
     }
   }
   ```

   > **HINWEIS:** Sie können die Bicep-Erweiterung in Visual Studio Code installieren, um Syntaxmarkierung und IntelliSense für Bicep-Dateien zu erhalten.

   > **WICHTIG:** Stellen Sie sicher, dass storageAccountName, webAppName und sqlServerName global eindeutig sind. Wenn die Bereitstellung aufgrund von Namenskonflikten fehlschlägt, ändern Sie die Namen entsprechend.

   > **HINWEIS**: Wenn ein Fehler im Zusammenhang mit der Region auftritt, versuchen Sie, die Bereitstellung in einer anderen Region durch Ändern des Parameterwerts `location` durchzuführen.

1. Speichern und schließen Sie die Datei.

### Aufgabe 3: Bereitstellen einer Vorlage mit der Azure CLI

1. Führen Sie den folgenden Befehl aus, um die Ressourcengruppe zu erstellen:

   ```bash
   az group create --name BicepLab-RG --location centralus

   ```

   > **HINWEIS:** Möglicherweise müssen Sie sich bei Ihrem Azure-Konto anmelden, wenn Sie noch nicht authentifiziert sind. Ausführliche Anweisungen finden Sie unter [Authentifizieren bei Azure mithilfe der Azure CLI](https://learn.microsoft.com/cli/azure/authenticate-azure-cli).

2. Stellen Sie die Vorlage innerhalb der Ressourcengruppe bereit:

   ```bash
   az deployment group create --resource-group BicepLab-RG --template-file main.bicep --parameters sqlAdminUser='azureuser' sqlAdminPassword='YourSecurePassword123!'

   ```

   > **HINWEIS:** Ersetzen Sie „YourSecurePassword123!“ durch ein sicheres Kennwort und „azureuser“ durch einen gültigen Benutzernamen.

3. Warten Sie, bis die Bereitstellung abgeschlossen ist. Es wird eine Bestätigungsmeldung angezeigt.
4. Navigieren Sie im Azure-Portal zu Ressourcengruppen.
5. Suchen Sie BicepLab-RG, und klicken Sie darauf.
6. Überprüfen Sie, ob die Ressourcen erfolgreich erstellt wurden.

Sie haben erfolgreich einen Azure App Service mit einem SQL-Datenbank Back-End mithilfe einer Bicep-Vorlage bereitgestellt. Sie können Ihre Infrastructure as Code verwalten und Ressourcen konsistent und effizient bereitstellen. Der nächste Schritt wäre die Integration dieser Vorlage in Ihre CI/CD-Pipeline für automatisierte Bereitstellungen.

## Übung 2: Erzwingen von Governance mit Tags und Richtlinien

In einer Self-Service-Infrastrukturumgebung ist es unerlässlich, Governance durchzusetzen, um Compliance und Kostenkontrolle sicherzustellen. Tags und Richtlinien sind zwei wichtige Mechanismen, um dies zu erreichen.

### Aufgabe 1: Hinzufügen von Ressourcen zum Katalog

1. Führen Sie den folgenden Befehl aus, um der Ressourcengruppe ein Tag hinzuzufügen:

   ```bash
   az group update --name BicepLab-RG --set tags.Owner='PlatformEngineering'
   ```

1. Überprüfen Sie, ob das Tag hinzugefügt wurde, indem Sie Folgendes ausführen:

   ```bash
   az group show --name BicepLab-RG --query tags
   ```

### Aufgabe 2: Erstellen einer Richtlinie zum Erzwingen von Tagging

1. Erstellen Sie eine JSON-Richtliniendatei:

   ```bash
   code tagging-policy.json
   ```

1. Kopieren Sie die folgende Richtliniendefinition und fügen Sie sie in die Datei ein:

   ```json
   {
     "if": {
       "not": {
         "field": "tags[Owner]",
         "exists": "true"
       }
     },
     "then": {
       "effect": "deny"
     }
   }
   ```

1. Erstellen Sie die Richtliniendefinition mithilfe der JSON-Datei:

   ```bash
    az policy definition create --name 'tagging-policy' --display-name 'Enforce tagging' --rules @tagging-policy.json --mode All
   ```

1. Weisen Sie der Ressourcengruppe die Richtlinie zu:

   ```bash
   az policy assignment create --name 'tagging-policy-assignment' --display-name 'Enforce tagging' --policy "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policyDefinitions/tagging-policy" --scope "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG"

   ```

   > **WICHTIG:** Ersetzen Sie beide <subscription-id> durch Ihre Azure-Abonnement-ID.

1. Überprüfen Sie, ob die Richtlinie zugewiesen wurde, indem Sie Folgendes ausführen:

   ```bash
   az policy assignment list --output table
   ```

### Aufgabe 3: Testen der Richtlinienerzwingung

1. Führen Sie den folgenden Befehl aus, um das Tag aus der Ressourcengruppe zu entfernen:

   ```bash
   az tag update --resource-id /subscriptions/<subscription-id>/resourceGroups/BicepLab-RG --operation delete --tags Owner
   ```

   > **WICHTIG:** Ersetzen Sie <subscription-id> durch Ihre Azure-Abonnement-ID.

1. Es sollte eine Fehlermeldung angezeigt werden, die angibt, dass der Vorgang aufgrund der Richtlinienerzwingung verweigert wurde. Das Tag sollte nicht entfernt werden. Dies bestätigt, dass die Richtlinie wie erwartet funktioniert. Um den Fehler zu beheben, entfernen Sie vorübergehend die Richtlinienzuweisung, und führen Sie den Befehl erneut aus.

   ```bash
   az policy exemption create --name 'tagging-policy-exemption' --policy-assignment "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG/providers/Microsoft.Authorization/policyAssignments/tagging-policy-assignment" --scope "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG" --display-name "Temporary exemption to remove tag" --exemption-category Waiver
   ```

   > **WICHTIG:** Ersetzen Sie beide <subscription-id> durch Ihre Azure-Abonnement-ID.

Sie haben erfolgreich Governance mit Tags und Richtlinien durchgesetzt. Fachkräfte in der Entwicklung müssen nun Ressourcen mit dem Tag „Besitzer“ kennzeichnen, um die ordnungsgemäße Eigentümerschaft und Verantwortlichkeit sicherzustellen. Sie können zusätzliche Richtlinien erstellen, um andere Governance-Anforderungen durchzusetzen, z. B. Namenskonventionen für Ressourcen, Ressourcentypen und Ressourcenstandorte.

## Übung 3: Implementieren der automatisierten Skalierung mit Bicep

In einer Plattform-Engineering-Umgebung ist die Sicherstellung, dass Anwendungen effizient skaliert werden können, eine wichtige Voraussetzung. Die automatisierte Skalierung verbessert die Leistung, optimiert die Ressourcenauslastung und minimiert die Kosten. In dieser Übung implementieren Sie die automatische Skalierung für einen Azure App Service mit Bicep.

### Aufgabe 1: Definieren einer automatischen Skalierungsrichtlinie in Bicep

1. Öffnen Sie die Datei main.bicep und suchen Sie den vorhandenen App Service-Plan-Abschnitt:

   ```bicep
   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'F1'
       tier: 'Free'
     }
   }
   ```

1. Ändern Sie die appPlan-Ressource so, dass die S1-SKU verwendet wird (die die automatische Skalierung unterstützt):

   ```bicep
   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'S1'
       tier: 'Standard'
     }
     properties: {
       perSiteScaling: false
       maximumElasticWorkerCount: 10
     }
   }
   ```

1. Fügen Sie unmittelbar nach dieser Ressource die AutoscaleSetting-Konfiguration hinzu:

   ```bicep
   resource autoscaleSetting 'Microsoft.Insights/autoscaleSettings@2024-01-01-preview' = {
   name: 'autoscale-rule'
   location: location
   properties: {
    profiles: [
      {
        name: 'defaultProfile'
        capacity: {
          minimum: '1'
          maximum: '5'
          default: '1'
        }
        rules: [
          {
            metricTrigger: {
              metricName: 'CpuPercentage'
              metricResourceUri: resourceId('Microsoft.Web/serverfarms', appServicePlanName)
              operator: 'GreaterThan'
              threshold: 75
              timeAggregation: 'Average'
              statistic: 'Average'
              timeWindow: 'PT5M'
              timeGrain: 'PT1M'
            }
            scaleAction: {
              direction: 'Increase'
              type: 'ChangeCount'
              value: '1'
              cooldown: 'PT1M'
            }
          }
        ]
      }
    ]
   }
   }
   ```

1. Speichern Sie die Datei.
1. Überprüfen Sie die aktualisierte Bicep-Datei:

   ```bash
   az bicep build --file main.bicep
   ```

1. Stellen Sie die aktualisierte Vorlage bereit:

   ```bash
   az deployment group create --resource-group BicepLab-RG --template-file main.bicep --parameters sqlAdminUser='azureuser' sqlAdminPassword='YourSecurePassword123!'

   ```

1. Überprüfen Sie, ob die Richtlinie für die automatische Skalierung auf den App Service-Plan angewendet wurde. Gehen Sie zum Azure-Portal, navigieren Sie zum App Service-Plan, wählen Sie das Blatt „Scale out“ (App Service-Plan) und überprüfen Sie, ob die Regel für die automatische Skalierung konfiguriert ist.

   > **HINWEIS:** Wenn Sie das automatische Skalierungsverhalten testen möchten, können Sie eine hohe CPU-Auslastung für den App Service simulieren, indem Sie einen Auslastungstest ausführen oder Datenverkehr generieren. Der App Service sollte basierend auf der definierten Regel automatisch skaliert werden.

Sie haben die automatisierte Skalierung für einen Azure App Service mithilfe von Bicep erfolgreich implementiert. So können Sie sicherstellen, dass Ihre Anwendungen den erhöhten Datenverkehr und die steigende Nachfrage effizient bewältigen können, was die Leistung verbessert und die Kosten senkt.
