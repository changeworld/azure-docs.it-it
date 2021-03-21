---
title: Integrazione con Servizio Azure SignalR
titleSuffix: Azure Digital Twins
description: Vedere come trasmettere dati di telemetria di Azure Digital gemelli ai client usando Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 89bd77c30ec52a72087598b86f22e85659fa1b0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203896"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrare i dispositivi gemelli digitali di Azure con il servizio Azure SignalR

In questo articolo si apprenderà come integrare i dispositivi gemelli digitali di Azure con il [servizio Azure SignalR](../azure-signalr/signalr-overview.md).

La soluzione descritta in questo articolo consente di effettuare il push dei dati di telemetria del gemello digitale ai client connessi, ad esempio una singola pagina Web o un'applicazione per dispositivi mobili. Di conseguenza, i client vengono aggiornati con le metriche e lo stato in tempo reale dei dispositivi Internet, senza dover eseguire il polling del server o inviare nuove richieste HTTP per gli aggiornamenti.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono riportati i prerequisiti che è necessario completare prima di procedere:

* Prima di integrare la soluzione con il servizio Azure SignalR in questo articolo, è necessario completare l'esercitazione sui dispositivi gemelli di Azure [_**: connettere una soluzione end-to-end**_](tutorial-end-to-end.md), perché questo articolo sulle procedure si basa su di esso. Questa esercitazione illustra la configurazione di un'istanza di Azure Digital gemelli che funziona con un dispositivo di Internet delle cose virtuali per attivare gli aggiornamenti dei dispositivi gemelli digitali. Questo articolo illustra come connettere gli aggiornamenti a un'app Web di esempio tramite il servizio Azure SignalR.

* Nell'esercitazione sono necessari i valori seguenti:
  - Argomento di griglia di eventi
  - Resource group
  - Nome del servizio app/app per le funzioni
    
* È necessario [**Node.js**](https://nodejs.org/) installato nel computer.

È anche possibile accedere al [portale di Azure](https://portal.azure.com/) con l'account Azure.

## <a name="solution-architecture"></a>Architettura della soluzione

Il servizio Azure SignalR verrà collegato ai dispositivi gemelli digitali di Azure tramite il percorso riportato di seguito. Le sezioni A, B e C nel diagramma sono tratte dal diagramma dell'architettura del [prerequisito dell'esercitazione end-to-end](tutorial-end-to-end.md). In questo articolo sulle procedure verrà compilata la sezione D nell'architettura esistente.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Visualizzazione dei servizi di Azure in uno scenario end-to-end. Viene illustrato il flusso di dati da un dispositivo all'hub Internet, tramite una funzione di Azure (freccia B) a un'istanza di Azure Digital gemelli (sezione A), quindi tramite griglia di eventi a un'altra funzione di Azure per l'elaborazione (freccia C). La sezione D Mostra il flusso di dati dalla stessa griglia di eventi nella freccia C a una funzione di Azure con etichetta &quot;broadcast&quot;. ' Broadcast ' comunica con un'altra funzione di Azure con etichetta ' Negotiate ' è Broadcast ' è Negotiate ' comunicano con i dispositivi computer." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Scaricare le applicazioni di esempio

Prima di tutto, scaricare le app di esempio richieste. Sono necessari entrambi gli elementi seguenti:
* [**Esempi end-to-end di Azure Digital gemelli**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): questo esempio contiene una *AdtSampleApp* che contiene due funzioni di Azure per lo spostamento dei dati in un'istanza di dispositivi gemelli digitali di Azure. per altre informazioni su questo scenario, vedere [*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md). Contiene anche un'applicazione di esempio *DeviceSimulator* che simula un dispositivo molto, generando un nuovo valore di temperatura ogni secondo.
    - Se l'esempio non è ancora stato scaricato come parte dell'esercitazione in [*prerequisiti*](#prerequisites), passare al [collegamento](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) di esempio e selezionare il pulsante *Sfoglia codice* sotto il titolo. In questo modo si passerà al repository GitHub per gli esempi, che è possibile scaricare come *. ZIP* selezionando il pulsante *codice* e *Scarica zip*.

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Visualizzazione del repository Digital-Twins-Samples in GitHub. Il pulsante codice è selezionato, producendo una piccola finestra di dialogo in cui è evidenziato il pulsante Scarica ZIP." lightbox="media/includes/download-repo-zip.png":::

    Verrà scaricata una copia del repository di esempio nel computer in uso, come **digital-twins-samples-master.zip**. Decomprimere la cartella.
* [**Esempio di app Web di integrazione SignalR**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): si tratta di un'app Web React di esempio che utilizzerà i dati di telemetria di Azure Digital Twins da un servizio Azure SignalR.
    -  Passare al collegamento di esempio e usare lo stesso processo di download per scaricare una copia dell'esempio nel computer, come _**digitaltwins-signalr-webapp-sample-main.zip**_. Decomprimere la cartella.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Lasciare aperta la finestra del browser alla portale di Azure, che verrà usata di nuovo nella sezione successiva.

## <a name="publish-and-configure-the-azure-functions-app"></a>Pubblicare e configurare l'app funzioni di Azure

In questa sezione vengono impostate due funzioni di Azure:
* **Negotiate** : funzione trigger http. Usa l'associazione di input *SignalRConnectionInfo* per generare e restituire informazioni di connessione valide.
* **broadcast** : funzione trigger di [griglia di eventi](../event-grid/overview.md) . Riceve i dati di telemetria di Azure Digital gemelli attraverso la griglia di eventi e usa l'associazione di output dell'istanza di *SignalR* creata nel passaggio precedente per trasmettere il messaggio a tutte le applicazioni client connesse.

Avviare Visual Studio (o un altro editor di codice di propria scelta) e aprire la soluzione di codice nella cartella *Digital-Twins-Samples-master > ADTSampleApp* . Eseguire quindi la procedura seguente per creare le funzioni:

1. Nel progetto *SampleFunctionsApp* creare una nuova classe C# denominata **SignalRFunctions. cs**.

1. Sostituire il contenuto del file di classe con il codice seguente:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Nella finestra console di *Gestione pacchetti* di Visual Studio o in qualsiasi finestra di comando del computer, passare alla cartella *Digital-Twins-Samples-master\AdtSampleApp\SampleFunctionsApp* ed eseguire il comando seguente per installare il `SignalRService` pacchetto NuGet nel progetto:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Questa operazione dovrebbe risolvere eventuali problemi di dipendenza nella classe.

1. Pubblicare la funzione in Azure, seguendo la procedura descritta nella sezione [ *pubblicare l'app*](tutorial-end-to-end.md#publish-the-app) dell'esercitazione relativa alla *soluzione di connessione end-to-end* . È possibile pubblicarla nella stessa app del servizio App/funzione utilizzata nel [prerequisito](#prerequisites)dell'esercitazione end-to-end o crearne una nuova, ma è possibile usare la stessa per ridurre al minimo la duplicazione. 

Configurare quindi le funzioni per la comunicazione con l'istanza di Azure SignalR. Si inizierà raccogliendo la **stringa di connessione** dell'istanza di SignalR, che verrà quindi aggiunta alle impostazioni dell'app per le funzioni.

1. Passare alla [portale di Azure](https://portal.azure.com/) e cercare il nome dell'istanza di SignalR nella barra di ricerca nella parte superiore del portale. Selezionare l'istanza per aprirla.
1. Selezionare **chiavi** dal menu istanza per visualizzare le stringhe di connessione per l'istanza del servizio SignalR.
1. Selezionare l'icona *copia* per copiare la stringa di connessione primaria.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Screenshot del portale di Azure che mostra la pagina chiavi per l'istanza di SignalR. L'icona ' copia negli Appunti ' accanto alla stringa di connessione primaria è evidenziata." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Infine, aggiungere la **stringa di connessione** di Azure SignalR alle impostazioni dell'app della funzione usando il comando dell'interfaccia della riga di comando di Azure seguente. Inoltre, sostituire i segnaposto con il gruppo di risorse e il nome del servizio app/app per le funzioni dal [prerequisito dell'esercitazione](how-to-integrate-azure-signalr.md#prerequisites). Il comando può essere eseguito in [Azure cloud Shell](https://shell.azure.com)o localmente se nel [computer è installata](/cli/azure/install-azure-cli)l'interfaccia della riga di comando di Azure:
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    L'output di questo comando Visualizza tutte le impostazioni dell'app configurate per la funzione di Azure. Cercare `AzureSignalRConnectionString` nella parte inferiore dell'elenco per verificare che sia stato aggiunto.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Estratto dell'output in una finestra di comando, che mostra una voce di elenco denominata ' AzureSignalRConnectionString '":::

#### <a name="connect-the-function-to-event-grid"></a>Connettere la funzione a Griglia di eventi

Quindi, sottoscrivere la funzione *broadcast* di Azure all' **argomento di griglia di eventi** creato durante il [prerequisito dell'esercitazione](how-to-integrate-azure-signalr.md#prerequisites). Questo consentirà il flusso dei dati di telemetria dal thermostat67 gemello attraverso l'argomento di griglia di eventi e alla funzione. Da qui la funzione può trasmettere i dati a tutti i client.

A tale scopo, creare una sottoscrizione di **eventi** dall'argomento della griglia di eventi per la funzione *broadcast* di Azure come endpoint.

Nel [portale di Azure](https://portal.azure.com/) passare all'argomento di Griglia di eventi cercandone il nome nella barra di ricerca in alto. Selezionare *+ Sottoscrizione di eventi*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Portale di Azure: Sottoscrizione di eventi di Griglia di eventi":::

Nella pagina *Crea sottoscrizione di eventi* compilare i campi come indicato di seguito (i campi compilati per impostazione predefinita non sono indicati):
* *DETTAGLI SOTTOSCRIZIONE EVENTO* > **Nome**: assegnare un nome alla sottoscrizione di eventi.
* *DETTAGLI ENDPOINT* > **Tipo di endpoint** : selezionare *Funzione di Azure* nelle opzioni del menu.
* *DETTAGLI ENDPOINT* > **Endpoint**: scegliere il collegamento *Seleziona un endpoint*. Verrà aperta la finestra *Seleziona funzione di Azure*:
    - Compilare la **sottoscrizione**, il **gruppo di risorse**, l'app per le **funzioni** e la **funzione** (*broadcast*). È possibile che alcuni di questi campi vengano prepopolati dopo aver selezionato la sottoscrizione.
    - Premere **Confermare la selezione**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Portale di Azure la visualizzazione della creazione di una sottoscrizione di eventi. I campi precedenti sono compilati e i pulsanti ' conferma selezione ' è Crea ' sono evidenziati.":::

Di nuovo nella pagina *Crea sottoscrizione di eventi* premere **Crea**.

A questo punto dovrebbero essere visualizzate due sottoscrizioni di eventi nella pagina di *argomento di griglia di eventi* .

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Portale di Azure visualizzazione di due sottoscrizioni di eventi nella pagina dell'argomento griglia di eventi." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Configurare ed eseguire l'app Web

In questa sezione verrà visualizzato il risultato in azione. Configurare prima di tutto l' **app Web client di esempio** per la connessione al flusso di Azure SignalR configurato. Verrà quindi avviata l'app di **esempio per il dispositivo simulato** che invia dati di telemetria tramite l'istanza di Azure Digital gemelli. Successivamente, si visualizzerà l'app Web di esempio per visualizzare i dati del dispositivo simulato che aggiornano l'app Web di esempio in tempo reale.

### <a name="configure-the-sample-client-web-app"></a>Configurare l'app Web client di esempio

Si configurerà quindi l'app Web client di esempio. Iniziare raccogliendo l' **URL dell'endpoint HTTP** della funzione *Negotiate* e quindi usarlo per configurare il codice dell'app nel computer.

1. Passare alla pagina app per le [funzioni](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) del portale di Azure e selezionare l'app per le funzioni dall'elenco. Nel menu dell'app selezionare *funzioni* e scegliere la funzione *Negotiate* .

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Visualizzazione portale di Azure dell'app per le funzioni, con ' functions ' evidenziato nel menu. L'elenco di funzioni viene visualizzato nella pagina e viene evidenziata anche la funzione ' Negotiate '.":::

1. Fare clic su *Ottieni URL funzione* e copiare il valore **fino a _/API_ (non includere l'ultimo _/Negotiate?_)**. Che verrà usato nel passaggio successivo.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Visualizzazione portale di Azure della funzione ' Negotiate '. Il pulsante ' Ottieni URL funzione ' è evidenziato e la parte dell'URL dall'inizio a'/API '":::

1. Usando Visual Studio o un editor di codice di propria scelta, aprire la cartella _**digitaltwins-SignalR-webapp-Sample-Main**_ decompressa scaricata nella sezione [*scaricare le applicazioni di esempio*](#download-the-sample-applications) .

1. Aprire il file *src/App.js* e sostituire l'URL della funzione in `HubConnectionBuilder` con l'URL dell'endpoint HTTP della funzione **Negotiate** salvata nel passaggio precedente:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. Nel *prompt dei comandi* per gli sviluppatori di Visual Studio o in qualsiasi finestra di comando nel computer, passare alla cartella *digitaltwins-SignalR-webapp-Sample-main\src* . Eseguire il comando seguente per installare i pacchetti del nodo dipendenti:

    ```cmd
    npm install
    ```

Impostare quindi le autorizzazioni nell'app per le funzioni nella portale di Azure:
1. Nella pagina app per le [funzioni](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) di portale di Azure selezionare l'istanza dell'app per le funzioni.

1. Scorrere verso il basso nel menu istanza e selezionare *CORS*. Nella pagina CORS aggiungere `http://localhost:3000` come origine consentita inserendola nella casella vuota. Selezionare la casella *Abilita Access-Control-Allow-Credentials* e quindi fare clic su *Save*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Impostazione di CORS nella funzione di Azure":::

### <a name="run-the-device-simulator"></a>Eseguire il simulatore del dispositivo

Durante il prerequisito dell'esercitazione end-to-end, è stato [configurato il simulatore di dispositivi per l'](tutorial-end-to-end.md#configure-and-run-the-simulation) invio di dati tramite un hub Internet e l'istanza di Azure Digital gemelli.

A questo punto, è sufficiente avviare il progetto del simulatore, disponibile in *Digital-Twins-Samples-master > DeviceSimulator > DeviceSimulator. sln*. Se si usa Visual Studio, è possibile aprire il progetto e quindi eseguirlo con questo pulsante sulla barra degli strumenti:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Pulsante di avvio di Visual Studio (progetto DeviceSimulator)":::

Verrà aperta una finestra della console che visualizza i messaggi di telemetria simulati sulle temperature. Questi vengono inviati tramite l'istanza di Azure Digital gemelli, in cui vengono quindi prelevati dalle funzioni di Azure e SignalR.

Non è necessario eseguire altre operazioni in questa console, ma è possibile lasciarla in esecuzione mentre si completa il passaggio successivo.

### <a name="see-the-results"></a>Visualizzare i risultati

Per visualizzare i risultati in azione, avviare l' **esempio di app Web di integrazione SignalR**. Questa operazione può essere eseguita da qualsiasi finestra della console nel percorso *digitaltwins-SignalR-webapp-Sample-main\src* , eseguendo questo comando:

```cmd
npm start
```

Verrà aperta una finestra del browser che esegue l'app di esempio, che visualizza un misuratore della temperatura visiva. Quando l'app è in esecuzione, è consigliabile iniziare a visualizzare i valori di telemetria della temperatura del simulatore di dispositivi che si propagano attraverso i dispositivi gemelli digitali di Azure che vengono riflessi dall'app Web in tempo reale.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Estratto dall'app Web client di esempio che mostra un misuratore della temperatura visiva. La temperatura riflessa è 67,52":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, attenersi alla procedura seguente per eliminarle. 

Usando l'interfaccia della riga di comando di Azure Azure Cloud Shell o locale è possibile eliminare tutte le risorse di Azure in un gruppo di risorse con il comando [AZ Group Delete](/cli/azure/group#az-group-delete) . Rimuovere anche il gruppo di risorse...
* istanza di Azure Digital Twins (dall'esercitazione end-to-end)
* l'hub e la registrazione del dispositivo hub (dall'esercitazione end-to-end)
* argomento di griglia di eventi e sottoscrizioni associate
* l'app funzioni di Azure, che include tutte e tre le funzioni e le risorse associate, ad esempio l'archiviazione
* istanza di Azure SignalR

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Eliminare infine le cartelle di esempio del progetto scaricate nel computer locale (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* e le relative controparti decompresse).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come configurare funzioni di Azure con SignalR per trasmettere gli eventi di telemetria di Azure Digital gemelli a un'applicazione client di esempio.

Successivamente, altre informazioni sul servizio Azure SignalR:
* [*Informazioni sul servizio Azure SignalR*](../azure-signalr/signalr-overview.md)

Per altre informazioni, vedere autenticazione del servizio Azure SignalR con funzioni di Azure:
* [*Autenticazione del servizio Azure SignalR*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)