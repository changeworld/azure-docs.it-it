---
title: Gestire endpoint e Route (API e CLI)
titleSuffix: Azure Digital Twins
description: Vedere come configurare e gestire gli endpoint e le route di eventi per i dati di Azure Digital gemelli.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d25a429873ccf8b546c0919456c97e64445f184c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071699"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Gestire endpoint e route nei dispositivi gemelli digitali di Azure (API e CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Nei dispositivi gemelli digitali di Azure è possibile instradare le [notifiche degli eventi](how-to-interpret-event-data.md) ai servizi downstream o alle risorse di calcolo connesse. Questa operazione viene eseguita configurando prima di tutto gli **endpoint** che possono ricevere gli eventi. È quindi possibile creare  [**Route di eventi**](concepts-route-events.md) che specificano gli eventi generati dai dispositivi gemelli digitali di Azure che vengono recapitati a quali endpoint.

Questo articolo illustra il processo di creazione di endpoint e route con le [API REST](/rest/api/azure-digitaltwins/), [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)e l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

In alternativa, è anche possibile gestire gli endpoint e le route con la [portale di Azure](https://portal.azure.com). Per una versione di questo articolo che usa invece il portale, vedere [*How-to: Manage Endpoints and routes (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Prerequisiti

- È necessario un **account Azure** (è possibile impostarne uno gratuitamente [qui](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- È necessaria un' **istanza di Azure Digital Twins** nella sottoscrizione di Azure. Se non si dispone già di un'istanza, è possibile crearne una usando la procedura descritta in [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-cli.md). Per usare più avanti in questo articolo, è possibile usare i valori seguenti del programma di installazione:
    - Nome istanza
    - Resource group

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Creare un endpoint per i dispositivi gemelli digitali di Azure

Questi sono i tipi di endpoint supportati che è possibile creare per l'istanza:
* [Griglia di eventi](../event-grid/overview.md)
* [Hub eventi](../event-hubs/event-hubs-about.md)
* [Bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md)

Per altre informazioni sui diversi tipi di endpoint, vedere [*scegliere tra i servizi di messaggistica di Azure*](../event-grid/compare-messaging-services.md).

Questa sezione illustra come creare questi endpoint usando l'interfaccia della riga di comando di Azure. È anche possibile gestire gli endpoint con le [API del piano di controllo DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Creare l'endpoint

Dopo aver creato le risorse dell'endpoint, è possibile usarle per un endpoint di Azure Digital gemelli. Gli esempi seguenti illustrano come creare endpoint usando il comando [AZ DT endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) per l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md). Sostituire i segnaposto nei comandi con i dettagli delle proprie risorse.

Per creare un endpoint di griglia di eventi:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Per creare un endpoint di hub eventi (autenticazione basata su chiavi):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Per creare un endpoint dell'argomento del bus di servizio (autenticazione basata su chiavi):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Dopo aver eseguito questi comandi, l'argomento griglia di eventi, Hub eventi o bus di servizio sarà disponibile come endpoint all'interno dei dispositivi gemelli digitali di Azure, con il nome specificato con l' `--endpoint-name` argomento. Questo nome viene in genere usato come destinazione di una **Route di eventi**, che verrà creata [più avanti in questo articolo](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Creare un endpoint con l'autenticazione basata su identità

È anche possibile creare un endpoint con autenticazione basata su identità, per usare l'endpoint con un' [identità gestita](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Questa opzione è disponibile solo per gli endpoint di tipo hub eventi e bus di servizio (non è supportata per griglia di eventi).

Il comando CLI per creare questo tipo di endpoint è riportato di seguito. Per inserire i segnaposto nel comando sono necessari i valori seguenti:
* ID risorsa di Azure dell'istanza di Azure Digital gemelli
* nome di un endpoint
* tipo di endpoint
* spazio dei nomi della risorsa dell'endpoint
* nome dell'hub eventi o dell'argomento del bus di servizio
* il percorso dell'istanza di Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Creazione di un endpoint con messaggi non recapitabili

Quando un endpoint non è in grado di recapitare un evento entro un determinato periodo di tempo o dopo il tentativo di recapitare l'evento un certo numero di volte, può inviare l'evento non recapitato a un account di archiviazione. Questo processo è noto come **messaggio non recapitabile**.

Gli endpoint con messaggi non recapitabili abilitati possono essere configurati con l' [interfaccia](how-to-use-cli.md) della riga di comando di Azure Digital gemelli o il [piano di controllo](how-to-use-apis-sdks.md#overview-control-plane-apis).

Per ulteriori informazioni sui messaggi non recapitabili, vedere [*concetti: route degli eventi*](concepts-route-events.md#dead-letter-events). Per istruzioni su come configurare un endpoint con messaggi non recapitabili, continuare con la parte restante di questa sezione.

#### <a name="set-up-storage-resources"></a>Configurare le risorse di archiviazione

Prima di impostare il percorso dei messaggi non recapitabili, è necessario avere un [account di archiviazione](../storage/common/storage-account-create.md?tabs=azure-portal) con un [contenitore](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) configurato nell'account Azure. 

Si fornirà l'URI per questo contenitore durante la creazione dell'endpoint in un secondo momento. La posizione dei messaggi non recapitabili verrà fornita all'endpoint come URI del contenitore con un [token](../storage/common/storage-sas-overview.md)di firma di accesso condiviso. Il token necessita `write` dell'autorizzazione per il contenitore di destinazione nell'account di archiviazione. Il formato dell'URI di firma di accesso condiviso con **lettera non recapitabile** completo sarà: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` .

Attenersi alla procedura seguente per configurare queste risorse di archiviazione nell'account di Azure, per preparare la configurazione della connessione dell'endpoint nella sezione successiva.

1. Seguire i passaggi descritti in [*creare un account di archiviazione*](../storage/common/storage-account-create.md?tabs=azure-portal) per creare un **account di archiviazione** nella sottoscrizione di Azure. Prendere nota del nome dell'account di archiviazione per usarlo in un secondo momento.
2. Seguire i passaggi descritti in [*creare un contenitore*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) per creare un **contenitore** all'interno del nuovo account di archiviazione. Prendere nota del nome del contenitore per usarlo in un secondo momento.
3. Successivamente, creare un **token** di firma di accesso condiviso per l'account di archiviazione che l'endpoint può usare per accedervi. Per iniziare, passare all'account di archiviazione nel [portale di Azure](https://ms.portal.azure.com/#home) (è possibile trovarlo in base al nome con la barra di ricerca del portale).
4. Nella pagina account di archiviazione scegliere il collegamento _firma di accesso condiviso_ nella barra di spostamento a sinistra per avviare la configurazione del token SAS.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Pagina account di archiviazione nell'portale di Azure" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Nella *pagina firma di accesso condiviso*, in *Servizi consentiti* e *tipi di risorse consentiti*, selezionare tutte le impostazioni desiderate. È necessario selezionare almeno una casella in ogni categoria. In *autorizzazioni consentite* scegliere **scrittura** (è anche possibile selezionare altre autorizzazioni se lo si desidera).
1. Impostare tutti i valori desiderati per le impostazioni rimanenti.
1. Al termine, selezionare il pulsante _genera SAS e stringa di connessione_ per generare il token SAS. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Pagina account di archiviazione nell'portale di Azure che Mostra tutte le impostazioni selezionate per generare un token di firma di accesso condiviso ed evidenziando il pulsante &quot;genera SAS e stringa di connessione&quot;" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Questa operazione genererà diversi valori della stringa di connessione e di firma di accesso condiviso nella parte inferiore della stessa pagina, sotto le selezioni delle impostazioni. Scorrere verso il basso per visualizzare i valori e usare l'icona *copia negli Appunti* per copiare il valore del **token SAS** . Salvarlo per usarlo in seguito.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Copiare il token SAS da usare nel segreto dei messaggi non recapitabili." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>Creare l'endpoint dei messaggi non recapitabili

Per creare un endpoint con messaggi non recapitabili abilitati, aggiungere il seguente parametro del messaggio non recapitabile al comando [AZ DT endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) per l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Il valore del parametro è l'URI di firma di accesso condiviso dei messaggi non **recapitabili** costituito dal nome dell'account di archiviazione, dal nome del contenitore e dal token SAS raccolti nella [sezione precedente](#set-up-storage-resources). Questo parametro crea l'endpoint con l'autenticazione basata su chiavi.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Aggiungere questo parametro alla fine dei comandi di creazione dell'endpoint dalla sezione [*creare l'endpoint*](#create-the-endpoint) in precedenza per creare un endpoint del tipo desiderato in cui è abilitato il messaggio non recapitabile.

In alternativa, è possibile creare endpoint di messaggi non recapitabili usando le [API del piano di controllo dei gemelli digitali di Azure](how-to-use-apis-sdks.md#overview-control-plane-apis) anziché l'interfaccia della riga di comando A tale scopo, vedere la [documentazione di DigitalTwinsEndpoint](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) per informazioni su come strutturare la richiesta e aggiungere i parametri dei messaggi non recapitabili.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Creare un endpoint dei messaggi non recapitabili con l'autenticazione basata su identità

È anche possibile creare un endpoint di messaggi non recapitabili con autenticazione basata su identità, per usare l'endpoint con un' [identità gestita](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Questa opzione è disponibile solo per gli endpoint di tipo hub eventi e bus di servizio (non è supportata per griglia di eventi).

Per creare questo tipo di endpoint, usare lo stesso comando CLI precedente per [creare un endpoint con l'autenticazione basata sull'identità](#create-an-endpoint-with-identity-based-authentication), con un campo aggiuntivo nel payload JSON per un `deadLetterUri` .

Di seguito sono riportati i valori necessari per inserire i segnaposto nel comando:
* ID risorsa di Azure dell'istanza di Azure Digital gemelli
* nome di un endpoint
* tipo di endpoint
* spazio dei nomi della risorsa dell'endpoint
* nome dell'hub eventi o dell'argomento del bus di servizio
* Dettagli URI SAS dei messaggi non **recapitabili** : nome dell'account di archiviazione, nome del contenitore
* il percorso dell'istanza di Azure Digital Twins

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Schema di archiviazione messaggi

Una volta configurato l'endpoint con i messaggi non recapitabili, i messaggi non recapitabili verranno archiviati nel formato seguente nell'account di archiviazione:

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

I messaggi non recapitabili corrisponderanno allo schema dell'evento originale destinato a essere recapitato all'endpoint originale.

Di seguito è riportato un esempio di messaggio non recapitabile per una notifica di creazione di un dispositivo [gemello](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Creare una route di eventi

**Prerequisito**: è necessario creare endpoint come descritto in precedenza in questo articolo prima di poter passare alla creazione di una route. È possibile procedere alla creazione di una route di eventi al termine della configurazione degli endpoint.

> [!NOTE]
> Se gli endpoint sono stati distribuiti di recente, verificare che la distribuzione sia terminata **prima** di provare a usarli per una nuova route di eventi. Se la distribuzione della route non riesce perché gli endpoint non sono pronti, attendere alcuni minuti e riprovare.
>
> Se si crea uno script per questo flusso, è possibile che si voglia tenere conto di questa situazione creando in 2-3 minuti di tempo di attesa per il completamento della distribuzione del servizio endpoint prima di passare alla configurazione del routing.

Per inviare effettivamente i dati dai dispositivi gemelli digitali di Azure a un endpoint, è necessario definire una **route dell'evento**. Le route degli eventi vengono usate per connettere il flusso eventi, in tutto il sistema e ai servizi downstream.

Una definizione di route può contenere gli elementi seguenti:
* Nome della route che si vuole usare
* Nome dell'endpoint che si desidera utilizzare
* Filtro che definisce gli eventi che vengono inviati all'endpoint 

Se non è presente alcun nome di route, nessun messaggio viene instradato all'esterno dei dispositivi gemelli digitali di Azure. Se è presente un nome di route e il filtro è `true` , tutti i messaggi vengono instradati all'endpoint. Se è presente un nome di route e viene aggiunto un filtro diverso, i messaggi verranno filtrati in base al filtro.

Una route deve consentire la selezione di più notifiche e tipi di evento. 

È possibile creare route di eventi con le API del [piano dati **EventRoutes**](/rest/api/digital-twins/dataplane/eventroutes) di Azure Digital gemelli o i [comandi **AZ DT Route** CLI](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true). Il resto di questa sezione illustra il processo di creazione.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Creare route con le API e C# SDK

Un modo per definire le route degli eventi è con le [API del piano dati](how-to-use-apis-sdks.md#overview-data-plane-apis). Gli esempi in questa sezione usano [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

`CreateOrReplaceEventRouteAsync` è la chiamata SDK utilizzata per aggiungere una route dell'evento. Di seguito è riportato un esempio di utilizzo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

#### <a name="event-route-sample-sdk-code"></a>Codice SDK di esempio della route di eventi

Il metodo di esempio seguente mostra come creare, elencare ed eliminare una route di eventi con C# SDK:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Creare route con l'interfaccia della riga di comando

Le route possono essere gestite anche usando i comandi [AZ DT Route](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true) per l'interfaccia della riga di comando di Azure Digital gemelli. 

Per altre informazioni sull'uso dell'interfaccia della riga di comando e sui comandi disponibili, vedere [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](how-to-use-cli.md).

## <a name="filter-events"></a>Filtrare gli eventi

Senza filtro, gli endpoint ricevono una serie di eventi da dispositivi gemelli digitali di Azure:
* Telemetria generata dai dispositivi [gemelli digitali](concepts-twins-graph.md) con l'API del servizio di dispositivi digitali gemelli di Azure
* Notifiche delle modifiche delle proprietà dei dispositivi gemelli, attivate per le modifiche delle proprietà per qualsiasi dispositivo gemello nell'istanza di Azure
* Eventi del ciclo di vita, generati quando vengono creati o eliminati gemelli o relazioni

È possibile limitare gli eventi inviati aggiungendo un **filtro** per un endpoint alla route dell'evento.

Per aggiungere un filtro, è possibile usare una richiesta PUT su *https://{Your-Azure-Digital-gemells-hostname}/eventRoutes/{Event-route-name}? API-Version = 2020-10-31* con il corpo seguente:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Ecco i filtri di route supportati. Usare i dettagli nella colonna *Filtra schema testo* per sostituire il `<filter-text>` segnaposto nel corpo della richiesta precedente.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui diversi tipi di messaggi di evento che è possibile ricevere, vedere:
* [*Procedura: interpretare i dati degli eventi*](how-to-interpret-event-data.md)
