---
title: Integrazione con Servizio Azure SignalR
titleSuffix: Azure Digital Twins
description: Informazioni su come trasmettere dati Gemelli digitali di Azure telemetria ai client usando Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e8bdb843ab6304f2f38228f37d8709e4084ee52e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775332"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrare Gemelli digitali di Azure con Servizio Azure SignalR

In questo articolo si apprenderà come integrare Gemelli digitali di Azure con [Servizio Azure SignalR](../azure-signalr/signalr-overview.md).

La soluzione descritta in questo articolo consente di eseguire il push dei dati di telemetria dei gemelli digitali ai client connessi, ad esempio una singola pagina Web o un'applicazione per dispositivi mobili. Di conseguenza, i client vengono aggiornati con metriche e stato in tempo reale dai dispositivi IoT, senza la necessità di eseguire il polling del server o inviare nuove richieste HTTP per gli aggiornamenti.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono elencati i prerequisiti da completare prima di procedere:

* Prima di integrare la soluzione con Servizio Azure SignalR in questo articolo, è necessario completare l'esercitazione di Gemelli digitali di Azure: Connettere una [_**soluzione end-to-end**_](tutorial-end-to-end.md), perché questo articolo di procedura si basa su di essa. L'esercitazione illustra come configurare un'Gemelli digitali di Azure che funziona con un dispositivo IoT virtuale per attivare gli aggiornamenti dei gemelli digitali. Questo articolo descrive come connettere questi aggiornamenti a un'app Web di esempio usando Servizio Azure SignalR.

* Saranno necessari i valori seguenti dell'esercitazione:
  - Argomento griglia di eventi
  - Resource group
  - Nome dell'app per il servizio app/per le funzioni
    
* Sarà necessario installare [**Node.js**](https://nodejs.org/) nel computer.

È anche consigliabile procedere e accedere al portale di Azure [con](https://portal.azure.com/) l'account Azure.

## <a name="solution-architecture"></a>Architettura della soluzione

Si allega un Servizio Azure SignalR Gemelli digitali di Azure tramite il percorso seguente. Le sezioni A, B e C del diagramma sono tratta dal diagramma dell'architettura del prerequisito dell'esercitazione [end-to-end](tutorial-end-to-end.md). In questo articolo si compila la sezione D sull'architettura esistente.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Visualizzazione dei servizi di Azure in uno scenario end-to-end. Illustra il flusso di dati da un dispositivo all'hub IoT, tramite una funzione di Azure (freccia B) a un'istanza di Gemelli digitali di Azure (sezione A), quindi da Griglia di eventi a un'altra funzione di Azure per l'elaborazione (freccia C). La sezione D mostra il flusso di dati dalla stessa griglia di eventi nella freccia C verso una funzione di Azure con etichetta &quot;broadcast&quot;. &quot;broadcast&quot; comunica con un'altra funzione di Azure con etichetta &quot;negotiate&quot; e &quot;broadcast&quot; e &quot;negotiate&quot; comunicano con i dispositivi del computer." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Scaricare le applicazioni di esempio

Prima di tutto, scaricare le app di esempio necessarie. Sono necessari entrambi gli elementi seguenti:
* [**Gemelli digitali di Azure esempi end-to-end:**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)questo esempio contiene un'app *AdtSampleApp* che contiene due funzioni di Azure per lo spostamento dei dati in un'istanza di Gemelli digitali di Azure . Per altre informazioni su questo scenario, vedere Esercitazione: Connettere una soluzione [*end-to-end.*](tutorial-end-to-end.md) Contiene anche *un'applicazione di esempio DeviceSimulator* che simula un dispositivo IoT, generando un nuovo valore di temperatura ogni secondo.
    - Se l'esempio non è ancora stato scaricato come parte dell'esercitazione [*in*](#prerequisites)  Prerequisiti, passare al collegamento dell'esempio e selezionare il pulsante Sfoglia codice sotto il titolo. [](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) Verrà visualizzato il repository GitHub per gli esempi, che è possibile scaricare come *. ZIP* selezionando il pulsante *Code (Codice)* *e Download ZIP (Scarica ZIP).*

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Visualizzazione del repository digital-twins-samples in GitHub. Il pulsante Codice è selezionato, generando una piccola finestra di dialogo in cui è evidenziato il pulsante Scarica ZIP." lightbox="media/includes/download-repo-zip.png":::

    Verrà scaricata una copia del repo di esempio nel computer, **digital-twins-samples-master.zip**. Decomprimere la cartella.
* [**Esempio di app Web di integrazione SignalR:**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)si tratta di un'app Web React di esempio che Gemelli digitali di Azure dati di telemetria da un Servizio Azure SignalR.
    -  Passare al collegamento di esempio e usare lo stesso processo di download per scaricare una copia dell'esempio nel computer, _**comedigitaltwins-signalr-webapp-sample-main.zip**_. Decomprimere la cartella.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Lasciare aperta la finestra del browser portale di Azure, perché verrà nuovamente utilizzata nella sezione successiva.

## <a name="publish-and-configure-the-azure-functions-app"></a>Pubblicare e configurare l Funzioni di Azure app

In questa sezione verranno impostate due funzioni di Azure:
* **negotiate:** funzione trigger HTTP. Usa l'associazione di input *SignalRConnectionInfo* per generare e restituire informazioni di connessione valide.
* **broadcast:** funzione [trigger di Griglia](../event-grid/overview.md) di eventi. Riceve i Gemelli digitali di Azure dati di telemetria tramite la griglia di eventi e usa l'associazione di output dell'istanza *di SignalR* creata nel passaggio precedente per trasmettere il messaggio a tutte le applicazioni client connesse.

Avviare Visual Studio (o un altro editor di codice di propria scelta) e aprire la soluzione di codice nella cartella *digital-twins-samples-master > ADTSampleApp.* Eseguire quindi la procedura seguente per creare le funzioni:

1. Nel progetto *SampleFunctionsApp* creare una nuova classe C# denominata **SignalRFunctions.cs.**

1. Sostituire il contenuto del file di classe con il codice seguente:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Nella finestra della *console Gestione pacchetti* di Visual Studio o in qualsiasi finestra di comando del computer passare alla cartella *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* ed eseguire il comando seguente per installare il pacchetto `SignalRService` NuGet nel progetto:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Questo dovrebbe risolvere eventuali problemi di dipendenza nella classe .

1. Pubblicare la funzione in Azure, usando la procedura descritta nella sezione [ *Pubblicare l'app*](tutorial-end-to-end.md#publish-the-app) dell'esercitazione Connettere una soluzione *end-to-end.* È possibile pubblicarlo nella stessa app per il servizio app o [](#prerequisites)per le funzioni usata nel prerequisito dell'esercitazione end-to-end oppure crearne una nuova, ma è possibile usare la stessa app per ridurre al minimo la duplicazione. 

Configurare quindi le funzioni per comunicare con l'istanza di Azure SignalR. Si inizierà raccogliendo la stringa di connessione dell'istanza di SignalR e quindi aggiungerla alle impostazioni dell'app per le funzioni.

1. Passare al [portale di Azure](https://portal.azure.com/) e cercare il nome dell'istanza di SignalR nella barra di ricerca nella parte superiore del portale. Selezionare l'istanza per aprirla.
1. Selezionare **Chiavi dal** menu dell'istanza per visualizzare le stringhe di connessione per l'istanza del servizio SignalR.
1. Selezionare *l'icona* Copia per copiare la stringa di connessione primaria.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Screenshot del portale di Azure che mostra la pagina Chiavi per l'istanza di SignalR. L'icona &quot;Copia negli Appunti&quot; accanto a Stringa di connessione primaria è evidenziata." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Aggiungere infine la stringa di connessione di Azure **SignalR** alle impostazioni dell'app della funzione usando il comando dell'interfaccia della riga di comando di Azure seguente. Sostituire anche i segnaposto con il gruppo di risorse e il nome del servizio app o dell'app per le funzioni dal [prerequisito dell'esercitazione.](how-to-integrate-azure-signalr.md#prerequisites) Il comando può essere eseguito [in](https://shell.azure.com)Azure Cloud Shell o in locale se nel computer è installata l'interfaccia della riga di [comando di](/cli/azure/install-azure-cli)Azure:
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    L'output di questo comando stampa tutte le impostazioni dell'app impostate per la funzione di Azure. Cercare nella `AzureSignalRConnectionString` parte inferiore dell'elenco per verificare che sia stato aggiunto.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Estratto dell'output in una finestra di comando, che mostra un elemento dell'elenco denominato 'AzureSignalRConnectionString'":::

#### <a name="connect-the-function-to-event-grid"></a>Connettere la funzione a Griglia di eventi

Sottoscrivere quindi la *funzione di* Azure broadcast all'argomento di **Griglia di** eventi creato durante il [prerequisito dell'esercitazione.](how-to-integrate-azure-signalr.md#prerequisites) Ciò consentirà il flusso dei dati di telemetria dal gemello thermostat67 attraverso l'argomento di Griglia di eventi e fino alla funzione . Da qui, la funzione può trasmettere i dati a tutti i client.

A tale scopo, si creerà una sottoscrizione **di** eventi dall'argomento di Griglia di eventi alla funzione *di* Azure trasmessa come endpoint.

Nel [portale di Azure](https://portal.azure.com/) passare all'argomento di Griglia di eventi cercandone il nome nella barra di ricerca in alto. Selezionare *+ Sottoscrizione di eventi*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Portale di Azure: Sottoscrizione di eventi di Griglia di eventi":::

Nella pagina *Crea sottoscrizione di eventi* compilare i campi come indicato di seguito (i campi compilati per impostazione predefinita non sono indicati):
* *DETTAGLI SOTTOSCRIZIONE EVENTO* > **Nome**: assegnare un nome alla sottoscrizione di eventi.
* *DETTAGLI ENDPOINT* > **Tipo di endpoint** : selezionare *Funzione di Azure* nelle opzioni del menu.
* *DETTAGLI ENDPOINT* > **Endpoint**: scegliere il collegamento *Seleziona un endpoint*. Verrà aperta la finestra *Seleziona funzione di Azure*:
    - Compilare i campi **Sottoscrizione**, **Gruppo di risorse**, App per le **funzioni** **e Funzione** (*broadcast*). È possibile che alcuni di questi campi vengano prepopolati dopo aver selezionato la sottoscrizione.
    - Premere **Confermare la selezione**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="portale di Azure di creazione di una sottoscrizione di eventi. I campi precedenti vengono compilati e i pulsanti &quot;Conferma selezione&quot; e &quot;Crea&quot; sono evidenziati.":::

Di nuovo nella pagina *Crea sottoscrizione di eventi* premere **Crea**.

A questo punto, nella pagina Argomento di Griglia di eventi dovrebbero essere presenti due *sottoscrizioni di* eventi.

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="portale di Azure di due sottoscrizioni di eventi nella pagina dell'argomento Griglia di eventi." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Configurare ed eseguire l'app Web

In questa sezione verrà visualizzato il risultato in azione. Configurare prima di tutto **l'app Web client** di esempio per connettersi al flusso di Azure SignalR configurato. Verrà quindi avviata l'app di esempio del dispositivo **simulato** che invia i dati di telemetria tramite l'Gemelli digitali di Azure istanza. Successivamente, verrà visualizzata l'app Web di esempio per visualizzare i dati del dispositivo simulato che aggiornano l'app Web di esempio in tempo reale.

### <a name="configure-the-sample-client-web-app"></a>Configurare l'app Web client di esempio

Successivamente, si configurerà l'app Web client di esempio. Iniziare raccogliendo **l'URL dell'endpoint HTTP** della *funzione negotiate* e quindi usarlo per configurare il codice dell'app nel computer.

1. Passare alla pagina Portale di Azure app [per le funzioni](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) e selezionare l'app per le funzioni dall'elenco. Nel menu dell'app selezionare *Funzioni e* scegliere la *funzione negozia.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="portale di Azure dell'app per le funzioni, con 'Funzioni' evidenziato nel menu. L'elenco delle funzioni viene visualizzato nella pagina e viene evidenziata anche la funzione 'negotiate'.":::

1. Premere *Ottieni l'URL* della funzione e copiare il valore **tramite _/api_ (non includere l'ultimo _/negotiate?_).** Questa operazione verrà utilizzata nel passaggio successivo.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="portale di Azure della funzione 'negotiate'. Il pulsante &quot;Get function URL&quot; (Ottieni URL funzione) è evidenziato e la parte dell'URL dall'inizio a '/api'":::

1. Usando Visual Studio o qualsiasi editor di codice di propria scelta, aprire la cartella _**digitaltwins-signalr-webapp-sample-main**_ decompressa scaricata nella sezione Scaricare le applicazioni [*di*](#download-the-sample-applications) esempio.

1. Aprire il file *src/App.js* e sostituire l'URL della funzione in con l'URL dell'endpoint HTTP della funzione negotiate salvata `HubConnectionBuilder` nel passaggio precedente: 

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. Nel *prompt* dei comandi per sviluppatori di Visual Studio o in qualsiasi finestra di comando nel computer passare alla cartella *digitaltwins-signalr-webapp-sample-main\src.* Eseguire il comando seguente per installare i pacchetti del nodo dipendente:

    ```cmd
    npm install
    ```

Impostare quindi le autorizzazioni nell'app per le funzioni nel portale di Azure:
1. Nella pagina portale di Azure app [per le funzioni](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) selezionare l'istanza dell'app per le funzioni.

1. Scorrere verso il basso nel menu dell'istanza e selezionare *CORS.* Nella pagina CORS aggiungere `http://localhost:3000` come origine consentita immettendolo nella casella vuota. Selezionare la casella Abilita *Access-Control-Allow-Credentials* e fare clic su *Salva.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Impostazione CORS nella funzione di Azure":::

### <a name="run-the-device-simulator"></a>Eseguire il simulatore di dispositivi

Durante il prerequisito dell'esercitazione end-to-end è stato configurato il [simulatore](tutorial-end-to-end.md#configure-and-run-the-simulation) di dispositivo per inviare dati tramite un hub IoT e all'istanza Gemelli digitali di Azure servizio.

A questo punto, è necessario solo avviare il progetto di simulatore, che si trova in *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln*. Se si usa un Visual Studio, è possibile aprire il progetto e quindi eseguirlo con questo pulsante sulla barra degli strumenti:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Pulsante di avvio di Visual Studio (progetto DeviceSimulator)":::

Verrà aperta una finestra della console che visualizza i messaggi di telemetria simulati sulle temperature. Questi vengono inviati tramite l'istanza Gemelli digitali di Azure, in cui vengono quindi prelevati dalle funzioni di Azure e da SignalR.

Non è necessario eseguire altre attività in questa console, ma lasciarla in esecuzione mentre si completa il passaggio successivo.

### <a name="see-the-results"></a>Visualizzare i risultati

Per visualizzare i risultati in azione, avviare l'esempio di **app Web di integrazione SignalR**. È possibile eseguire questa operazione da qualsiasi finestra della console nel percorso *digitaltwins-signalr-webapp-sample-main\src* eseguendo questo comando:

```cmd
npm start
```

Verrà aperta una finestra del browser che esegue l'app di esempio, che visualizza un misuratore della temperatura visiva. Quando l'app è in esecuzione, è consigliabile iniziare a visualizzare i valori di telemetria della temperatura dal simulatore di dispositivo che si propagano tramite Gemelli digitali di Azure riflessa dall'app Web in tempo reale.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Estratto dell'app Web client di esempio, che mostra un misuratore della temperatura visiva. La temperatura riflessa è 67,52":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, seguire questa procedura per eliminarle. 

Usando l'Azure Cloud Shell o l'interfaccia della riga di comando di Azure locale, è possibile eliminare tutte le risorse di Azure in un gruppo di risorse con [il comando az group delete.](/cli/azure/group#az_group_delete) La rimozione del gruppo di risorse rimuoverà anche ...
* l Gemelli digitali di Azure a istanza (dall'esercitazione end-to-end)
* l'hub IoT e la registrazione del dispositivo hub (dall'esercitazione end-to-end)
* l'argomento griglia di eventi e le sottoscrizioni associate
* l Funzioni di Azure app, incluse tutte e tre le funzioni e le risorse associate, ad esempio l'archiviazione
* istanza di Azure SignalR

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Infine, eliminare le cartelle di esempio del progetto scaricate nel computer locale (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* e le relative controparti decompresse).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si configura Funzioni di Azure con SignalR per trasmettere Gemelli digitali di Azure di telemetria a un'applicazione client di esempio.

Altre informazioni sulle Servizio Azure SignalR:
* [*Informazioni sul servizio Azure SignalR*](../azure-signalr/signalr-overview.md)

In altre informazioni sull'autenticazione Servizio Azure SignalR con Funzioni di Azure:
* [*Autenticazione del servizio Azure SignalR*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)