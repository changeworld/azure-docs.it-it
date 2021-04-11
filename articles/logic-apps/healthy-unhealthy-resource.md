---
title: Configurare la registrazione per monitorare le app per la logica nel centro sicurezza di Azure
description: Monitorare l'integrità delle risorse delle app per la logica nel centro sicurezza di Azure configurando la registrazione diagnostica.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712386"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Configurare la registrazione per monitorare le app per la logica nel centro sicurezza di Azure

Quando si esegue il monitoraggio delle risorse di app per la logica in [Microsoft Azure Centro sicurezza](../security-center/security-center-introduction.md), è possibile [verificare se le app per la logica seguono i criteri predefiniti](#view-logic-apps-health-status). Azure Mostra lo stato di integrità di una risorsa di app per la logica dopo l'abilitazione della registrazione e la configurazione corretta della destinazione dei log. Questo articolo illustra come configurare la registrazione diagnostica e assicurarsi che tutte le app per la logica siano risorse integre.

> [!TIP]
> Per trovare lo stato corrente per il servizio app per la logica, vedere la [pagina stato di Azure](https://status.azure.com/), che elenca lo stato dei diversi prodotti e servizi in ogni area disponibile.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, [creare un account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* App per la logica esistenti con [registrazione diagnostica abilitata](#enable-diagnostic-logging).
* Un'area di lavoro Log Analytics, che è necessaria per abilitare la registrazione per l'app per la logica. Se non si dispone di un'area di lavoro, [creare prima l'area di lavoro](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Abilitare la registrazione diagnostica

Prima di poter visualizzare lo stato di integrità delle risorse per le app per la logica, è necessario prima [configurare la registrazione diagnostica](monitor-logic-apps-log-analytics.md). Se si dispone già di un'area di lavoro di Log Analytics, è possibile abilitare la registrazione quando si crea l'app per la logica o in app per la logica esistenti.

> [!TIP]
> L'indicazione predefinita prevede l'abilitazione dei log di diagnostica per le app per la logica. Questa impostazione viene tuttavia controllata per le app per la logica. Quando si abilitano i log di diagnostica per le app per la logica, è possibile usare le informazioni per analizzare gli eventi imprevisti della sicurezza.

### <a name="check-diagnostic-logging-setting"></a>Controllare le impostazioni di registrazione diagnostica

Se non si è certi che la registrazione diagnostica sia abilitata per le app per la logica, è possibile archiviare il Centro sicurezza:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca immettere e selezionare **Centro sicurezza**.
1. Nel menu del dashboard del Centro sicurezza, in **generale**, selezionare **raccomandazioni**.
1. Nella tabella dei suggerimenti di sicurezza trovare e selezionare **Abilita il controllo e la registrazione** dei log di &gt; **diagnostica in app per la logica deve essere abilitato** nella tabella dei controlli di sicurezza.
1. Nella pagina raccomandazione, espandere la sezione **procedure correttive** ed esaminare le opzioni. È possibile abilitare la diagnostica delle app per la logica selezionando la **correzione rapida.** o seguendo le istruzioni per la correzione manuale.

## <a name="view-logic-apps-health-status"></a>Visualizzare lo stato di integrità delle app per la logica

Dopo aver [abilitato la registrazione diagnostica](#enable-diagnostic-logging), è possibile visualizzare lo stato di integrità delle app per la logica nel centro sicurezza.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca immettere e selezionare **Centro sicurezza**.
1. Scegliere **inventario** dal menu Dashboard del Centro sicurezza in **generale**.
1. Nella pagina inventario filtrare l'elenco asset per visualizzare solo le risorse delle app per la logica. Nel menu della pagina selezionare **tipi di risorse** app per la &gt; **logica**.

   Il contatore **delle risorse non integre** indica il numero di app per la logica considerate non integre dal centro sicurezza.
1.  Nell'elenco delle risorse di app per la logica esaminare la colonna **raccomandazioni** . Per esaminare i dettagli di integrità per un'app per la logica specifica, selezionare un nome di risorsa oppure fare clic sul pulsante con i puntini di sospensione (**...**) &gt; **Visualizzare la risorsa**.
1.  Per correggere i potenziali problemi di integrità delle risorse, seguire i passaggi elencati per le app per la logica.

Se la registrazione diagnostica è già abilitata, potrebbe essersi verificato un problema con la destinazione per i log. Esaminare [le procedure per risolvere i problemi relativi alle diverse destinazioni di registrazione diagnostica](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Correggere la registrazione diagnostica per le app per la logica

Se le [app per la logica sono elencate come non integre nel centro sicurezza](#view-logic-apps-health-status), aprire l'app per la logica nella visualizzazione codice nell'portale di Azure o tramite l'interfaccia della riga di comando di Azure. Controllare quindi la configurazione di destinazione per i log di diagnostica: [azure log Analytics](#log-analytics-and-event-hubs-destinations), [Hub eventi di Azure](#log-analytics-and-event-hubs-destinations)o [un account di archiviazione di Azure](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Destinazioni Log Analytics e hub eventi

Se si usa Log Analytics o hub eventi come destinazione per i log di diagnostica delle app per la logica, controllare le impostazioni seguenti. 

1. Per confermare che sono stati abilitati i log di diagnostica, verificare che il `logs.enabled` campo impostazioni di diagnostica sia impostato su `true` . 
1. Per verificare che non sia stato impostato un account di archiviazione come destinazione, verificare che il `storageAccountId` campo sia impostato su `false` .

Ad esempio:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Destinazione dell'account di archiviazione

Se si usa un account di archiviazione come destinazione per i log di diagnostica delle app per la logica, controllare le impostazioni seguenti.

1. Per confermare che sono stati abilitati i log di diagnostica, verificare che il `logs.enabled` campo impostazioni di diagnostica sia impostato su `true` .
1. Per confermare che è stato abilitato un criterio di conservazione per i log di diagnostica, verificare che il `retentionPolicy.enabled` campo sia impostato su `true` .
1. Per confermare di impostare un periodo di conservazione di 0-365 giorni, controllare che il `retentionPolicy.days` campo sia impostato su un numero compreso tra 0 e 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```