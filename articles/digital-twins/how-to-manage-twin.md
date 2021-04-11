---
title: Gestire i gemelli digitali
titleSuffix: Azure Digital Twins
description: Vedere come recuperare, aggiornare ed eliminare singoli gemelli e relazioni.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 666e77a06bd2934622400cc2f11830d6ebc34ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954650"
---
# <a name="manage-digital-twins"></a>Gestire i gemelli digitali

Le entità nell'ambiente sono rappresentate da dispositivi [gemelli digitali](concepts-twins-graph.md). La gestione dei dispositivi gemelli digitali può includere la creazione, la modifica e la rimozione. Per eseguire queste operazioni, è possibile usare le [**API DigitalTwins**](/rest/api/digital-twins/dataplane/twins), [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Questo articolo è incentrato sulla gestione di dispositivi gemelli digitali; per lavorare con le relazioni e il [grafo gemello](concepts-twins-graph.md) nel suo complesso, vedere [*How-to: Manage The Twin Graph with Relationships*](how-to-manage-graph.md).

> [!TIP]
> Tutte le funzioni SDK sono disponibili in versioni sincrone e asincrone.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>Modalità di gestione di dispositivi gemelli

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Creare un dispositivo gemello digitale

Per creare un dispositivo gemello, usare il `CreateOrReplaceDigitalTwinAsync()` metodo nel client del servizio come segue:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

Per creare un dispositivo gemello digitale, è necessario fornire:
* ID desiderato per il dispositivo gemello digitale
* [Modello](concepts-models.md) che si desidera utilizzare

Facoltativamente, è possibile fornire i valori iniziali per tutte le proprietà del dispositivo gemello digitale. Le proprietà vengono considerate facoltative e possono essere impostate in un secondo momento, ma **non verranno visualizzate come parte di un gemello fino a quando non sono state impostate.**

>[!NOTE]
>Sebbene non sia necessario inizializzare le proprietà dei dispositivi gemelli, è **necessario impostare** tutti i [componenti](concepts-models.md#elements-of-a-model) del dispositivo gemello quando viene creato il dispositivo gemello. Possono essere oggetti vuoti, ma devono esistere anche i componenti.

Il modello ed eventuali valori di proprietà iniziali vengono forniti tramite il `initData` parametro, ovvero una stringa JSON contenente i dati rilevanti. Per ulteriori informazioni sulla strutturazione di questo oggetto, passare alla sezione successiva.

> [!TIP]
> Dopo la creazione o l'aggiornamento di un dispositivo gemello, è possibile che si verifichi una latenza di un massimo di 10 secondi prima che le modifiche vengano riflesse nelle [query](how-to-query-graph.md). L' `GetDigitalTwin` API, descritta [più avanti in questo articolo](#get-data-for-a-digital-twin), non riscontra questo ritardo, quindi se è necessaria una risposta immediata, usare la chiamata API anziché eseguire query per visualizzare i dispositivi gemelli appena creati. 

### <a name="initialize-model-and-properties"></a>Inizializzare il modello e le proprietà

È possibile inizializzare le proprietà di un gemello al momento della creazione del dispositivo gemello. 

L'API per la creazione di dispositivi gemelli accetta un oggetto che viene serializzato in una descrizione JSON valida delle proprietà dei dispositivi gemelli. Per una descrizione del formato JSON per un gemello, vedere [*concetti: dispositivi gemelli digitali e il grafico a gemelli*](concepts-twins-graph.md) . 

In primo luogo, è possibile creare un oggetto dati per rappresentare il gemello e i relativi dati della proprietà. È possibile creare un oggetto Parameter manualmente oppure usando una classe helper fornita. Di seguito è riportato un esempio di ogni.

#### <a name="create-twins-using-manually-created-data"></a>Creare i dispositivi gemelli usando dati creati manualmente

Senza l'uso di alcuna classe helper personalizzata, è possibile rappresentare le proprietà di un gemello in un `Dictionary<string, object>` oggetto, dove `string` è il nome della proprietà e `object` è un oggetto che rappresenta la proprietà e il relativo valore.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Creare i dispositivi gemelli con la classe helper

La classe helper di `BasicDigitalTwin` consente di archiviare direttamente i campi di proprietà in un oggetto "gemello". È comunque possibile creare l'elenco di proprietà usando un `Dictionary<string, object>` oggetto, che può quindi essere aggiunto direttamente all'oggetto gemello `CustomProperties` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` gli oggetti vengono inclusi in un `Id` campo. È possibile lasciare vuoto questo campo, ma se si aggiunge un valore ID, è necessario che corrisponda al parametro ID passato alla `CreateOrReplaceDigitalTwinAsync()` chiamata. Ad esempio:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Ottenere i dati per un dispositivo gemello digitale

È possibile accedere ai dettagli di qualsiasi dispositivo gemello digitale chiamando il `GetDigitalTwin()` metodo come segue:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Questa chiamata restituisce i dati gemelli come un tipo di oggetto fortemente tipizzato, ad esempio `BasicDigitalTwin` . `BasicDigitalTwin` è una classe helper di serializzazione inclusa nell'SDK, che restituirà i metadati e le proprietà del gemello di base in un modulo pre-analizzato. Ecco un esempio di come usare questa procedura per visualizzare i dettagli dei dispositivi gemelli:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Quando si recupera un gemello con il metodo, vengono restituite solo le proprietà impostate almeno una volta `GetDigitalTwin()` .

>[!TIP]
>`displayName`Per un dispositivo gemello fa parte dei metadati del modello, pertanto non verrà visualizzato quando si recuperano i dati per l'istanza del dispositivo gemello. Per visualizzare questo valore, è possibile [recuperarlo dal modello](how-to-manage-model.md#retrieve-models).

Per recuperare più dispositivi gemelli usando una singola chiamata API, vedere gli esempi di API di query in [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

Si consideri il modello seguente (scritto in [Digital Gemini Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)) che definisce una *Luna*:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

Il risultato della chiamata `object result = await client.GetDigitalTwinAsync("my-moon");` su un gemello di tipo *Moon* potrebbe essere simile al seguente:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Le proprietà definite del gemello digitale vengono restituite come proprietà di primo livello nel dispositivo gemello digitale. I metadati o le informazioni di sistema che non fanno parte della definizione DTDL vengono restituiti con un `$` prefisso. Le proprietà dei metadati includono i valori seguenti:
* `$dtId`: ID del dispositivo gemello digitale in questa istanza di Azure Digital Twins
* `$etag`: Campo HTTP standard assegnato dal server Web. Questa operazione viene aggiornata a un nuovo valore ogni volta che il dispositivo gemello viene aggiornato, che può essere utile per determinare se i dati del gemello sono stati aggiornati nel server dopo un controllo precedente. È possibile usare `If-Match` per eseguire gli aggiornamenti e le eliminazioni che vengono completate solo se l'ETag dell'entità corrisponde al valore ETag fornito. Per ulteriori informazioni su queste operazioni, vedere la documentazione relativa a [DigitalTwins Update](/rest/api/digital-twins/dataplane/twins/digitaltwins_update) e [DigitalTwins Delete](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete).
* `$metadata`: Un set di altre proprietà, tra cui:
  - DTMI del modello del gemello digitale.
  - Stato di sincronizzazione per ogni proprietà scrivibile. Questa operazione è particolarmente utile per i dispositivi, in cui è possibile che il servizio e il dispositivo abbiano stati divergenti, ad esempio quando un dispositivo è offline. Attualmente questa proprietà si applica solo ai dispositivi fisici connessi all'hub Internet. Con i dati nella sezione dei metadati, è possibile comprendere lo stato completo di una proprietà, oltre ai timestamp dell'Ultima modifica. Per altre informazioni sullo stato di sincronizzazione, vedere [questa esercitazione sull'hub di questo](../iot-hub/tutorial-device-twins.md) strumento sulla sincronizzazione dello stato del dispositivo.
  - Metadati specifici del servizio, ad esempio dall'hub o dai dispositivi gemelli digitali di Azure. 

Per altre informazioni sulle classi helper di serializzazione, `BasicDigitalTwin` ad esempio [*, vedere Procedura: usare gli SDK e le API dei dispositivi gemelli digitali di Azure*](how-to-use-apis-sdks.md).

## <a name="view-all-digital-twins"></a>Visualizza tutti i dispositivi gemelli digitali

Per visualizzare tutti i dispositivi gemelli digitali nell'istanza, usare una [query](how-to-query-graph.md). È possibile eseguire una query con le [API di query](/rest/api/digital-twins/dataplane/query) o i [comandi dell'interfaccia](how-to-use-cli.md)della riga di comando.

Di seguito è riportato il corpo della query di base che restituirà un elenco di tutti i dispositivi gemelli digitali nell'istanza:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Aggiornare un gemello digitale

Per aggiornare le proprietà di un dispositivo gemello digitale, è necessario scrivere le informazioni che si desidera sostituire nel formato di [patch JSON](http://jsonpatch.com/) . In questo modo, è possibile sostituire più proprietà contemporaneamente. Passare quindi il documento della patch JSON in un `UpdateDigitalTwin()` Metodo:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Una chiamata patch può aggiornare tutte le proprietà di un singolo gemello come si preferisce (anche tutte). Se è necessario aggiornare le proprietà tra più dispositivi gemelli, sarà necessaria una chiamata di aggiornamento separata per ogni dispositivo gemello.

> [!TIP]
> Dopo la creazione o l'aggiornamento di un dispositivo gemello, è possibile che si verifichi una latenza di un massimo di 10 secondi prima che le modifiche vengano riflesse nelle [query](how-to-query-graph.md). L' `GetDigitalTwin` API (descritta [in precedenza in questo articolo](#get-data-for-a-digital-twin)) non presenta questo ritardo, quindi usare la chiamata API invece di eseguire query per visualizzare i gemelli appena aggiornati se è necessaria una risposta immediata. 

Di seguito è riportato un esempio di codice patch JSON. Questo documento sostituisce i valori delle proprietà di *massa* e *RADIUS* del dispositivo gemello digitale a cui viene applicato.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

È possibile creare patch usando il [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument)di Azure .NET SDK. Ecco un esempio.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>Aggiornare le proprietà nei componenti gemelli digitali

Tenere presente che un modello può contenere componenti, in modo che sia costituito da altri modelli. 

Per applicare patch alle proprietà nei componenti di un dispositivo gemello digitale, è possibile usare la sintassi del percorso nella patch JSON:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>Aggiornare il modello di un dispositivo gemello digitale

La `UpdateDigitalTwin()` funzione può essere usata anche per eseguire la migrazione di un dispositivo gemello digitale a un modello diverso. 

Si consideri, ad esempio, il documento di patch JSON seguente che sostituisce il campo dei metadati del gemello digitale `$model` :

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Questa operazione avrà esito positivo solo se il dispositivo gemello digitale modificato dalla patch è conforme al nuovo modello. 

Si consideri l'esempio seguente:
1. Immaginate un dispositivo gemello digitale con un modello di *foo_old*. *foo_old* definisce una *massa* di proprietà obbligatoria.
2. Il nuovo modello *foo_new* definisce una massa della proprietà e aggiunge una nuova *temperatura* della proprietà richiesta.
3. Dopo la patch, il dispositivo gemello digitale deve avere una proprietà di massa e di temperatura. 

La patch per questa situazione deve aggiornare sia il modello che la proprietà temperatura del dispositivo gemello, come indicato di seguito:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Gestire le chiamate di aggiornamento in conflitto

I dispositivi gemelli digitali di Azure garantiscono che tutte le richieste in ingresso vengano elaborate una dopo l'altra. Ciò significa che, anche se più funzioni tentano di aggiornare la stessa proprietà in un dispositivo gemello allo stesso tempo, non è **necessario** scrivere codice di blocco esplicito per gestire il conflitto.

Questo comportamento si basa su un singolo dispositivo. 

Si supponga, ad esempio, uno scenario in cui queste tre chiamate arrivano allo stesso tempo: 
*   Scrivi proprietà A in *Twin1*
*   Scrivere la proprietà B in *Twin1*
*   Scrivi proprietà A in *Twin2*

Le due chiamate che modificano *Twin1* vengono eseguite una dopo l'altra e i messaggi delle modifiche vengono generati per ogni modifica. La chiamata a Modify *Twin2* può essere eseguita simultaneamente senza conflitti, non appena arriva.

## <a name="delete-a-digital-twin"></a>Eliminare un dispositivo gemello digitale

È possibile eliminare i dispositivi gemelli usando il `DeleteDigitalTwin()` metodo. Tuttavia, è possibile eliminare un gemello solo quando non sono presenti altre relazioni. Quindi, eliminare prima le relazioni in ingresso e in uscita del dispositivo gemello.

Di seguito è riportato un esempio del codice per eliminare i gemelli e le relative relazioni. La `DeleteDigitalTwin` chiamata SDK è evidenziata per chiarire il punto in cui rientra nel contesto di esempio più ampio.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Elimina tutti i dispositivi gemelli digitali

Per un esempio di come eliminare tutti i dispositivi gemelli contemporaneamente, scaricare l'app di esempio usata nell' [*esercitazione: esplorare le nozioni di base con un'app client di esempio*](tutorial-command-line-app.md). Il file *CommandLoop. cs* esegue questa operazione in una `CommandDeleteAllTwins()` funzione.

## <a name="runnable-digital-twin-code-sample"></a>Esempio di codice di un gemello digitale eseguibile

È possibile usare l'esempio di codice eseguibile riportato di seguito per creare un dispositivo gemello, aggiornarne i dettagli ed eliminare il dispositivo gemello. 

### <a name="set-up-the-runnable-sample"></a>Configurare l'esempio eseguibile

Il frammento di codice usa il [Room.jsnella](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definizione del modello da [*esercitazione: esplorare i dispositivi gemelli digitali di Azure con un'app client di esempio*](tutorial-command-line-app.md). È possibile usare questo collegamento per passare direttamente al file o scaricarlo come parte del progetto di esempio end-to-end completo [qui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Prima di eseguire l'esempio, eseguire le operazioni seguenti:
1. Scaricare il file del modello, inserirlo nel progetto e sostituire il `<path-to>` segnaposto nel codice seguente per indicare al programma dove trovarlo.
2. Sostituire il segnaposto `<your-instance-hostname>` con il nome host dell'istanza di Azure Digital gemelli.
3. Aggiungere due dipendenze al progetto che saranno necessarie per lavorare con i dispositivi gemelli digitali di Azure. La prima è il pacchetto per [Azure Digital Twins SDK per .NET](/dotnet/api/overview/azure/digitaltwins/client), la seconda fornisce gli strumenti che consentono di eseguire l'autenticazione in Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

È inoltre necessario configurare le credenziali locali se si desidera eseguire l'esempio direttamente. La sezione successiva illustra questa procedura.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Eseguire l'esempio

Dopo aver completato i passaggi precedenti, è possibile eseguire direttamente il codice di esempio seguente.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

Ecco l'output della console del programma precedente: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Output della console che mostra che il dispositivo gemello è stato creato, aggiornato ed eliminato" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Passaggi successivi

Vedere come creare e gestire le relazioni tra i dispositivi gemelli digitali:
* [*Procedura: gestire il grafo gemello con relazioni*](how-to-manage-graph.md)