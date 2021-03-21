---
title: Come usare Application Insights agente In-Process Java nel cloud Spring di Azure
description: Come monitorare le app e i microservizi usando Application Insights agente In-Process Java nel cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: c4871c3de8028eec1b6184c1d03ac2180b50f57d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881351"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights agente In-Process Java nel cloud Spring di Azure (anteprima)

Questo documento illustra come monitorare le app e i microservizi usando il Application Insights agente Java nel cloud Spring di Azure. 

Con questa funzionalità è possibile:

* Eseguire ricerche nei dati di traccia con filtri diversi.
* Visualizzare la mappa delle dipendenze dei microservizi.
* Controllare le prestazioni della richiesta.
* Monitorare le metriche Live in tempo reale.
* Controllare gli errori della richiesta.
* Controllare le metriche dell'applicazione.

Application Insights offrono molte prospettive osservabili, tra cui:

* Mappa delle applicazioni
* Prestazioni
* Errori
* Metriche
* Metriche attive    
* Disponibilità

## <a name="enable-java-in-process-agent-for-application-insights"></a>Abilita l'agente di In-Process Java per Application Insights

Abilitare la funzionalità di anteprima di Java In-Process Agent usando la procedura seguente.

1. Passare alla pagina Panoramica servizio dell'istanza del servizio.
2. Fare clic su **Application Insights** voce sotto il pannello monitoraggio.
3. Per abilitare l'integrazione **Application Insights** , fare clic sul pulsante **Abilita Application Insights** .
4. Selezionare un'istanza esistente di Application Insights o crearne una nuova.
5. Chick **Abilita Java in-Process Agent** per abilitare l'anteprima della funzionalità Java in-Process Agent. Qui è anche possibile personalizzare la frequenza di campionamento da 0 a 100.
6.  Fare clic su **Salva** per salvare la modifica.

## <a name="portal"></a>Portale

1. Vai al **servizio | Pagina Panoramica** e selezionare **Application Insights** nella sezione **monitoraggio** . 
2. Fare clic su **abilita Application Insights** per abilitare Application Insights nel cloud Spring di Azure.
3. Fare clic su **Abilita Java in-Process Agent** per abilitare la funzionalità di anteprima di Java IPA. Quando è abilitata una funzionalità di anteprima IPA, è possibile configurare una frequenza di campionamento facoltativa (valore predefinito 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Uso della funzionalità Application Insights

Quando è abilitata la funzionalità **Application Insights** , è possibile:

Nel riquadro di spostamento a sinistra fare clic su **Application Insights** per passare alla pagina **Panoramica** di Application Insights. 

* Fare clic su **mappa applicazioni** per visualizzare lo stato delle chiamate tra le applicazioni.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Fare clic sul collegamento tra Customers-service e `petclinic` per visualizzare altri dettagli, ad esempio una query da SQL.

* Nel riquadro di spostamento a sinistra fare clic su **prestazioni** per visualizzare i dati sulle prestazioni di tutte le operazioni delle applicazioni, nonché le dipendenze e i ruoli.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* Nel riquadro di spostamento a sinistra fare clic su **errori** per verificare se si è verificata una situazione imprevista dalle applicazioni.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* Nel riquadro di spostamento a sinistra fare clic su **metriche** e selezionare lo spazio dei nomi. vengono visualizzate sia le metriche di Spring boot sia le metriche personalizzate, se presenti.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* Nel riquadro di spostamento a sinistra fare clic su **metriche attive** per visualizzare le metriche in tempo reale per le diverse dimensioni.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* Nel riquadro di spostamento a sinistra fare clic su **disponibilità** per monitorare la disponibilità e la velocità di risposta delle app Web creando [test di disponibilità in Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Modello ARM
Per usare il modello di Azure Resource Manager, copiare il contenuto seguente in `azuredeploy.json` .

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
Applicare il modello ARM con il comando CLI:

* Per un'istanza di Azure Spring cloud esistente:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Per un'istanza di Azure Spring cloud appena creata:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Per disabilitare app-Insight:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Vedi anche
* [Usare la traccia distribuita con Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)
* [Analizzare log e metriche](diagnostic-services.md)
* [Eseguire lo streaming dei log in tempo reale](spring-cloud-howto-log-streaming.md)