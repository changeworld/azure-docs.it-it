---
title: Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi | Microsoft Docs
description: Questo articolo fornisce una descrizione del modo in cui la messaggistica del bus di servizio di Azure si integra con griglia di eventi di Azure.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369663"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi
Il bus di servizio può ora inviare eventi a Griglia di eventi quando una coda o una sottoscrizione contiene messaggi e non sono presenti ricevitori. È possibile creare sottoscrizioni di Griglia di eventi per gli spazi dei nomi del bus di servizio, restare in ascolto di tali eventi e quindi reagire a essi avviando un ricevitore. Questa funzionalità consente di usare il bus di servizio in modelli di programmazione reattivi. Il principale scenario di questa funzionalità è la possibilità, in caso di code e sottoscrizioni del bus di servizio con un volume ridotto di messaggi, di non avere un ricevitore che esegue costantemente il polling dei messaggi. 

Per abilitare questa funzionalità, sono necessari gli elementi seguenti:

* Spazio dei nomi Premium del bus di servizio con almeno una coda o un argomento del bus di servizio con almeno una sottoscrizione.
* Accesso di tipo Collaboratore allo spazio dei nomi del bus di servizio. Passare allo spazio dei nomi del bus di servizio nel portale di Azure, quindi selezionare **controllo di accesso (IAM)** e selezionare scheda **assegnazioni di ruolo** . Verificare di disporre dell'accesso collaboratore allo spazio dei nomi. 
* Sottoscrizione di Griglia di eventi per lo spazio dei nomi del bus di servizio. Questa sottoscrizione riceve una notifica da Griglia di eventi che segnala la presenza di messaggi da prelevare. I sottoscrittori tipici potrebbero essere la funzionalità App per la logica di Servizio app di Azure, Funzioni di Azure o un webhook che contatta un'app Web. Il sottoscrittore elabora quindi i messaggi. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Sottoscrizioni di griglia di eventi per spazi dei nomi del bus di servizio
È possibile creare sottoscrizioni di Griglia di eventi per gli spazi dei nomi del bus di servizio in tre diversi modi:

- portale di Azure. Per informazioni su come usare portale di Azure per creare sottoscrizioni di griglia di eventi per gli eventi del bus di servizio con app per la logica di Azure e funzioni di Azure come gestori, vedere le esercitazioni seguenti. 
    - [App per la logica di Azure](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Funzioni di Azure](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* Interfaccia della riga di comando di Azure. L'esempio CLI seguente mostra come creare una sottoscrizione di funzioni di Azure per un [argomento di sistema](../event-grid/system-topics.md) creato da uno spazio dei nomi del bus di servizio.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Ecco un esempio:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Numero di eventi emessi e frequenza di emissione

Se si hanno più code e argomenti o sottoscrizioni nello spazio dei nomi, vengono generati almeno un evento per coda e un evento per sottoscrizione. Gli eventi vengono emessi immediatamente se non sono presenti messaggi nell'entità del bus di servizio e arriva un nuovo messaggio oppure vengono emessi ogni due minuti a meno che il bus di servizio non rilevi un ricevitore attivo. L'esplorazione di messaggi non interrompe gli eventi.

Per impostazione predefinita, il bus di servizio emette eventi per tutte le entità nello spazio dei nomi. Se si vogliono ottenere eventi solo per entità specifiche, vedere la sezione successiva.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Usare i filtri per limitare le origini da cui è possibile ottenere gli eventi

Se si vogliono ottenere eventi solo da una coda o una sottoscrizione nello spazio dei nomi, ad esempio, è possibile usare i filtri *Inizia con* o *Termina con* offerti da Griglia di eventi, che in alcune interfacce sono denominati filtri per *prefisso* e *suffisso*. Se si vogliono ottenere eventi per più code e sottoscrizioni ma non per tutte, è possibile creare più sottoscrizioni di Griglia di eventi e specificare un filtro per ognuna.

## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti: 
- [App per la logica di Azure per gestire i messaggi del bus di servizio ricevuti tramite griglia di eventi](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Funzioni di Azure per gestire i messaggi del bus di servizio ricevuti tramite griglia di eventi](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
