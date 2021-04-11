---
title: Gestire il grafo dei gemelli con relazioni
titleSuffix: Azure Digital Twins
description: Vedere come gestire un grafico dei dispositivi gemelli digitali connettendosi con le relazioni.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102433319"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gestire un grafico di gemelli digitali usando relazioni

Il cuore di Azure Digital gemelli è il [grafo gemello](concepts-twins-graph.md) che rappresenta l'intero ambiente. Il grafo gemello è costituito da singoli gemelli digitali connessi tramite **relazioni**. 

Quando si ha un' [istanza di Azure Digital Twins](how-to-set-up-instance-portal.md) funzionante e si è configurato il codice di [autenticazione](how-to-authenticate-client.md) nell'app client, è possibile usare le [**API DigitalTwins**](/rest/api/digital-twins/dataplane/twins) per creare, modificare ed eliminare i dispositivi gemelli digitali e le relative relazioni in un'istanza di Azure Digital gemelli. È anche possibile usare [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md).

Questo articolo è incentrato sulla gestione delle relazioni e del grafo nel suo complesso; per lavorare con singoli dispositivi gemelli digitali, vedere [*How-to: Manage Digital gemells*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Modalità di gestione del grafo

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

È anche possibile apportare modifiche al grafo usando l'esempio Azure Digital Twins Explorer, che consente di visualizzare i dispositivi gemelli e il grafo e di usare l'SDK in background. Questo esempio viene descritto in dettaglio nella sezione successiva.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Creare relazioni

Le relazioni descrivono il modo in cui i gemelli digitali sono connessi tra loro, che costituisce la base del grafo gemello.

Le relazioni vengono create utilizzando la `CreateOrReplaceRelationshipAsync()` chiamata. 

Per creare una relazione, è necessario specificare:
* ID del dispositivo gemello `srcId` di origine (nell'esempio di codice riportato di seguito): ID del gemello in cui ha origine la relazione.
* ID del gemello di destinazione ( `targetId` nell'esempio di codice riportato di seguito): l'ID del gemello in cui arriva la relazione.
* Un nome di relazione ( `relName` nell'esempio di codice riportato di seguito): tipo generico di relazione, simile a _Contains_.
* Un ID relazione ( `relId` nell'esempio di codice riportato di seguito): il nome specifico per la relazione, ad esempio _Relationship1_.

L'ID relazione deve essere univoco all'interno del gemello di origine specificato. Non è necessario che sia globalmente univoco.
Per il *foo* gemello, ad esempio, ogni ID relazione specifico deve essere univoco. Tuttavia, un'altra *barra* gemella può avere una relazione in uscita che corrisponde allo stesso ID di una relazione *foo* .

L'esempio di codice seguente illustra come creare una relazione nell'istanza di Azure Digital gemelli. Usa la chiamata SDK (evidenziata) all'interno di un metodo personalizzato che può essere visualizzato nel contesto di un programma più grande.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Questa funzione personalizzata può ora essere chiamata per creare una relazione _Contains_ come la seguente: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Se si desidera creare più relazioni, è possibile ripetere le chiamate allo stesso metodo, passando tipi di relazione diversi nell'argomento. 

Per altre informazioni sulla classe helper `BasicRelationship` , vedere [*How-to: Use the Azure Digital Twins API and SDKs*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Creare più relazioni tra i dispositivi gemelli

Le relazioni possono essere classificate come una delle seguenti: 

* Relazioni in uscita: relazioni appartenenti a questo gemello che puntano verso l'esterno per connetterle ad altri dispositivi gemelli. Il `GetRelationshipsAsync()` metodo viene usato per ottenere le relazioni in uscita di un dispositivo gemello.
* Relazioni in ingresso: relazioni appartenenti ad altri gemelli che puntano a questo gemello per creare un collegamento "in ingresso". Il `GetIncomingRelationshipsAsync()` metodo viene usato per ottenere le relazioni in ingresso di un dispositivo gemello.

Non esiste alcuna restrizione al numero di relazioni che è possibile avere tra due gemelli. è possibile avere tutte le relazioni tra i dispositivi gemelli. 

Ciò significa che è possibile esprimere più tipi diversi di relazioni tra due gemelli in una sola volta. Ad esempio, *il gemello A* può avere una relazione *archiviata* e una relazione *prodotta* con il *gemello B*.

Se lo si desidera, è anche possibile creare più istanze dello stesso tipo di relazione tra gli stessi due gemelli. In questo esempio, il *gemello a* può avere due relazioni *archiviate* diverse con il *gemello B*, purché le relazioni abbiano ID relazione diversi.

## <a name="list-relationships"></a>Elencare le relazioni

Per accedere all'elenco delle relazioni in **uscita** per un determinato gemello nel grafico, è possibile usare il `GetRelationships()` metodo come segue:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

`Azure.Pageable<T>`Viene restituito o `Azure.AsyncPageable<T>` , a seconda che si usi la versione sincrona o asincrona della chiamata.

Di seguito è riportato un esempio in cui viene recuperato un elenco di relazioni. Usa la chiamata SDK (evidenziata) all'interno di un metodo personalizzato che può essere visualizzato nel contesto di un programma più grande.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

È ora possibile chiamare questo metodo personalizzato per vedere le relazioni in uscita dei dispositivi gemelli come segue:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

È possibile usare le relazioni recuperate per passare ad altri gemelli nel grafo. A tale scopo, leggere il `target` campo dalla relazione restituita e usarlo come ID per la chiamata successiva a `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Trovare le relazioni in ingresso a un dispositivo gemello digitale

I dispositivi gemelli digitali di Azure hanno anche un'API che consente di trovare tutte le relazioni in **ingresso** per un dispositivo gemello specifico. Questa operazione è spesso utile per la navigazione inversa o quando si elimina un dispositivo gemello.

>[!NOTE]
> `IncomingRelationship` le chiamate non restituiscono il corpo completo della relazione. Per ulteriori informazioni sulla `IncomingRelationship` classe, vedere la relativa [documentazione di riferimento](/dotnet/api/azure.digitaltwins.core.incomingrelationship).

L'esempio di codice nella sezione precedente si è concentrato sull'individuazione delle relazioni in uscita da un dispositivo gemello. L'esempio seguente è strutturato in modo analogo, ma trova invece le relazioni in *ingresso* al dispositivo gemello. Questo esempio usa anche la chiamata SDK (evidenziata) all'interno di un metodo personalizzato che può essere visualizzato nel contesto di un programma più grande.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

È ora possibile chiamare questo metodo personalizzato per vedere le relazioni in ingresso dei dispositivi gemelli come segue:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Elencare tutte le proprietà e le relazioni dei dispositivi gemelli

Usando i metodi precedenti per elencare le relazioni in uscita e in ingresso in un dispositivo gemello, è possibile creare un metodo che stampa le informazioni complete sui dispositivi gemelli, incluse le proprietà del gemello e entrambi i tipi di relazione. Di seguito è riportato un esempio di metodo personalizzato che Mostra come combinare i metodi personalizzati sopra indicati a questo scopo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

È ora possibile chiamare questa funzione personalizzata come la seguente: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Aggiorna relazioni

Le relazioni vengono aggiornate usando il `UpdateRelationship` metodo. 

>[!NOTE]
>Questo metodo consente di aggiornare le **Proprietà** di una relazione. Se è necessario modificare il dispositivo gemello di origine o di destinazione della relazione, è necessario [eliminare la relazione](#delete-relationships) e [ricrearne una](#create-relationships) usando i nuovi dispositivi gemelli.

I parametri obbligatori per la chiamata client sono l'ID del dispositivo gemello di origine (il gemello da cui ha origine la relazione), l'ID della relazione da aggiornare e un documento di [patch JSON](http://jsonpatch.com/) che contiene le proprietà e i nuovi valori che si vuole aggiornare.

Ecco il codice di esempio che illustra come usare questo metodo. Questo esempio usa la chiamata SDK (evidenziata) all'interno di un metodo personalizzato che può essere visualizzato nel contesto di un programma più grande.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Di seguito è riportato un esempio di una chiamata a questo metodo personalizzato, passando un documento di patch JSON con le informazioni per aggiornare una proprietà.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Eliminare relazioni

Il primo parametro specifica il gemello di origine, ovvero il gemello da cui ha origine la relazione. L'altro parametro è l'ID della relazione. Sono necessari sia l'ID gemello che l'ID relazione, perché gli ID relazione sono univoci solo all'interno dell'ambito di un dispositivo gemello.

Ecco il codice di esempio che illustra come usare questo metodo. Questo esempio usa la chiamata SDK (evidenziata) all'interno di un metodo personalizzato che può essere visualizzato nel contesto di un programma più grande.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

È ora possibile chiamare questo metodo personalizzato per eliminare una relazione come la seguente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Esempio di grafico a gemello eseguibile

Il frammento di codice eseguibile seguente usa le operazioni relative alle relazioni di questo articolo per creare un grafico gemello da gemelli digitali e relazioni.

### <a name="set-up-the-runnable-sample"></a>Configurare l'esempio eseguibile

Il frammento di codice usa il [*Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) e [*Floor.jsnelle*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definizioni di modello da [*esercitazione: esplorare i dispositivi gemelli digitali di Azure con un'app client di esempio*](tutorial-command-line-app.md). È possibile usare questi collegamenti per passare direttamente ai file o scaricarli come parte del progetto di esempio end-to-end completo [qui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Prima di eseguire l'esempio, eseguire le operazioni seguenti:
1. Scaricare i file del modello, inserirli nel progetto e sostituire i `<path-to>` segnaposto nel codice riportato di seguito per indicare al programma dove trovarli.
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

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Ecco l'output della console del programma precedente: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Output della console che mostra i dettagli del dispositivo gemello, le relazioni in ingresso e in uscita dei dispositivi gemelli." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Il grafo gemello è un concetto di creazione di relazioni tra due gemelli. Se si vuole visualizzare la rappresentazione visiva del grafo gemello, vedere la sezione [*visualizzazione*](how-to-manage-graph.md#visualization) di questo articolo. 

## <a name="create-graph-from-a-csv-file"></a>Creare un grafico da un file CSV

Nei casi d'uso pratici, le gerarchie gemelle vengono spesso create dai dati archiviati in un database diverso o eventualmente in un foglio di calcolo o in un file CSV. In questa sezione viene illustrato come leggere i dati da un file CSV e creare un grafico gemello.

Si consideri la tabella dati seguente, che descrive un set di gemelli e relazioni digitali.

|  ID modello    | ID gemello (deve essere univoco) | Nome relazione  | ID gemello di destinazione  | Dati init gemelli |
| --- | --- | --- | --- | --- |
| dtmi: esempio: Floor; 1    | Floor1 | contains | Room1 | |
| dtmi: esempio: Floor; 1    | Floor0 | contains | Room0 | |
| dtmi: esempio: Room; 1    | Room1 | | | {"Temperature": 80} |
| dtmi: esempio: Room; 1    | Room0 | | | {"Temperature": 70} |

Un modo per ottenere questi dati nei dispositivi gemelli digitali di Azure consiste nel convertire la tabella in un file CSV e scrivere codice per interpretare il file nei comandi per creare i gemelli e le relazioni. Nell'esempio di codice seguente viene illustrata la lettura dei dati dal file CSV e la creazione di un grafico a gemelli nei dispositivi gemelli digitali di Azure.

Nel codice riportato di seguito, il file CSV è denominato *data.csv* ed è presente un segnaposto che rappresenta il **nome host** dell'istanza di Azure Digital gemelli. Nell'esempio vengono inoltre utilizzati diversi pacchetti che è possibile aggiungere al progetto per agevolare il processo.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'esecuzione di query su un grafo gemelli di Azure Digital gemello:
* [*Concetti: linguaggio di query*](concepts-query-language.md)
* [*Procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md)