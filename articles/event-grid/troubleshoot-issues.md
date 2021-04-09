---
title: Risolvere i problemi relativi alla griglia di eventi
description: Questo articolo illustra diversi modi per risolvere i problemi di griglia di eventi di Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: d30b8464de90474ad74853cc423de700b41226a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720560"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Risolvere i problemi di griglia di eventi di Azure
Questo articolo fornisce informazioni che consentono di risolvere i problemi relativi alla griglia di eventi di Azure. 

## <a name="diagnostic-logs"></a>Log di diagnostica
Abilitare le impostazioni di diagnostica per argomenti o domini della griglia di eventi per acquisire e visualizzare i log degli errori di pubblicazione e recapito. Per ulteriori informazioni, vedere [log di diagnostica](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Metriche
È possibile visualizzare le metriche per gli argomenti e le sottoscrizioni di griglia di eventi e creare avvisi su di essi. Per altre informazioni, vedere [metriche di griglia di eventi](monitor-event-delivery.md).

## <a name="alerts"></a>Avvisi
Creare avvisi per le metriche di griglia di eventi di Azure e le operazioni del log attività. Per altre informazioni, vedere [avvisi per le metriche e i log attività di griglia di eventi](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problemi di convalida della sottoscrizione
Durante la creazione della sottoscrizione di eventi, è possibile che venga visualizzato un messaggio di errore che indica che la convalida dell'endpoint specificato non è riuscita. Per la risoluzione dei problemi di convalida delle sottoscrizioni, vedere risolvere i problemi di convalida della [sottoscrizione di griglia di eventi](troubleshoot-subscription-validation.md) 

## <a name="network-connectivity-issues"></a>Problemi relativi alla connettività di rete
Esistono diversi motivi per cui le applicazioni client non sono in grado di connettersi a un argomento o dominio di griglia di eventi. I problemi di connettività che si verificano potrebbero essere permanenti o temporanei. Per informazioni su come risolvere questi problemi, vedere [risolvere i problemi di connettività](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Codici di errore
Se vengono visualizzati messaggi di errore con codici di errore, ad esempio 400, 409 e 403, vedere [risolvere gli errori di griglia di eventi](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Traccia distribuita (.NET)
La libreria .NET di griglia di eventi supporta la distribuzione della traccia. Per rispettare le [linee guida della specifica CloudEvents](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) sulla distribuzione della traccia, la libreria imposta `traceparent` e `tracestate` in [ExtensionAttributes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization#L126) di una `CloudEvent` quando è abilitata la traccia distribuita. Per altre informazioni su come abilitare la traccia distribuita nell'applicazione, vedere la [documentazione relativa alla traccia distribuita](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)di Azure SDK.

### <a name="sample"></a>Esempio
Vedere l' [esempio di contatore di righe](/samples/azure/azure-sdk-for-net/line-counter/). Questa app di esempio illustra l'uso di archiviazione, Hub eventi e client di griglia di eventi insieme a ASP.NET Core integrazione, traccia distribuita e servizi ospitati. Consente agli utenti di caricare un file in un BLOB, che attiva un evento di hub eventi contenente il nome del file. Il processore di hub eventi riceve l'evento, quindi l'app Scarica il BLOB e conta il numero di righe nel file. L'app Visualizza un collegamento a una pagina che contiene il conteggio delle righe. Quando si fa clic sul collegamento, un CloudEvent contenente il nome del file viene pubblicato usando griglia di eventi.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, pubblicare il problema nel [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) oppure aprire un [ticket di supporto](https://azure.microsoft.com/support/options/). 
