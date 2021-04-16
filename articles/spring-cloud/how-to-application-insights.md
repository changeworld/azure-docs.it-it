---
title: Come usare Application Insights Java In-Process Agent in Azure Spring Cloud
description: Come monitorare app e microservizi Application Insights Java In-Process Agent in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c7083cb6669d7bc779a8e69babfef38988819f8c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483774"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights'agente In-Process Java in Azure Spring Cloud (anteprima)

Questo documento illustra come monitorare le app e i microservizi usando Application Insights'agente Java in Azure Spring Cloud. 

Con questa funzionalità è possibile:

* Eseguire ricerche nei dati di traccia con filtri diversi.
* Visualizzare la mappa delle dipendenze dei microservizi.
* Controllare le prestazioni della richiesta.
* Monitorare le metriche in tempo reale.
* Controllare gli errori delle richieste.
* Controllare le metriche dell'applicazione.

Application Insights offrono molte prospettive osservabili, tra cui:

* Mappa delle applicazioni
* Prestazioni
* Errori
* Metriche
* Metriche attive    
* Disponibilità

## <a name="enable-java-in-process-agent-for-application-insights"></a>Abilitare Java In-Process Agent per Application Insights

Abilitare la funzionalità In-Process di anteprima di Java In-Process Agent usando la procedura seguente.

1. Passare alla pagina di panoramica del servizio dell'istanza del servizio.
2. Fare **clic Application Insights** voce nel pannello di monitoraggio.
3. Fare **clic sul pulsante Application Insights** per abilitare **l'Application Insights** integrazione.
4. Selezionare un'istanza esistente Application Insights crearne una nuova.
5. Abilitazione **dell'agente java in-process** per abilitare la funzionalità dell'agente java in-process in anteprima. Qui è anche possibile personalizzare la frequenza di campionamento da 0 a 100.
6.  Fare clic su **Salva** per salvare la modifica.

## <a name="portal"></a>Portale

1. Passare al **servizio | Pagina** Panoramica e selezionare **Application Insights** nella **sezione** Monitoraggio. 
2. Fare **clic su Application Insights** per abilitare Application Insights in Azure Spring Cloud.
3. Fare **clic su Enable Java in-process agent (Abilita agente java in-process)** per abilitare la funzionalità di anteprima java IPA. Quando è abilitata una funzionalità di anteprima IPA, è possibile configurare una frequenza di campionamento facoltativa (il valore predefinito è 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Uso della funzionalità Application Insights

Quando la **Application Insights** è abilitata, è possibile:

Nel riquadro di spostamento a sinistra fare **clic Application Insights** per passare alla **pagina** Panoramica di Application Insights. 

* Fare **clic su Mappa delle** applicazioni per visualizzare lo stato delle chiamate tra le applicazioni.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Fare clic sul collegamento tra customers-service `petclinic` e per visualizzare altri dettagli, ad esempio una query da SQL.

* Nel riquadro di spostamento a sinistra fare clic su **Prestazioni** per visualizzare i dati sulle prestazioni di tutte le operazioni delle applicazioni, nonché le dipendenze e i ruoli.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Nel riquadro di spostamento a sinistra fare clic **su Errori** per verificare se si è verificato un evento imprevisto dalle applicazioni.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Nel riquadro di spostamento a sinistra fare clic su **Metriche** e selezionare lo spazio dei nomi. Verranno Spring Boot metriche personalizzate e metriche personalizzate, se presenti.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Nel riquadro di spostamento a sinistra fare clic **su Metriche attive** per visualizzare le metriche in tempo reale per dimensioni diverse.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Nel riquadro di spostamento a sinistra fare clic su **Disponibilità** per monitorare la disponibilità e la velocità di risposta delle app Web creando test di disponibilità [in Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Modello ARM
Per usare il modello Azure Resource Manager, copiare il contenuto seguente in `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
Applicare un modello di Arm con il comando dell'interfaccia della riga di comando:

* Per un'istanza Azure Spring Cloud esistente:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* Per un'istanza di Azure Spring Cloud creata:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* Per disabilitare app-insight:

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

```

## <a name="see-also"></a>Vedi anche
* [Usare la traccia distribuita con Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Analizzare log e metriche](diagnostic-services.md)
* [Eseguire lo streaming dei log in tempo reale](spring-cloud-howto-log-streaming.md)
