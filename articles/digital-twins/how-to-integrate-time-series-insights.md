---
title: Integrazione con Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Informazioni su come configurare route di eventi da Gemelli digitali di Azure a Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783774"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrare Gemelli digitali di Azure con Azure Time Series Insights

In questo articolo si apprenderà come integrare Gemelli digitali di Azure con [Azure Time Series Insights (TSI).](../time-series-insights/overview-what-is-tsi.md)

La soluzione descritta in questo articolo consentirà di raccogliere e analizzare i dati cronologici sulla soluzione IoT. Il servizio Gemelli digitali di Azure è una scelta ideale per l'inserimento di dati in Time Series Insights, perché consente di correlare più flussi di dati e di standardizzare le informazioni prima di inviarle a Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare una relazione con Time Series Insights, è necessario configurare le risorse seguenti:
* Un **hub IoT**. Per istruzioni, vedere la sezione [*Creare un hub IoT*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) dell'avvio rapido Inviare dati di telemetria *dell'hub IoT.*
* Istanza **Gemelli digitali di Azure.**
Per istruzioni, vedere [*Procedura: Configurare un'istanza di Gemelli digitali di Azure e l'autenticazione.*](./how-to-set-up-instance-portal.md)
* Un **modello e un gemello nell'istanza Gemelli digitali di Azure .**
È necessario aggiornare alcune volte le informazioni del dispositivo gemello per visualizzare i dati di cui viene Time Series Insights. Per istruzioni, vedere la sezione [*Aggiungere un modello e un dispositivo gemello*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) dell'articolo *Procedura: Inserire un hub IoT.*

> [!TIP]
> In questo articolo i valori dei gemelli digitali che vengono visualizzati in Time Series Insights vengono aggiornati manualmente per semplicità. Tuttavia, se si desidera completare questo articolo con dati simulati in tempo reale, è possibile configurare una funzione di Azure che aggiorna i gemelli digitali in base agli eventi di telemetria IoT da un dispositivo simulato. Per istruzioni, seguire Procedura: Inserire i dati [*dell'hub IoT,*](how-to-ingest-iot-hub-data.md)inclusi i passaggi finali per eseguire il simulatore di dispositivi e convalidare il funzionamento del flusso di dati.
>
> Successivamente, cercare un altro suggerimento per mostrare dove iniziare a eseguire il simulatore di dispositivi e fare in modo che le funzioni di Azure aggiornetino automaticamente i gemelli, invece di inviare comandi di aggiornamento manuale dei gemelli digitali.


## <a name="solution-architecture"></a>Architettura della soluzione

Verrà collegato un Time Series Insights a Gemelli digitali di Azure tramite il percorso seguente.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagramma dei servizi di Azure in uno scenario end-to-end, con Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Creare lo spazio dei nomi dell'hub eventi

Prima di creare gli hub eventi, si creerà prima uno spazio dei nomi dell'hub eventi che riceverà eventi dall'istanza Gemelli digitali di Azure eventi. È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure seguenti o portale di Azure: [*Guida introduttiva: Creare*](../event-hubs/event-hubs-create.md)un hub eventi usando portale di Azure . Per informazioni su quali aree supportano gli hub eventi, visitare [*i prodotti Azure disponibili per area*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Se viene visualizzato un errore che indica , assicurarsi che il nome scelto per lo spazio dei nomi soddisfi i requisiti di denominazione descritti in questo documento di riferimento: Creare lo spazio `BadRequest: The specified service namespace is invalid.` [dei nomi](/rest/api/servicebus/create-namespace).

Questo spazio dei nomi dell'hub eventi verrà utilizzato per contenere i due hub eventi necessari per questo articolo:

  1. **Hub gemelli -** Hub eventi per ricevere eventi di modifica dei dispositivi gemelli
  2. **Hub serie temporizzato** - Hub eventi per trasmettere gli eventi Time Series Insights

Le sezioni successive illustrano come creare e configurare questi hub all'interno dello spazio dei nomi dell'hub eventi.

## <a name="create-twins-hub"></a>Creare l'hub gemelli

Il primo hub eventi che verrà creato in questo articolo è **l'hub gemelli**. Questo hub eventi riceverà eventi di modifica gemelli da Gemelli digitali di Azure.
Per configurare l'hub gemelli, completare i passaggi seguenti in questa sezione:

1. Creare l'hub gemelli
2. Creare una regola di autorizzazione per controllare le autorizzazioni per l'hub
3. Creare un endpoint in Gemelli digitali di Azure che usa la regola di autorizzazione per accedere all'hub
4. Creare una route in un Gemelli digitali di Azure che invia l'evento degli aggiornamenti dei dispositivi gemelli all'endpoint e all'hub gemelli connessi
5. Ottenere la stringa di connessione dell'hub gemelli

Creare **l'hub gemelli con** il comando dell'interfaccia della riga di comando seguente. Specificare un nome per l'hub gemelli.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Creare una regola di autorizzazione dell'hub gemelli

Creare una [regola di autorizzazione](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) con autorizzazioni di invio e ricezione. Specificare un nome per la regola.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Creare l'endpoint dell'hub dei dispositivi gemelli

Creare un endpoint Gemelli digitali di Azure [che](concepts-route-events.md#create-an-endpoint) collega l'hub eventi all'Gemelli digitali di Azure istanza. Specificare un nome per l'endpoint dell'hub dei dispositivi gemelli.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Creare una route di eventi dell'hub gemelli

Gemelli digitali di Azure possono generare eventi [di aggiornamento dei](how-to-interpret-event-data.md) dispositivi gemelli ogni volta che viene aggiornato lo stato di un gemello. In questa sezione si creerà una **route** Gemelli digitali di Azure eventi che instraderà questi eventi di aggiornamento all'hub dei gemelli per un'ulteriore elaborazione.

Creare una [route in Gemelli digitali di Azure](concepts-route-events.md#create-an-event-route) inviare gli eventi di aggiornamento dei dispositivi gemelli all'endpoint precedente. Il filtro in questa route consentirà solo ai messaggi di aggiornamento dei dispositivi gemelli di essere passati all'endpoint. Specificare un nome per la route eventi dell'hub dei gemelli.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub dei gemelli

Ottenere la [stringa di connessione dell'hub](../event-hubs/event-hubs-get-connection-string.md)eventi dei dispositivi gemelli usando le regole di autorizzazione create in precedenza per l'hub dei gemelli.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Prendere nota del valore **primaryConnectionString** del risultato per configurare l'impostazione dell'app hub gemelli più avanti in questo articolo.

## <a name="create-time-series-hub"></a>Creare l'hub della serie temporale

Il secondo hub eventi che verrà creato in questo articolo è **l'hub della serie temporale.** Si tratta di un hub eventi che trasmettere gli eventi Gemelli digitali di Azure per Time Series Insights.
Per configurare l'hub della serie temporale, completare questi passaggi:

1. Creare l'hub della serie temporale
2. Creare una regola di autorizzazione per controllare le autorizzazioni per l'hub
3. Ottenere la stringa di connessione dell'hub time series

In seguito, quando si crea l'istanza Time Series Insights, si connetterà questo hub time series come origine evento per l'Time Series Insights istanza.

Creare **l'hub della serie temporale** usando il comando seguente. Specificare un nome per l'hub della serie temporale.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Creare una regola di autorizzazione dell'hub time series

Creare una [regola di autorizzazione](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) con autorizzazioni di invio e ricezione. Specificare un nome per la regola di autenticazione dell'hub time series.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub della serie temporale

Ottenere la [stringa di connessione dell'hub time series](../event-hubs/event-hubs-get-connection-string.md)usando le regole di autorizzazione create in precedenza per l'hub time series:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Prendere nota del valore **primaryConnectionString** del risultato per configurare l'impostazione dell'app hub time series più avanti in questo articolo.

Prendere anche nota dei valori seguenti per usarli in un secondo momento per creare un'Time Series Insights istanza.
* Spazio dei nomi dell'hub eventi
* Nome dell'hub della serie temporale
* Regola di autenticazione dell'hub della serie temporale

## <a name="create-a-function"></a>Creare una funzione

In questa sezione si creerà una funzione di Azure che convertirà gli eventi di aggiornamento dei dispositivi gemelli dal formato originale come documenti JSON Patch in oggetti JSON, contenenti solo valori aggiornati e aggiunti dai gemelli.

### <a name="step-1-create-function-app"></a>Passaggio 1: Creare un'app per le funzioni

Creare prima di tutto un nuovo progetto di app per le funzioni in Visual Studio. Per istruzioni su come eseguire questa operazione, vedere la sezione Creare [**un'app**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) per le funzioni in Visual Studio dell'articolo Procedura: Configurare una funzione per *l'elaborazione dei dati.*

### <a name="step-2-add-a-new-function"></a>Passaggio 2: Aggiungere una nuova funzione

Creare una nuova funzione di Azure denominata *ProcessDTUpdatetoTSI.cs* per aggiornare gli eventi di telemetria del dispositivo Time Series Insights. Il tipo di funzione sarà **trigger dell'hub eventi.**

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Screenshot della Visual Studio creare una nuova funzione di Azure di tipo trigger dell'hub eventi.":::

### <a name="step-3-fill-in-function-code"></a>Passaggio 3: Compilare il codice della funzione

Aggiungere i pacchetti seguenti al progetto:
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Sostituire il codice nel file *ProcessDTUpdatetoTSI.cs* con il codice seguente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Salvare il codice della funzione.

### <a name="step-4-publish-the-function-app-to-azure"></a>Passaggio 4: Pubblicare l'app per le funzioni in Azure

Pubblicare il progetto con la *funzione ProcessDTUpdatetoTSI.cs* in un'app per le funzioni in Azure.

Per istruzioni su come eseguire questa operazione, vedere la sezione Pubblicare [**l'app**](how-to-create-azure-function.md#publish-the-function-app-to-azure) per le funzioni in Azure dell'articolo Procedura: Configurare una funzione per *l'elaborazione dei* dati.

Salvare il nome dell'app per le funzioni da usare in un secondo momento per configurare le impostazioni dell'app per i due hub eventi.

### <a name="step-5-security-access-for-the-function-app"></a>Passaggio 5: Accesso di sicurezza per l'app per le funzioni

Assegnare **quindi un ruolo di accesso** per la funzione e configurare le impostazioni **dell'applicazione** in modo che possa accedere all'Gemelli digitali di Azure istanza. Per istruzioni su come eseguire questa operazione, vedere la sezione Configurare l'accesso di sicurezza per [**l'app**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) per le funzioni dell'articolo *Procedura: Configurare* una funzione per l'elaborazione dei dati.

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Passaggio 6: Configurare le impostazioni dell'app per i due hub eventi

Successivamente, si aggiungeranno le variabili di ambiente nelle impostazioni dell'app per le funzioni che consentono di accedere all'hub gemelli e all'hub serie temporale.

Usare il valore **primaryConnectionString** dell'hub gemelli salvato in precedenza per creare un'impostazione dell'app nell'app per le funzioni che contiene la stringa di connessione dell'hub dei gemelli:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Usare il valore **primaryConnectionString** dell'hub serie temporale salvato in precedenza per creare un'impostazione dell'app nell'app per le funzioni che contiene la stringa di connessione dell'hub della serie temporale:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Creare e connettere un'istanza di Time Series Insights

In questa sezione si configura un'istanza Time Series Insights ricevere dati dall'hub time series. Per altre informazioni su questo processo, vedere Esercitazione: Configurare un ambiente con Azure Time Series Insights gen2 con [*pagamento in base al prezzo.*](../time-series-insights/tutorial-set-up-environment.md) Seguire questa procedura per creare un ambiente Time Series Insights.

1. Nel [portale di Azure](https://portal.azure.com)cercare Time Series Insights *ambienti* e selezionare il **pulsante** Aggiungi. Scegliere le opzioni seguenti per creare l'ambiente time series.

    * **Sottoscrizione:** scegliere la sottoscrizione.
        - **Gruppo di risorse:** scegliere il gruppo di risorse.
    * **Nome ambiente:** specificare un nome per l'ambiente time series.
    * **Località:** scegliere una località.
    * **Livello:** scegliere il **piano tariffario Gen2(L1).**
    * **Nome proprietà:** immettere **$dtId** (altre informazioni sulla selezione di un valore ID in Procedure consigliate per la scelta di [*un ID serie temporale).*](../time-series-insights/how-to-select-tsid.md)
    * **Nome account di archiviazione:** specificare un nome di account di archiviazione.
    * **Abilita warm store:** lasciare questo campo impostato su *Sì.*

    In questa pagina è possibile lasciare i valori predefiniti per altre proprietà. Selezionare il **pulsante Avanti : Origine >** evento.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Screenshot dell'portale di Azure per creare un Time Series Insights locale. Selezionare la sottoscrizione, il gruppo di risorse e il percorso nei rispettivi elenchi a discesa e scegliere un nome per l'ambiente." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Screenshot dell'portale di Azure per creare un Time Series Insights locale. Il piano tariffario Gen2(L1) è selezionato e il nome della proprietà ID serie temporale è $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Nella scheda *Origine evento* scegliere i campi seguenti:

   * **Create an event source?** (Creare un origine evento?) - Scegliere *Sì.*
   * **Tipo di origine:** scegliere *Hub eventi.*
   * **Nome:** specificare un nome per l'origine evento.
   * **Sottoscrizione:** scegliere la sottoscrizione di Azure.
   * **Spazio dei nomi dell'hub** eventi: scegliere lo spazio dei nomi creato in precedenza in questo articolo.
   * **Nome hub eventi:** scegliere il **nome dell'hub serie** temporale creato in precedenza in questo articolo.
   * **Nome dei criteri di accesso dell'hub** eventi: scegliere la regola *di autenticazione dell'hub serie* temporale creata in precedenza in questo articolo.
   * **Gruppo di consumer dell'hub** eventi: *selezionare Nuovo* e specificare un nome per il gruppo di consumer dell'hub eventi. Selezionare quindi *Aggiungi*.
   * **Nome proprietà:** lasciare vuoto questo campo.
    
    Scegliere il **pulsante Rivedi e** crea per esaminare tutti i dettagli. Selezionare quindi di nuovo **il pulsante Rivedi e crea** per creare l'ambiente della serie temporale.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Screenshot dell'portale di Azure per creare Time Series Insights ambiente. Si sta creando un'origine evento con le informazioni dell'hub eventi indicate in precedenza. Si sta creando anche un nuovo gruppo di consumer." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Inviare dati IoT a Gemelli digitali di Azure

Per iniziare a inviare dati Time Series Insights, è necessario iniziare ad aggiornare le proprietà del gemello digitale in Gemelli digitali di Azure con la modifica dei valori dei dati.

Usare il comando dell'interfaccia della riga di comando seguente per aggiornare la proprietà *Temperature* nel gemello *thermostat67* aggiunto all'istanza nella [sezione](#prerequisites) Prerequisiti.

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Ripetere il comando almeno 4** volte con valori di temperatura diversi per creare diversi punti dati che possono essere osservati in un secondo momento nell'ambiente Time Series Insights dati.

> [!TIP]
> Se si desidera completare questo articolo con dati simulati in tempo reale anziché aggiornare manualmente i [](#prerequisites) valori dei gemelli digitali, assicurarsi prima di tutto di aver completato il suggerimento dalla sezione Prerequisiti per configurare una funzione di Azure che aggiorna i gemelli da un dispositivo simulato.
Successivamente, è possibile eseguire il dispositivo ora per iniziare a inviare dati simulati e aggiornare il gemello digitale tramite tale flusso di dati.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualizzare i dati in Time Series Insights

A questo punto, i dati devono essere Time Series Insights nell'istanza di , pronti per l'analisi. Seguire questa procedura per esplorare i dati in arrivo.

1. Nel [portale di Azure](https://portal.azure.com)cercare il nome dell'ambiente time series creato in precedenza. Nelle opzioni di menu a sinistra selezionare *Panoramica* per visualizzare l strumento di esplorazione di Time Series Insights *URL*. Selezionare l'URL per visualizzare le variazioni di temperatura riflesse nell'Time Series Insights locale.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Screenshot dell'portale di Azure per selezionare l'URL Time Series Insights Explorer nella scheda panoramica dell'ambiente Time Series Insights locale." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. Nella finestra di esplorazione verranno visualizzati i gemelli nell'istanza Gemelli digitali di Azure visualizzata a sinistra. Selezionare il *termostato67* gemello, scegliere la *proprietà Temperature* e fare clic **su Aggiungi.**

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Screenshot dell'Time Series Insights explorer per selezionare thermostat67, selezionare la temperatura della proprietà e premere Aggiungi." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Verranno ora visualizzate le letture iniziali della temperatura dal termostato, come illustrato di seguito. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Screenshot di TSI Explorer per visualizzare i dati della temperatura iniziale. Si tratta di una riga di valori casuali compresi tra 68 e 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Se si consente l'esecuzione di una simulazione per molto più tempo, la visualizzazione avrà un aspetto simile al seguente:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Screenshot dello explorer TSI in cui i dati relativi alla temperatura per ogni gemello vengono grafici in tre righe parallele di colori diversi." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Passaggi successivi

I gemelli digitali vengono archiviati per impostazione predefinita come gerarchia semplice in Time Series Insights, ma possono essere arricchiti con informazioni sul modello e una gerarchia a più livelli per l'organizzazione. Per altre informazioni su questo processo, leggere: 

* [*Esercitazione: Definire e applicare un modello*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

È possibile scrivere logica personalizzata per fornire automaticamente queste informazioni usando i dati del modello e del grafo già archiviati in Gemelli digitali di Azure. Per altre informazioni sulla gestione, l'aggiornamento e il recupero di informazioni dal grafo dei gemelli, vedere i riferimenti seguenti:

* [*Procedura: Gestire un gemello digitale*](./how-to-manage-twin.md)
* [*Procedura: Eseguire query sul grafo gemello*](./how-to-query-graph.md)