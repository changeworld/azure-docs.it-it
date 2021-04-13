---
title: Integrazione con Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Vedere How to set up Event routes from Azure Digital Gemells to Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 256bb80687ffedba9718303710335cce1a582c1d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304006"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrare i dispositivi gemelli digitali di Azure con Azure Time Series Insights

In questo articolo si apprenderà come integrare i dispositivi gemelli digitali di Azure con [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

La soluzione descritta in questo articolo consente di raccogliere e analizzare i dati cronologici sulla soluzione Internet delle cose. Il servizio Gemelli digitali di Azure è una scelta ideale per l'inserimento di dati in Time Series Insights, perché consente di correlare più flussi di dati e di standardizzare le informazioni prima di inviarle a Time Series Insights.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter configurare una relazione con Time Series Insights, è necessario configurare le risorse seguenti:
* Un **Hub** Internet. Per istruzioni, vedere la sezione [*creare un hub*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) per gli Internet nella Guida introduttiva all'invio di dati di *telemetria dell'hub* Internet.
* Un' **istanza di dispositivi gemelli digitali di Azure**.
Per istruzioni, vedere [*procedura: configurare un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione*](./how-to-set-up-instance-portal.md).
* Un **modello e un gemello nell'istanza di Azure Digital gemelli**.
È necessario aggiornare le informazioni dei dispositivi gemelli alcune volte per vedere che i dati sono stati rilevati in Time Series Insights. Per istruzioni, vedere la sezione [*aggiungere un modello e un dispositivo gemello*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) nell'articolo *procedura:* inserire l'hub.

> [!TIP]
> In questo articolo, la modifica dei valori gemelli digitali visualizzati in Time Series Insights viene aggiornata manualmente per semplicità. Tuttavia, se si vuole completare questo articolo con i dati simulati in tempo reale, è possibile configurare una funzione di Azure che aggiorna i dispositivi gemelli digitali in base agli eventi di telemetria degli stessi da un dispositivo simulato. Per istruzioni, vedere [*procedura:*](how-to-ingest-iot-hub-data.md)inserire i dati dell'hub Internet, inclusi i passaggi finali per eseguire il simulatore di dispositivi e verificare il funzionamento del flusso di dati.
>
> In seguito, cercare un altro suggerimento per mostrare dove iniziare a eseguire il simulatore di dispositivi e fare in modo che le funzioni di Azure aggiornino automaticamente i gemelli, invece di inviare comandi di aggiornamento manuale dei dispositivi gemelli.


## <a name="solution-architecture"></a>Architettura della soluzione

Si collegherà Time Series Insights ai dispositivi gemelli digitali di Azure tramite il percorso riportato di seguito.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagramma dei servizi di Azure in uno scenario end-to-end, evidenziando Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Creare lo spazio dei nomi dell'hub eventi

Prima di creare gli hub eventi, creare prima di tutto uno spazio dei nomi dell'hub eventi che riceverà gli eventi dall'istanza di Azure Digital gemelli. È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure usare la portale di Azure: [*Guida introduttiva: creare un hub eventi usando portale di Azure*](../event-hubs/event-hubs-create.md). Per visualizzare le aree che supportano gli hub eventi, visitare i [*prodotti Azure disponibili in base all'area*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Se viene visualizzato un errore che informa `BadRequest: The specified service namespace is invalid.` , assicurarsi che il nome scelto per lo spazio dei nomi soddisfi i requisiti di denominazione descritti in questo documento di riferimento: [create namespace](/rest/api/servicebus/create-namespace).

Si userà questo spazio dei nomi di hub eventi per contenere i due hub eventi necessari per questo articolo:

  1. **Hub gemelli** -Hub eventi per ricevere eventi di modifica di dispositivi gemelli
  2. **Hub della serie temporale** -Hub eventi per trasmettere eventi a Time Series Insights

Le sezioni successive illustrano la creazione e la configurazione di questi Hub nello spazio dei nomi dell'hub eventi.

## <a name="create-twins-hub"></a>Crea Hub gemelli

Il primo hub eventi che verrà creato in questo articolo è l' **Hub gemelli**. Questo hub eventi riceverà gli eventi di modifica dei dispositivi gemelli dai dispositivi gemelli digitali di Azure.
Per configurare l'hub dei dispositivi gemelli, completare la procedura seguente in questa sezione:

1. Creare l'hub dei dispositivi gemelli
2. Creare una regola di autorizzazione per controllare le autorizzazioni per l'hub
3. Creare un endpoint nei dispositivi gemelli digitali di Azure che usa la regola di autorizzazione per accedere all'hub
4. Creare una route nei dispositivi gemelli digitali di Azure che invia un evento di aggiornamento dei dispositivi gemelli all'endpoint e all'hub gemelli connessi
5. Ottenere la stringa di connessione dell'hub gemelli

Creare l' **Hub gemelli** con il comando dell'interfaccia della riga di comando seguente. Specificare un nome per l'hub gemelli.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Crea regola di autorizzazione dell'hub gemelli

Creare una [regola di autorizzazione](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) con le autorizzazioni di invio e ricezione. Specificare un nome per la regola.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Crea endpoint dell'hub gemelli

Creare un [endpoint](concepts-route-events.md#create-an-endpoint) dei dispositivi gemelli digitali di Azure che collega l'hub eventi all'istanza di Azure Digital gemelli. Specificare un nome per l'endpoint dell'hub gemelli.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Crea route eventi Hub gemelli

Le istanze dei dispositivi gemelli digitali di Azure possono generare [eventi di aggiornamento gemelli](how-to-interpret-event-data.md) ogni volta che viene aggiornato lo stato di un dispositivo In questa sezione verrà creata una **Route di eventi** di Azure Digital gemelli che indirizza gli eventi di aggiornamento all'hub dei dispositivi gemelli per un'ulteriore elaborazione.

Creare una [Route](concepts-route-events.md#create-an-event-route) nei dispositivi gemelli digitali di Azure per inviare eventi di aggiornamento gemelli all'endpoint precedente. Il filtro in questa route consente di passare solo i messaggi di aggiornamento gemelli all'endpoint. Specificare un nome per la route degli eventi dell'hub gemelli.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub gemelli

Ottenere la [stringa di connessione dell'hub eventi gemelli](../event-hubs/event-hubs-get-connection-string.md), usando le regole di autorizzazione create in precedenza per l'hub gemelli.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Prendere nota del valore **primaryConnectionString** dal risultato per configurare l'impostazione dell'app per hub gemelli più avanti in questo articolo.

## <a name="create-time-series-hub"></a>Crea Hub Time Series

Il secondo hub eventi che verrà creato in questo articolo è l' **Hub Time Series**. Si tratta di un hub eventi che consente di trasmettere gli eventi dei dispositivi gemelli digitali di Azure a Time Series Insights.
Per configurare l'hub Time Series, è necessario completare i passaggi seguenti:

1. Creare l'hub Time Series
2. Creare una regola di autorizzazione per controllare le autorizzazioni per l'hub
3. Ottenere la stringa di connessione dell'hub Time Series

Successivamente, quando si crea l'istanza di Time Series Insights, l'hub Time Series verrà connesso come origine evento per l'istanza di Time Series Insights.

Creare l' **Hub Time Series** usando il comando seguente. Specificare un nome per l'hub Time Series.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Crea regola di autorizzazione dell'hub Time Series

Creare una [regola di autorizzazione](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) con le autorizzazioni di invio e ricezione. Specificare un nome per la regola di autenticazione dell'hub Time Series.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Ottenere la stringa di connessione dell'hub Time Series

Ottenere la [stringa di connessione dell'hub Time Series](../event-hubs/event-hubs-get-connection-string.md), usando le regole di autorizzazione create in precedenza per l'hub Time Series:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Prendere nota del valore **primaryConnectionString** dal risultato per configurare l'impostazione dell'app hub Time Series più avanti in questo articolo.

Prendere nota anche dei valori seguenti per usarli in un secondo momento per creare un'istanza di Time Series Insights.
* Spazio dei nomi dell'hub eventi
* Nome hub serie temporale
* Regola di autenticazione dell'hub Time Series

## <a name="create-a-function"></a>Creare una funzione

In questa sezione verrà creata una funzione di Azure che convertirà gli eventi di aggiornamento dei dispositivi gemelli dal formato originale come documenti patch JSON in oggetti JSON, che contengono solo valori aggiornati e aggiunti dai dispositivi gemelli.

### <a name="step-1-create-function-app"></a>Passaggio 1: creare l'app per le funzioni

Per prima cosa, creare un nuovo progetto di app per le funzioni in Visual Studio. Per istruzioni su come eseguire questa operazione, vedere la sezione [**creare un'app per le funzioni in Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) dell'articolo *procedura: configurare una funzione per l'elaborazione dei dati* .

### <a name="step-2-add-a-new-function"></a>Passaggio 2: aggiungere una nuova funzione

Creare una nuova funzione di Azure denominata *ProcessDTUpdatetoTSI. cs* per aggiornare gli eventi di telemetria del dispositivo alla Time Series Insights. Il tipo di funzione sarà il **trigger di hub eventi**.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Screenshot di Visual Studio per creare una nuova funzione di Azure di tipo trigger dell'hub eventi.":::

### <a name="step-3-fill-in-function-code"></a>Passaggio 3: compilare il codice della funzione

Aggiungere i pacchetti seguenti al progetto:
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Sostituire il codice nel file *ProcessDTUpdatetoTSI. cs* con il codice seguente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Salvare il codice della funzione.

### <a name="step-4-publish-the-function-app-to-azure"></a>Passaggio 4: pubblicare l'app per le funzioni in Azure

Pubblicare il progetto con la funzione *ProcessDTUpdatetoTSI. cs* in un'app per le funzioni in Azure.

Per istruzioni su come eseguire questa operazione, vedere la sezione [**pubblicare l'app per le funzioni in Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) dell'articolo *procedura: configurare una funzione per l'elaborazione dei dati* .

Salvare il nome dell'app per le funzioni da usare in un secondo momento per configurare le impostazioni dell'app per i due hub eventi.

### <a name="step-5-security-access-for-the-function-app"></a>Passaggio 5: accesso di sicurezza per l'app per le funzioni

Assegnare quindi **un ruolo di accesso** per la funzione e **configurare le impostazioni dell'applicazione** in modo che possa accedere all'istanza di Azure Digital gemelli. Per istruzioni su come eseguire questa operazione, vedere la sezione [**configurare l'accesso di sicurezza per l'app per le funzioni**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) dell'articolo *procedura: configurare una funzione per l'elaborazione dei dati* .

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Passaggio 6: configurare le impostazioni dell'app per i due hub eventi

Successivamente, verranno aggiunte le variabili di ambiente nelle impostazioni dell'app per le funzioni che consentono di accedere all'hub dei dispositivi gemelli e all'hub della serie temporale.

Usare il valore **primaryConnectionString** dell'hub gemelli salvato in precedenza per creare un'impostazione dell'app nell'app per le funzioni che contiene la stringa di connessione dell'hub gemello:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Usare il valore **primaryConnectionString** dell'hub Time Series salvato in precedenza per creare un'impostazione dell'app nell'app per le funzioni che contiene la stringa di connessione dell'hub Time Series:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Creare e connettere un'istanza di Time Series Insights

In questa sezione si imposterà Time Series Insights istanza per ricevere i dati dall'hub Time Series. Per ulteriori informazioni su questo processo, vedere [*esercitazione: configurazione di un ambiente Azure Time Series Insights Gen2 PAYG*](../time-series-insights/tutorial-set-up-environment.md). Seguire questa procedura per creare un ambiente Time Series Insights.

1. Nella [portale di Azure](https://portal.azure.com)cercare *Time Series Insights ambienti* e selezionare il pulsante **Aggiungi** . Scegliere le opzioni seguenti per creare l'ambiente della serie temporale.

    * **Sottoscrizione** : scegliere la sottoscrizione.
        - **Gruppo di risorse** : scegliere il gruppo di risorse.
    * **Nome ambiente** : specificare un nome per l'ambiente della serie temporale.
    * **Località** : scegliere una località.
    * **Livello** : scegliere il piano tariffario **Gen2 (L1)** .
    * **Nome proprietà** : immettere **$dtId** (per altre informazioni sulla selezione di un valore ID, vedere [*procedure consigliate per la scelta di un ID di serie temporale*](../time-series-insights/how-to-select-tsid.md)).
    * **Nome dell'account di archiviazione** : specificare un nome per l'account di archiviazione.
    * **Abilita warm Store** : lasciare questo campo impostato su *Sì*.

    È possibile lasciare i valori predefiniti per le altre proprietà in questa pagina. Selezionare il pulsante **Next: origine evento >** .

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Screenshot della portale di Azure per creare Time Series Insights ambiente. Selezionare la sottoscrizione, il gruppo di risorse e la località dai rispettivi elenchi a discesa e scegliere un nome per l'ambiente." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Screenshot della portale di Azure per creare Time Series Insights ambiente. Il piano tariffario Gen2 (L1) è selezionato e il nome della proprietà ID della serie temporale è $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. Nella scheda *origine evento* scegliere i campi seguenti:

   * **Create an event source?** (Creare un origine evento?) -Scegliere *Sì*.
   * **Tipo di origine** : scegliere *Hub eventi*.
   * **Nome** : specificare un nome per l'origine evento.
   * **Sottoscrizione** : scegliere la sottoscrizione di Azure.
   * **Spazio dei nomi dell'hub eventi** : scegliere lo spazio dei nomi creato in precedenza in questo articolo.
   * **Nome dell'hub eventi** : scegliere il nome dell' **hub della serie temporale** creato in precedenza in questo articolo.
   * **Nome dei criteri di accesso dell'hub eventi** : scegliere la *regola di autenticazione dell'hub della serie temporale* creata in precedenza in questo articolo.
   * **Gruppo di consumer dell'hub eventi** : selezionare *nuovo* e specificare un nome per il gruppo di consumer dell'hub eventi. Quindi selezionare *Aggiungi*.
   * **Nome proprietà** : lasciare vuoto questo campo.
    
    Scegliere il pulsante **Verifica + crea** per esaminare tutti i dettagli. Quindi, selezionare di nuovo il pulsante **Verifica e crea** per creare l'ambiente della serie temporale.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Screenshot della portale di Azure per creare Time Series Insights ambiente. Si sta creando un'origine evento con le informazioni sull'hub eventi riportate sopra. Si sta creando anche un nuovo gruppo di consumer." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Inviare i dati dell'it ai dispositivi gemelli digitali di Azure

Per iniziare a inviare dati a Time Series Insights, è necessario avviare l'aggiornamento delle proprietà dei dispositivi gemelli digitali nei dispositivi gemelli digitali di Azure con valori di dati modificabili.

Usare il comando dell'interfaccia della riga di comando seguente per aggiornare la proprietà *temperature* nel dispositivo gemello *thermostat67* aggiunto all'istanza nella sezione [prerequisiti](#prerequisites) .

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Ripetere il comando almeno quattro volte con valori di temperatura diversi**, per creare più punti dati che possono essere osservati successivamente nell'ambiente Time Series Insights.

> [!TIP]
> Per completare questo articolo con dati simulati in tempo reale anziché aggiornare manualmente i valori dei dispositivi gemelli digitali, assicurarsi di aver completato il suggerimento dalla sezione [*prerequisiti*](#prerequisites) per impostare una funzione di Azure che aggiorna i dispositivi gemelli da un dispositivo simulato.
Successivamente, è possibile eseguire il dispositivo ora per iniziare a inviare dati simulati e aggiornare il dispositivo gemello digitale tramite il flusso di dati.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualizzare i dati in Time Series Insights

A questo punto, i dati devono essere propagati nell'istanza di Time Series Insights, pronti per essere analizzati. Per esplorare i dati in arrivo, attenersi alla procedura riportata di seguito.

1. Nella [portale di Azure](https://portal.azure.com)cercare il nome dell'ambiente della serie temporale creato in precedenza. Nelle opzioni di menu a sinistra selezionare *Panoramica* per visualizzare l'URL di *Time Series Insights Explorer*. Selezionare l'URL per visualizzare le variazioni di temperatura riflesse nell'ambiente Time Series Insights.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Screenshot della portale di Azure per selezionare l'URL di Time Series Insights Explorer nella scheda Panoramica dell'ambiente Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. Nella finestra di esplorazione si vedranno i dispositivi gemelli nell'istanza di Azure Digital Twins visualizzata a sinistra. Selezionare il *thermostat67* gemello, scegliere la *temperatura* della proprietà e quindi fare clic su **Aggiungi**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Screenshot di Esplora Time Series Insights per selezionare thermostat67, selezionare la temperatura della proprietà e quindi fare clic su Aggiungi." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Verranno ora visualizzate le letture della temperatura iniziali dal termostato, come mostrato di seguito. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Screenshot di TSI Explorer per visualizzare i dati di temperatura iniziali. Si tratta di una riga di valori casuali compresa tra 68 e 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Se si consente l'esecuzione di una simulazione per molto più tempo, la visualizzazione sarà simile alla seguente:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Screenshot di TSI Explorer in cui i dati di temperatura per ogni dispositivo gemello sono rappresentati in tre linee parallele di colori diversi." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Passaggi successivi

I dispositivi gemelli digitali vengono archiviati per impostazione predefinita come gerarchia piatta in Time Series Insights, ma possono essere arricchiti con le informazioni sul modello e una gerarchia a più livelli per l'organizzazione. Per ulteriori informazioni su questo processo, vedere: 

* [*Esercitazione: definire e applicare un modello*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

È possibile scrivere logica personalizzata per fornire automaticamente queste informazioni usando i dati del modello e del grafo già archiviati nei dispositivi gemelli digitali di Azure. Per altre informazioni sulla gestione, l'aggiornamento e il recupero delle informazioni dal grafico gemello, vedere i riferimenti seguenti:

* [*Procedura: gestire un dispositivo gemello digitale*](./how-to-manage-twin.md)
* [*Procedura: eseguire una query sul grafico gemello*](./how-to-query-graph.md)