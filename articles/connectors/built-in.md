---
title: Trigger e azioni predefiniti per App per la logica di Azure
description: Usare trigger e azioni predefiniti per creare flussi di lavoro automatizzati che integrano app, dati, servizi e sistemi, per controllare i flussi di lavoro e per gestire i dati usando App per la logica di Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796928"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Trigger e azioni predefiniti per App per la logica


[I trigger e](apis-list.md) le azioni predefiniti consentono di controllare la pianificazione e la [](#manage-or-manipulate-data)struttura del flusso di [lavoro,](#control-workflow)eseguire codice [personalizzato,](#run-code-from-workflows)gestire o modificare i dati e completare altre attività nei flussi di lavoro. Diversamente dai [connettori gestiti,](managed.md)molte operazioni incorporate non sono collegate a un servizio, un sistema o un protocollo specifico. Ad esempio, è possibile avviare quasi tutti i flussi di lavoro in base a una pianificazione usando il trigger Ricorrenza. In caso contrario, è possibile fare in modo che il flusso di lavoro attenda fino a quando non viene chiamato usando il trigger di richiesta. Tutte le operazioni predefinite vengono eseguite in modo nativo nel servizio App per la logica e per la maggior parte non è necessario creare una connessione prima di usarle. 

App per la logica offre anche operazioni predefinite per un numero minore di servizi, sistemi e protocolli, ad esempio bus di servizio di Azure, Funzioni di Azure, SQL, AS2 e così via. Il numero e l'intervallo variano a seconda che si crei un'app per la logica multi-tenant o un'app per la logica a tenant singolo. In alcuni casi sono disponibili sia una versione predefinita che una versione del connettore gestito. Nella maggior parte dei casi, la versione predefinita offre prestazioni, funzionalità, prezzi e così via migliori. Ad esempio, per scambiare messaggi B2B usando il protocollo [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md)selezionare la versione predefinita, a meno che non siano necessarie funzionalità di rilevamento, disponibili solo nella versione del connettore gestito (deprecata).

Nell'elenco seguente vengono descritte solo alcune delle attività che è possibile eseguire con trigger e [azioni predefiniti:](#understand-triggers-and-actions)

- Eseguire flussi di lavoro usando pianificazioni personalizzate e avanzate. Per altre informazioni sulla pianificazione, vedere la sezione sul comportamento di ricorrenza in Panoramica del connettore app [per la logica.](apis-list.md#recurrence-behavior)
- Organizzare e controllare la struttura del flusso di lavoro, ad esempio usando cicli e condizioni.
- Usare variabili, date, operazioni sui dati, trasformazioni del contenuto e operazioni batch.
- Comunicare con altri endpoint usando trigger e azioni HTTP.
- Ricevere e rispondere alle richieste.
- Chiamare funzioni personalizzate (Funzioni di Azure), app Web (app Azure Services), API (Azure API Management), altri flussi di lavoro di App per la logica che possono ricevere richieste e così via.

## <a name="understand-triggers-and-actions"></a>Informazioni su trigger e azioni

App per la logica offre i trigger e le azioni predefiniti seguenti:

:::row:::
    :::column:::
        [![Icona Pianifica in App per la logica][schedule-icon]][schedule-doc]
        \
        \
        [**Programma**][schedule-doc]
        \
        \
        [**Ricorrenza:**][schedule-recurrence-doc]attiva un flusso di lavoro in base alla ricorrenza specificata.
        \
        \
        [**Finestra temporale scorrevole:**][schedule-sliding-window-doc]attiva un flusso di lavoro che deve gestire i dati in blocchi continui.
        \
        \
        [**Delay:**][schedule-delay-doc]sospende il flusso di lavoro per la durata specificata.
        \
        \
        [**Ritarda fino a**][schedule-delay-until-doc]: sospende il flusso di lavoro fino alla data e all'ora specificate.
    :::column-end:::
    :::column:::
        [![Icona di Batch in App per la logica][batch-icon]][batch-doc]
        \
        \
        [**lotto**][batch-doc]
        \
        \
        [**Messaggi batch:**][batch-doc]attiva un flusso di lavoro che elabora i messaggi in batch.
        \
        \
        [**Invia messaggi al batch:**][batch-doc]chiamare un flusso di lavoro esistente che attualmente inizia con un trigger **di messaggi batch.**
    :::column-end:::
    :::column:::
        [![Icona HTTP in App per la logica][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Chiamare un endpoint HTTP o HTTPS usando il trigger o l'azione HTTP. 
        \
        \
        È anche possibile usare questi altri trigger e azioni HTTP predefiniti: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Icona richiesta][http-request-icon]][http-request-doc]
        \
        \
        [**Richiesta**][http-request-doc]
        \
        \
        [**Quando viene ricevuta una richiesta HTTP:**][http-request-doc]attendere una richiesta da un altro flusso di lavoro, app o servizio. Questo trigger rende il flusso di lavoro chiamabile senza dover essere controllato o sottoposto a polling in base a una pianificazione.
        \
        \
        [**Risposta:**][http-request-doc]rispondere a una richiesta ricevuta dal trigger Quando viene ricevuta una **richiesta HTTP** nello stesso flusso di lavoro.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona API Management azure in App per la logica][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Chiamare trigger e azioni personalizzati nelle API definite, gestite e pubblicate usando [Azure API Management](../api-management/api-management-key-concepts.md). <p><p>**Nota:** non supportato quando si usa [il livello a consumo per API Management](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![icona app Azure Services in App per la logica][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Chiamare le app create e ospitate [in Servizio app di Azure,](../app-service/overview.md)ad esempio App per le API e App Web.
        \
        \
        Quando Swagger è incluso, i trigger e le azioni definiti da queste app vengono visualizzati come qualsiasi altro trigger e azione di prima App per la logica di Azure.
    :::column-end:::
    :::column:::
        [![App per la logica di Azure icona in App per la logica][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**App per la logica di Azure**][nested-logic-app-doc]
        \
        \
        Chiamare altri flussi di lavoro che iniziano con il trigger di richiesta **denominato Quando viene ricevuta una richiesta HTTP.**
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Eseguire codice dai flussi di lavoro

App per la logica offre azioni predefinite per l'esecuzione di codice personalizzato nel flusso di lavoro:

:::row:::
    :::column:::
        [![Funzioni di Azure in App per la logica][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Funzioni di Azure**][azure-functions-doc]
        \
        \
        Chiamare [funzioni ospitate in Azure per](../azure-functions/functions-overview.md) eseguire frammenti di codice personalizzati (C# o Node.js) all'interno del flusso di lavoro. 
    :::column-end:::
    :::column:::
        [![Icona codice inline in App per la logica][inline-code-icon]][inline-code-doc]
        \
        \
        [**Codice inline**][inline-code-doc]
        \
        \
        [**Esegui codice JavaScript:**][inline-code-doc]aggiungere ed eseguire frammenti di codice JavaScript inline *all'interno* del flusso di lavoro.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Controllare il flusso di lavoro

App per la logica offre azioni predefinite per strutturare e controllare le azioni nel flusso di lavoro:

:::row:::
    :::column:::
        [![Icona dell'azione Condizione in App per la logica][condition-icon]][condition-doc]
        \
        \
        [**Condizione**][condition-doc]
        \
        \
        Valutare una condizione ed eseguire diverse azioni in base al fatto che la condizione sia true o false.
    :::column-end:::
    :::column:::
        [![Icona di azione For Each in App per la logica][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        Eseguire le stesse azioni su ogni elemento in una matrice.
    :::column-end:::
    :::column:::
        [![Icona di azione Ambito in App per la logica][scope-icon]][scope-doc]
        \
        \
        [**Nome**][scope-doc]
        \
        \
        Raggruppare le azioni in *ambiti*, che ottengono un proprio stato al termine delle azioni nell'ambito.
    :::column-end:::
    :::column:::
        [![Icona Cambia azione in App per la logica][switch-icon]][switch-doc]
        \
        \
        [**Interruttore**][switch-doc]
        \
        \
        Raggruppare le azioni in *case*, ai quali vengono assegnati valori univoci ad eccezione del case predefinito. Eseguire solo il case il cui valore assegnato corrisponde al risultato di un'espressione, oggetto o token. Se non è presente alcuna corrispondenza, eseguire il case predefinito.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona di azione Termina in App per la logica][terminate-icon]][terminate-doc]
        \
        \
        [**Terminare**][terminate-doc]
        \
        \
        Arrestare un flusso di lavoro di un'applicazione logica in esecuzione attiva. 
    :::column-end:::
    :::column:::
        [![Icona fino all'azione in App per la logica][until-icon]][until-doc]
        \
        \
        [**fino a**][until-doc]
        \
        \
        Ripetere azioni fino a quando non viene soddisfatta la condizione specificata o è stato modificato uno stato.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Gestire o manipolare i dati

App per la logica offre azioni predefinite per l'uso degli output dei dati e dei relativi formati:

:::row:::
    :::column:::
        [![Icona dell'azione Operazioni dati in App per la logica][data-operations-icon]][data-operations-doc]
        \
        \
        [**Operazioni sui dati**][data-operations-doc]
        \
        \
        Eseguire operazioni con i dati. 
        \
        \
        **Componi**: crea un singolo output da più input con tipi diversi. 
        \
        \
        **Crea tabella CSV**: creare una tabella CSV (valore separato da virgole) da una matrice con oggetti JSON. 
        \
        \
        **Crea tabella HTML**: crea una tabella HTML da una matrice di oggetti JSON. 
        \
        \
        **Filtra matrice**: creare una matrice da elementi di un'altra matrice che soddisfano i criteri specificati. 
        \
        \
        **Join**: crea una stringa da tutti gli elementi in una matrice e separa gli elementi con il delimitatore specificato. 
        \
        \
        **Analizza JSON:** creare token descrittivi dalle proprietà e dai relativi valori nel contenuto JSON in modo che sia possibile usare tali proprietà nel flusso di lavoro. 
        \
        \
        **Seleziona**: crea una matrice con oggetti JSON trasformando elementi o valori in un’altra matrice ed eseguendo il mapping di tali elementi per proprietà specifiche.
    :::column-end:::
    :::column:::
        ![Icona dell'azione Data e ora in App per la logica][date-time-icon]
        \
        \
        **Data e ora**
        \
        \
        Eseguire operazioni con timestamp.
        \
        \
        **Aggiungi a time**: aggiunge il numero di unità specificato a un timestamp. 
        \
        \
        **Converti fuso orario**: converte un timestamp dal fuso orario di origine al fuso orario di destinazione. 
        \
        \
        **Tempo corrente**: restituisce il timestamp corrente come stringa. 
        \
        \
        **Recupera time futuro**: restituisce il timestamp corrente più le unità di tempo specificate. 
        \
        \
        **Recupera time passato**: restituisce il timestamp corrente meno le unità di tempo specificate. 
        \
        \
        **Sottrazione dall'ora:** sottrae un numero di unità di tempo da un timestamp.
    :::column-end:::
    :::column:::
        [![Icona di azione Variabili in App per la logica][variables-icon]][variables-doc]
        \
        \
        [**Variabili**][variables-doc]
        \
        \
        Eseguire operazioni con variabili.
        \
        \
        **Accoda a variabile di matrice**: inserisce un valore come l'ultimo elemento nella matrice memorizzato da una variabile. 
        \
        \
        **Accoda a variabile di stringa**: inserire un valore come l'ultimo carattere in una stringa archiviata da una variabile.
        \
        \
        **Decrementa variabile**: riduce una variabile di un valore costante.
        \
        \
        **Incrementa variabile**: aumenta una variabile di un valore costante. 
        \
        \
        **Inizializza variabile**: crea una variabile e dichiara il tipo di dati e il valore iniziale. 
        \
        \
        **Imposta variabile**: assegna un valore diverso ad una variabile esistente. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare API personalizzate che è possibile chiamare da App per la logica](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Creare un'istanza del servizio Gestione API di Azure per gestire e pubblicare le proprie API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrare le app per la logica con le app per le API del servizio app"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrate logic apps with Azure Functions" (Integrazione delle app per la logica con Funzioni di Azure)
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Elaborare i messaggi in gruppi o come batch"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Valutare una condizione ed eseguire azioni diverse a seconda che la condizione sia true o false"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Eseguire le stesse azioni su ogni elemento in una matrice"
[http-doc]: ./connectors-native-http.md "Chiamare endpoint HTTP o HTTPS dalle app per la logica"
[http-request-doc]: ./connectors-native-reqres.md "Ricevere richieste HTTP nelle app per la logica"
[http-response-doc]: ./connectors-native-reqres.md "Rispondere alle richieste HTTP dalle app per la logica"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Chiamare gli endpoint REST dalle app per la logica"
[http-webhook-doc]: ./connectors-native-webhook.md "Attendere eventi specifici da endpoint HTTP o HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Aggiungere ed eseguire frammenti di codice JavaScript dalle app per la logica"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrare le app per la logica con flussi di lavoro annidati"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selezionare e filtrare matrici con l'azione di query"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Eseguire app per la logica in base a una pianificazione"
[schedule-delay-doc]: ./connectors-native-delay.md "Ritardare l'esecuzione dell'azione successiva"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Ritardare l'esecuzione dell'azione successiva"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Eseguire app per la logica in base a una pianificazione ricorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Eseguire app per la logica che devono gestire i dati in blocchi contigui"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizza le azioni in gruppi, che ottengono un proprio stato dopo la fine delle azioni in gruppo"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizzare le azioni in casi a cui vengono assegnati valori univoci. Eseguire solo il case il cui valore corrisponde al risultato di un'espressione, un oggetto o un token. Se non sono presenti corrispondenze, eseguire il case predefinito"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrestare o annullare un flusso di lavoro attivamente in esecuzione per l'app per la logica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ripetere le azioni fino a quando la condizione specificata non è true o non viene modificato uno stato"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Eseguire operazioni sui dati, ad esempio il filtro di matrici o la creazione di tabelle CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Eseguire operazioni con variabili, ad esempio inizializzazione, set, incremento, decremento e accodare a variabile di stringa o matrice"
