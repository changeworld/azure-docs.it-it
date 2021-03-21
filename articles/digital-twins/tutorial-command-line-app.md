---
title: 'Esercitazione: creare un grafico in dispositivi gemelli digitali di Azure (app client)'
titleSuffix: Azure Digital Twins
description: Esercitazione per la creazione di uno scenario di Azure Digital gemelli usando un'applicazione della riga di comando di esempio
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493706"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Esercitazione: creare un grafo di gemelli digitali di Azure usando un'app client di esempio

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

In questa esercitazione verrà compilato un grafico in dispositivi gemelli digitali di Azure usando modelli, gemelli e relazioni. Lo strumento per questa esercitazione è l' **applicazione client da riga di comando di esempio** per l'interazione con un'istanza di dispositivi gemelli digitali di Azure. L'app client è simile a quella scritta in [*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md).

È possibile usare questo esempio per eseguire azioni essenziali di Gemelli digitali di Azure, ad esempio il caricamento di modelli, la creazione e la modifica di gemelli e la creazione di relazioni. È anche possibile esaminare il [codice dell'esempio](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) per ottenere informazioni sulle API dei dispositivi gemelli digitali di Azure e provare a implementare i propri comandi modificando il progetto di esempio, tuttavia si desidera.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Modellare un ambiente
> * Creare i gemelli digitali
> * Aggiungere relazioni per formare un grafo
> * Eseguire una query sul grafo per rispondere alle domande

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Eseguire il progetto di esempio

Ora che l'app e l'autenticazione sono configurate, eseguire il progetto con questo pulsante sulla barra degli strumenti:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Screenshot del pulsante di avvio di Visual Studio (progetto SampleClientApp)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Viene aperta una finestra della console, in cui viene eseguita l'autenticazione e si attende un comando. 
* L'autenticazione viene gestita tramite il browser. Il Web browser predefinito verrà aperto con una richiesta di autenticazione. Usare la richiesta per accedere con le credenziali di Azure. È quindi possibile chiudere la scheda o la finestra del browser.

Ecco uno screenshot della console del progetto:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Screenshot del messaggio di benvenuto dall'app della riga di comando." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Per un elenco di tutti i comandi che è possibile usare con questo progetto, immettere `help` nella console del progetto e premere INVIO.

Mantenere in esecuzione la console del progetto per i passaggi rimanenti dell'esercitazione.

## <a name="model-a-physical-environment-with-dtdl"></a>Modellare un ambiente fisico con DTDL

Ora che l'istanza di Azure Digital Twins e l'app di esempio sono configurati, è possibile iniziare a creare un grafico di uno scenario. 

Il primo passaggio per la creazione di una soluzione di Gemelli digitali di Azure consiste nel definire i [**modelli**](concepts-models.md) gemelli per l'ambiente. 

I modelli sono simili alle classi nei linguaggi di programmazione orientati a oggetti. Forniscono modelli definiti dall'utente per i [gemelli digitali](concepts-twins-graph.md) da seguire e di cui creare istanze in seguito. Sono scritti in un linguaggio simile a JSON, **DTDL (Digital Twin Definition Language)** , e consentono di definire le *proprietà*, i *dati di telemetria*, le *relazioni* e i *componenti* di un gemello.

> [!NOTE]
> DTDL consente anche di definire *comandi* nei gemelli digitali. Tuttavia, i comandi non sono attualmente supportati nel servizio Gemelli digitali di Azure.

Nella finestra di Visual Studio in cui è aperto il progetto _**AdtE2ESample**_ , usare il riquadro *Esplora soluzioni* per passare alla *cartella AdtSampleApp\SampleClientApp\Models*. Questa cartella contiene modelli di esempio.

Selezionare *Room.json* per aprirlo nella finestra di modifica e cambiarlo nei modi seguenti:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Caricare modelli in Gemelli digitali di Azure

Dopo aver progettato i modelli, è necessario caricarli nell'istanza di Gemelli digitali di Azure. Questa operazione configura l'istanza del servizio Gemelli digitali di Azure con il vocabolario del dominio personalizzato. Una volta caricati i modelli, è possibile creare le istanze di gemelli che li usano.

1. Nella finestra della console del progetto eseguire il comando seguente per caricare il modello *Room* aggiornato, nonché un modello *Floor* che verrà usato anche nella sezione successiva per creare tipi diversi di gemelli.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    L'output dovrebbe indicare che i modelli sono stati creati correttamente.

1. Verificare se i modelli sono stati creati eseguendo il comando `GetModels true`. Viene eseguita una query sull'istanza di Gemelli digitali di Azure per recuperare tutti i modelli caricati e verranno stampate le relative informazioni complete. Cercare il modello *Room* modificato nei risultati:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Screenshot del risultato da getmodes, che mostra il modello di chat room aggiornato." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Errors

L'applicazione di esempio gestisce anche gli errori del servizio. 

Eseguire di nuovo il comando `CreateModels` per provare a ricaricare una seconda volta uno degli stessi modelli appena caricati:

```cmd/sh
CreateModels Room
```

Poiché i modelli non possono essere sovrascritti, ora viene restituito un errore del servizio.
Per informazioni dettagliate su come eliminare i modelli esistenti, vedere [*procedura: gestire i modelli di DTDL*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Creare i gemelli digitali

Ora che alcuni modelli sono stati caricati nell'istanza di Gemelli digitali di Azure, è possibile creare i [**gemelli digitali**](concepts-twins-graph.md) in base alle definizioni dei modelli. I gemelli digitali rappresentano le entità all'interno dell'ambiente aziendale, ad esempio i sensori di un'azienda agricola, le stanze di un edificio o i fari di un'auto. 

Per creare un gemello digitale, usare il comando `CreateDigitalTwin`. È necessario fare riferimento al modello su cui si basa il gemello e facoltativamente definire i valori iniziali per tutte le proprietà nel modello. In questa fase non è necessario passare informazioni sulle relazioni.

1. Eseguire questo codice nella console del progetto in esecuzione per creare diversi gemelli, in base al modello *Room* aggiornato in precedenza e a un altro modello, *Floor*. Tenere presente che *Room* include tre proprietà, quindi è possibile specificare argomenti con i valori iniziali per tali proprietà. L'inizializzazione dei valori delle proprietà è facoltativa in generale, ma sono necessari per questa esercitazione.

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    L'output di questi comandi dovrebbe indicare che i gemelli sono stati creati correttamente. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Screenshot che mostra un estratto dal risultato dei comandi CreateDigitalTwin, che include floor0, floor1, room0 e room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. È possibile verificare che i gemelli siano stati creati eseguendo il `Query` comando. Questo comando esegue una query nell'istanza di Gemelli digitali di Azure per recuperare tutti i gemelli digitali che contiene. Cercare i *room0*, *Room1*, *floor0* e *floor1* gemelli nei risultati.

### <a name="modify-a-digital-twin"></a>Modificare un gemello digitale

È anche possibile modificare le proprietà di un gemello creato. 

> [!NOTE]
> L'API REST sottostante usa il formato di [patch JSON](http://jsonpatch.com/) per definire gli aggiornamenti in un dispositivo gemello. L'app da riga di comando usa anche questo formato per offrire un'esperienza effettiva con le aspettative delle API sottostanti.

1. Eseguire questo comando per modificare *Room0* roomname da *room0* a *PresidentialSuite*:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    L'output dovrebbe indicare che il gemello è stato aggiornato correttamente.

1. È possibile verificare che l'aggiornamento sia stato eseguito correttamente eseguendo questo comando per visualizzare le informazioni di *room0*:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    L'output dovrebbe riflettere il nome aggiornato.


## <a name="create-a-graph-by-adding-relationships"></a>Creare un grafo con l'aggiunta di relazioni

Successivamente, è possibile creare **relazioni** tra questi gemelli, per connetterli in un [**grafo dei gemelli**](concepts-twins-graph.md). I grafi dei gemelli vengono usati per rappresentare l'intero ambiente. 

I tipi di relazioni che è possibile creare da un gemello a un altro vengono definiti all'interno dei [modelli](#model-a-physical-environment-with-dtdl) caricati in precedenza. La [definizione del modello per *floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) specifica che i piani possono avere un tipo di relazione denominato *Contains*. In questo modo è possibile creare una relazione di tipo *Contains* da ogni singolo *piano* alla stanza corrispondente che contiene.

Per aggiungere una relazione, usare il comando `CreateRelationship`. Specificare il gemello da cui provengono la relazione, il tipo di relazione e il gemello a cui si connette la relazione. Infine, assegnare un ID univoco alla relazione.

1. Eseguire il codice seguente per aggiungere una relazione "contains" da ogni gemello *Floor* creato in precedenza a un gemello *Room* corrispondente. Le relazioni sono denominate *relationship0* e *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >La relazione *Contains* nel [modello *floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) è stata definita anche con due proprietà di stringa `ownershipUser` e `ownershipDepartment` , pertanto, quando si creano le relazioni, è possibile fornire anche argomenti con i valori iniziali.
    > Ecco una versione alternativa del comando precedente per creare *relationship0* che specifichi anche i valori iniziali per queste proprietà:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    L'output di questi comandi conferma che le relazioni sono state create correttamente:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Screenshot di un estratto dal risultato dei comandi CreateRelationship, che include relationship0 e relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. È possibile verificare le relazioni con uno dei comandi seguenti, che eseguono query sulle relazioni nell'istanza di Azure Digital gemelli.
    * Per visualizzare tutte le relazioni provenienti da ogni piano (visualizzando le relazioni da un lato):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Per visualizzare tutte le relazioni in arrivo in ogni stanza (visualizzando la relazione dal lato "altro"):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Per cercare queste relazioni singolarmente, in base all'ID:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

I gemelli e le relazioni configurati in questa esercitazione formano il grafo concettuale seguente:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagramma che mostra un grafo concettuale. floor0 è connesso tramite relationship0 a room0 e floor1 è connesso tramite relationship1 a Room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Eseguire una query sul grafo dei gemelli per rispondere alla domande sull'ambiente

Una delle principali funzionalità di Gemelli digitali di Azure è la possibilità di eseguire [query](concepts-query-language.md) sul grafo dei gemelli in modo semplice ed efficiente per trovare le risposte alle domande sull'ambiente. 

Eseguire i comandi seguenti nella console del progetto in esecuzione per rispondere ad alcune domande sull'ambiente di esempio.

1. **Quali sono tutte le entità dell'ambiente rappresentate nei dispositivi gemelli digitali di Azure?** (query su tutto)

    ```cmd/sh
    Query
    ```

    In questo modo è possibile ottenere una panoramica dell'ambiente e assicurarsi che tutti gli elementi siano rappresentati come previsto all'interno di Gemelli digitali di Azure. Il risultato è un output contenente ogni gemello digitale con i relativi dettagli. Ecco un estratto:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Screenshot che mostra un risultato parziale dalla query dei dispositivi gemelli, inclusi room0 e floor1.":::

    >[!NOTE]
    >Nel progetto di esempio il comando `Query` senza argomenti aggiuntivi è l'equivalente di `Query SELECT * FROM DIGITALTWINS`. Per eseguire query su tutti i gemelli nell'istanza con le [API Query](/rest/api/digital-twins/dataplane/query) o i [comandi dell'interfaccia della riga di comando](how-to-use-cli.md), usare la query più lunga (completa).

1. **Quali sono tutte le stanze dell'ambiente?** (query per modello)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    È possibile limitare la query ai gemelli di un determinato tipo, per ottenere informazioni più specifiche su quello che rappresentano. Il risultato di questa operazione mostra *room0* e *room1*, ma **non** mostra *floor0* o *floor1* (perché si tratta di piani, non di stanze).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Screenshot del risultato della query del modello che mostra solo room0 e room1.":::

1. **Quali sono tutte le stanze in *floor0*?** (query per relazione)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    È possibile eseguire query in base alle relazioni nel grafo, per ottenere informazioni sul modo in cui i gemelli sono connessi o limitare la query a una determinata area. In *floor0* è presente solo *room0*, che quindi è l'unica stanza nel risultato.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Screenshot del risultato della query Relationship, che mostra room0.":::

1. **Quali sono gli altri gemelli dell'ambiente con una temperatura superiore a 75?** (query per proprietà)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    È possibile eseguire una query sul grafo in base alle proprietà per rispondere a una serie di domande, anche per trovare outlier nell'ambiente che potrebbero richiedere attenzione. Sono supportati anche altri operatori di confronto, ossia *<* , *>* , *=* o *!=* . I risultati mostrano *room1*, perché ha una temperatura pari a 80.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Screenshot del risultato della query della proprietà, che mostra solo Room1.":::

1. **Quali sono tutte le stanze in *floor0* con una temperatura superiore a 75?** (query composta)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    È anche possibile combinare le query precedenti come in SQL, usando operatori di combinazione come `AND`, `OR`, `NOT`. Questa query usa `AND` per rendere più specifica la query precedente sulle temperature dei gemelli. Il risultato include ora solo le stanze con temperature superiori a 75 che si trovano in *floor0*, ovvero in questo caso nessuna stanza. Il set di risultati è vuoto.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Screenshot del risultato della query composta che non mostra alcun risultato." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, è possibile scegliere le risorse che si desidera rimuovere, a seconda di ciò che si desidera eseguire successivamente.

* **Se si prevede di continuare con l'esercitazione successiva**, è possibile mantenere le risorse impostate qui per continuare a usare questa istanza di Azure Digital Twins e l'app di esempio configurata per l'esercitazione successiva.

* **Se si vuole continuare a usare l'istanza di Azure Digital gemelli, ma si cancellano tutti i modelli, i gemelli e le relazioni**, è possibile usare i `DeleteAllTwins` comandi e dell'app `DeleteAllModels` di esempio per cancellare rispettivamente i gemelli e i modelli nell'istanza.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Potrebbe anche essere necessario eliminare la cartella del progetto dal computer locale.

## <a name="next-steps"></a>Passaggi successivi 

Questa esercitazione è stata avviata con i dispositivi gemelli digitali di Azure creando un grafo nell'istanza usando un'applicazione client di esempio. Sono stati creati modelli, gemelli digitali e relazioni per formare un grafo. Sono state anche eseguite alcune query sul grafo per avere un'idea dei tipi di domande a cui i gemelli digitali di Azure possono rispondere su un ambiente.

Passare all'esercitazione successiva per combinare i dispositivi gemelli digitali di Azure con altri servizi di Azure per completare uno scenario end-to-end basato sui dati:
> [!div class="nextstepaction"]
> [*Esercitazione: Connettere una soluzione end-to-end*](tutorial-end-to-end.md)