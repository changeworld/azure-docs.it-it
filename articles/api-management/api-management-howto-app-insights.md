---
title: Integrare Gestione API di Azure con applicazione Azure Insights
titleSuffix: Azure API Management
description: Informazioni su come registrare e visualizzare gli eventi da Gestione API di Azure in Azure Application Insights.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564269"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Come integrare Gestione API di Azure con Azure Application Insights

Gestione API di Azure consente una facile integrazione con Azure Application Insights, un servizio estendibile per sviluppatori Web che compilano e gestiscono app in più piattaforme. Questa guida illustra ogni passaggio di tale integrazione e descrive le strategie per ridurre l'impatto sulle prestazioni nell'istanza del servizio Gestione API.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa guida, è necessario avere un'istanza di Gestione API di Azure. Se non è disponibile, completare prima l'[esercitazione](get-started-create-service-instance.md).

## <a name="create-an-application-insights-instance"></a>Creare un'istanza di Application Insights

Prima di poter usare Application Insights, è prima di tutto necessario creare un'istanza del servizio. Per i passaggi necessari per creare un'istanza di usando il portale di Azure, vedere [risorse Application Insights basate sull'area di lavoro](../azure-monitor/app/create-workspace-resource.md).
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Creare una connessione tra Application Insights e gestione API

1. Passare all' **istanza del servizio gestione API di Azure** nel **portale di Azure**.
1. Selezionare **Application Insights** nel menu a sinistra.
1. Fare clic su **+ Aggiungi**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="Screenshot che mostra dove aggiungere una nuova connessione":::
1. Selezionare l'istanza di **Application Insights** creata in precedenza e immettere una breve descrizione.
1. Fare clic su **Crea**.
1. È stata appena creata una Application Insights logger con una chiave di strumentazione. Verrà ora visualizzato nell'elenco.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="Screenshot che mostra dove visualizzare il logger Application Insights appena creato con la chiave di strumentazione":::

> [!NOTE]
> A parte, viene creata un'entità [Logger](/rest/api/apimanagement/2019-12-01/logger/createorupdate) nell'istanza di gestione API, contenente la chiave di strumentazione dell'istanza di Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Abilitare la registrazione di Application Insights per l'API

1. Passare all' **istanza del servizio gestione API di Azure** nel **portale di Azure**.
1. Selezionare **API** nel menu a sinistra.
1. Fare clic sull'API, in questo caso **Demo Conference API**. Se configurata, selezionare una versione.
1. Passare alla scheda **Impostazioni** dalla barra in alto.
1. Scorrere verso il basso fino alla sezione **Log di diagnostica**.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Logger di Application Insights":::
1. Selezionare la casella **Abilita**.
1. Selezionare il logger associato nell'elenco a discesa **Destinazione**.
1. Input **100** As **Sampling (%)** e selezionare la casella di controllo **Always log Errors** .
1. Selezionare **Salva**.

> [!WARNING]
> L'override del valore predefinito **0** nell'impostazione **numero di byte di payload da registrare** può ridurre significativamente le prestazioni delle API.

> [!NOTE]
> A parte viene creata un'entità [Diagnostica](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) denominata "applicationinsights" a livello di API.

| Nome impostazione                        | Tipo di valore                        | Descrizione                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abilita                              | boolean                           | Specifica se la registrazione di questa API è abilitata.                                                                                                                                                                                                                                                                                                |
| Destination                         | Logger di Azure Application Insights | Specifica il logger di Azure Application Insights da usare                                                                                                                                                                                                                                                                                           |
| Sampling (%) (Campionamento - %)                        | decimal                           | Valori compresi tra 0 e 100 (percentuale). <br/> Specifica la percentuale di richieste che verranno registrate Application Insights. Un campionamento pari allo 0% indica che verranno registrate zero richieste, mentre un campionamento del 100% indica che verranno registrate tutte le richieste. <br/> Usare questa impostazione per ridurre l'effetto sulle prestazioni durante la registrazione delle richieste a Application Insights. Vedere [implicazioni sulle prestazioni e campionamento del log](#performance-implications-and-log-sampling). |
| Always log errors (Registra sempre gli errori)                   | boolean                           | Se questa impostazione è selezionata, tutti gli errori verranno registrati in Application Insights, indipendentemente dall'impostazione del **campionamento** .   
| Indirizzo IP del client di log | |  Se questa impostazione è selezionata, l'indirizzo IP del client per le richieste API verrà registrato in Application Insights.                                         |
| Livello di dettaglio         |                                   | Specifica il livello di dettaglio. Verranno registrate solo le tracce personalizzate con livello di gravità superiore. Impostazione predefinita: Information.      | 
| Protocollo di correlazione |  |  Selezionare il protocollo usato per correlare i dati di telemetria inviati da più componenti. Impostazione predefinita: Legacy <br/>Per informazioni, vedere [correlazione di telemetria in Application Insights](../azure-monitor/app/correlation.md).  |
| Opzioni di base: intestazioni da registrare              | list                              | Specifica le intestazioni che verranno registrate per Application Insights per le richieste e le risposte.  Impostazione predefinita: non vengono registrate intestazioni.                                                                                                                                                                                                             |
| Opzioni di base: numero di byte del payload da registrare | numero intero                           | Specifica il numero di primi byte del corpo registrati per Application Insights per le richieste e le risposte.  Valore predefinito: 0.                                                                                                                                                                                                    |
| Opzioni avanzate: Frontend Request (Richiesta front-end)  |                                   | Specifica se e come verranno registrate *le richieste* front-end per Application Insights. La *richiesta front-end* è una richiesta in arrivo al servizio Gestione API di Azure.                                                                                                                                                                        |
| Opzioni avanzate: Frontend Response (Risposta front-end) |                                   | Specifica se e come verranno registrate le risposte front- *end* per Application Insights. La *risposta front-end* è una risposta in uscita dal servizio Gestione API di Azure.                                                                                                                                                                   |
| Opzioni avanzate: Backend Request (Richiesta back-end)   |                                   | Specifica se e come verranno registrate *le richieste back-end* per Application Insights. La *richiesta back-end* è una richiesta in uscita dal servizio Gestione API di Azure.                                                                                                                                                                        |
| Opzioni avanzate: Backend Response (Risposta back-end)  |                                   | Specifica se e come verranno registrate le *risposte back-end* per Application Insights. La *risposta back-end* è una risposta in arrivo al servizio Gestione API di Azure.                                                                                                                                                                       |

> [!NOTE]
> È possibile specificare i logger a livelli diversi: logger per singole API o un logger per tutte le API.
>  
> Se si specificano entrambi:
> + Se sono logger diversi, verranno usati entrambi (multiplexing di log).
> + Se sono gli stessi logger, ma hanno impostazioni diverse, quello per l'API singola (livello più granulare) eseguirà l'override di quello per tutte le API.

## <a name="what-data-is-added-to-application-insights"></a>Quali dati vengono aggiunti a Application Insights

Application Insights riceve:

+ Elemento di telemetria *richiesta*, per ogni richiesta in ingresso (*richiesta front-end*, *risposta front-end*).
+ Elemento di telemetria *dipendenza*, per ogni richiesta inoltrata a un servizio back-end (*richiesta back-end*, *risposta back-end*).
+ Elemento di telemetria delle *eccezioni* per ogni richiesta non riuscita:
    + operazione non riuscita a causa di una connessione client chiusa
    + attivata una sezione di *errore* dei criteri API
    + Ha un codice di stato HTTP della risposta corrispondente a 4xx o 5xx.
+ Elemento di telemetria di *traccia* , se si configura un criterio di [traccia](api-management-advanced-policies.md#Trace) . L' `severity` impostazione del `trace` criterio deve essere maggiore o uguale a quella del `verbosity` Application Insights registrazione.

> [!NOTE]
> Per informazioni sulle dimensioni massime e sul numero di metriche ed eventi per Application Insights istanza, vedere [Application Insights limiti](../azure-monitor/service-limits.md#application-insights) .

## <a name="performance-implications-and-log-sampling"></a>Implicazioni sulle prestazioni e campionamento dei log

> [!WARNING]
> La registrazione di tutti gli eventi può comportare gravi implicazioni per le prestazioni, a seconda della frequenza delle richieste in ingresso.

In base ai test di carico interni, l'abilitazione di questa funzionalità ha causato una riduzione del 40%-50% della velocità effettiva quando la frequenza delle richieste superava le 1.000 richieste al secondo. Application Insights è progettato per l'utilizzo dell'analisi statistica per la valutazione delle prestazioni dell'applicazione. Non è pensato come sistema di controllo e non è adatto alla registrazione di ogni singola richiesta di API con volumi elevati.

È possibile modificare il numero di richieste registrate regolando l'impostazione di **campionamento** (vedere i passaggi precedenti). Il valore 100% indica che tutte le richieste vengono registrate, mentre 0% non riflette alcuna registrazione. Il **campionamento** consente di ridurre il volume dei dati di telemetria, evitando in tal modo un calo significativo delle prestazioni, pur continuando a sfruttare i vantaggi

Evitando di registrare le intestazioni e il corpo di richieste e risposte, si riducono considerevolmente anche i problemi di prestazioni.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni su [Azure Application Insights](/azure/application-insights/).
+ Considerare la possibilità di eseguire la [registrazione con Hub eventi di Azure](api-management-howto-log-event-hubs.md).
