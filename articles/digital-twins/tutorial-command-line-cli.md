---
title: 'Esercitazione: creare un grafico in dispositivi gemelli digitali di Azure (CLI)'
titleSuffix: Azure Digital Twins
description: Esercitazione per creare uno scenario di dispositivi gemelli digitali di Azure con l'interfaccia della riga di comando
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 578befe3e26ebb42fa2172976e07d0a5836e3743
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107143"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Esercitazione: creare un grafico gemelli di Azure Digital con l'interfaccia della riga di comando di Azure

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

In questa esercitazione verrà compilato un grafico in dispositivi gemelli digitali di Azure usando modelli, gemelli e relazioni. Lo strumento per questa esercitazione è il [set di comandi di Azure Digital gemelli per l'interfaccia della riga di comando di **Azure**](how-to-use-cli.md). 

È possibile usare i comandi dell'interfaccia della riga di comando per eseguire azioni essenziali di Azure Digital Twins, ad esempio il caricamento di modelli, la creazione e la modifica di gemelli e la creazione di relazioni. Per visualizzare il set completo di comandi dell'interfaccia della riga di comando, è anche possibile consultare la [documentazione di riferimento per *AZ DT* Command Set](/cli/azure/dt) .

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Modellare un ambiente
> * Creare i gemelli digitali
> * Aggiungere relazioni per formare un grafo
> * Eseguire una query sul grafo per rispondere alle domande

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi di questa esercitazione, è necessario prima completare i prerequisiti seguenti.

Se non si ha una sottoscrizione di Azure, **creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** prima di iniziare.

### <a name="download-the-sample-models"></a>Scaricare i modelli di esempio

L'esercitazione usa due modelli pre-scritti che fanno parte del progetto di [esempio end-to-end](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) C# per i dispositivi gemelli digitali di Azure. I file del modello sono disponibili qui: 
* [*Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Per ottenere i file nel computer, usare i collegamenti di navigazione precedenti e copiare i corpi dei file in file locali nel computer con gli stessi nomi (*Room.jsin* e *Floor.js*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Configurare una sessione di Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparare un'istanza di Gemelli digitali di Azure

Per usare Gemelli digitali di Azure come descritto in questo articolo, è necessario prima **configurare un'istanza di Gemelli digitali di Azure** e le autorizzazioni necessarie. Se in precedenza è stata già configurata un'istanza di Gemelli digitali di Azure, è possibile usare tale istanza.

In caso contrario, seguire le istruzioni descritte in [*Procedura: Configurare un'istanza e l'autenticazione*](how-to-set-up-instance-cli.md). Le istruzioni contengono anche le operazioni da eseguire per verificare che ogni passaggio sia stato completato correttamente e sia quindi possibile passare all'uso della nuova istanza.

Dopo aver configurato l'istanza di Gemelli digitali di Azure, prendere nota dei valori seguenti che saranno necessari per connettersi all'istanza in un secondo momento:
* **_nome host_ dell'istanza**
* **sottoscrizione di Azure** usata per creare l'istanza. 

È possibile ottenere entrambi i valori per l'istanza nell'output del comando dell'interfaccia della riga di comando di Azure seguente: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Screenshot della finestra Cloud Shell browser che mostra l'output del comando AZ DT show. Il campo nome host e l'ID sottoscrizione (parte del campo ID) sono evidenziati.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modellare un ambiente fisico con DTDL

Ora che l'interfaccia della riga di comando e l'istanza di Azure Digital Twins sono configurati, è possibile iniziare a creare un grafico di uno scenario. 

Il primo passaggio per la creazione di una soluzione di Gemelli digitali di Azure consiste nel definire i [**modelli**](concepts-models.md) gemelli per l'ambiente. 

I modelli sono simili alle classi nei linguaggi di programmazione orientati a oggetti. Forniscono modelli definiti dall'utente per i [gemelli digitali](concepts-twins-graph.md) da seguire e di cui creare istanze in seguito. Sono scritti in un linguaggio simile a JSON, **DTDL (Digital Twin Definition Language)** , e consentono di definire le *proprietà*, i *dati di telemetria*, le *relazioni* e i *componenti* di un gemello.

> [!NOTE]
> DTDL consente anche di definire *comandi* nei gemelli digitali. Tuttavia, i comandi non sono attualmente supportati nel servizio Gemelli digitali di Azure.

Passare al computer *Room.js* nel file creato nella sezione dei [prerequisiti](#prerequisites) . Aprirlo in un editor di codice e modificarlo nei modi seguenti:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Caricare modelli in Gemelli digitali di Azure

Dopo aver progettato i modelli, è necessario caricarli nell'istanza di Gemelli digitali di Azure. Questa operazione configura l'istanza del servizio Gemelli digitali di Azure con il vocabolario del dominio personalizzato. Una volta caricati i modelli, è possibile creare le istanze di gemelli che li usano.

1. Per aggiungere modelli con Cloud Shell, è necessario caricare i file del modello nella risorsa di archiviazione di Cloud Shell in modo che i file siano disponibili quando si esegue il comando Cloud Shell che li utilizza. A tale scopo, selezionare l'icona "Carica/Scarica file" e scegliere "carica".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Screenshot della finestra del browser Cloud Shell che mostra la selezione dell'icona di caricamento.":::
    
    Passare al *Room.js* nel file nel computer e selezionare "Apri". Ripetere quindi questo passaggio per *Floor.jsil*.

1. Usare quindi il comando [**AZ DT Model create**](/cli/azure/dt/model#az_dt_model_create) come illustrato di seguito per caricare il modello di *chat room* aggiornato nell'istanza di Azure Digital gemelli. Il secondo comando carica un altro modello, *floor*, che verrà usato anche nella sezione successiva per creare tipi diversi di dispositivi gemelli.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    L'output di ogni comando visualizzerà le informazioni sul modello caricato correttamente.

    >[!TIP]
    >È anche possibile caricare contemporaneamente tutti i modelli all'interno di una directory usando l' `--from-directory` opzione per il comando Model create. Per altre informazioni, vedere [parametri facoltativi per *AZ DT Model create*](/cli/azure/dt/model#az_dt_model_create-optional-parameters).

1. Verificare che i modelli siano stati creati con il comando [**AZ DT model list**](/cli/azure/dt/model#az_dt_model_list) , come illustrato di seguito. Verrà stampato un elenco di tutti i modelli che sono stati caricati nell'istanza di Azure Digital Twins con le informazioni complete. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Cercare il modello *Room* modificato nei risultati:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Screenshot di Cloud Shell che mostra il risultato del comando model list, che include il modello room aggiornato." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Errors

L'interfaccia della riga di comando gestisce anche gli errori del servizio. 

Eseguire di nuovo il comando `az dt model create` per provare a ricaricare una seconda volta uno degli stessi modelli appena caricati:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Poiché i modelli non possono essere sovrascritti, ora verrà restituito un codice di errore `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Creare i gemelli digitali

Ora che alcuni modelli sono stati caricati nell'istanza di Gemelli digitali di Azure, è possibile creare i [**gemelli digitali**](concepts-twins-graph.md) in base alle definizioni dei modelli. I gemelli digitali rappresentano le entità all'interno dell'ambiente aziendale, ad esempio i sensori di un'azienda agricola, le stanze di un edificio o i fari di un'auto. 

Per creare un dispositivo gemello digitale, usare il comando [**AZ DT gemelle create**](/cli/azure/dt/twin#az_dt_twin_create) . È necessario fare riferimento al modello su cui si basa il gemello e facoltativamente definire i valori iniziali per tutte le proprietà nel modello. In questa fase non è necessario passare informazioni sulle relazioni.

1. Eseguire questo codice nel Cloud Shell per creare più dispositivi gemelli, in base al modello di *chat room* aggiornato in precedenza e a un altro modello *floor*. Tenere presente che *Room* include tre proprietà, quindi è possibile specificare argomenti con i valori iniziali per tali proprietà. L'inizializzazione dei valori delle proprietà è facoltativa in generale, ma sono necessari per questa esercitazione.

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Se si usa Cloud Shell nell'ambiente PowerShell, potrebbe essere necessario eseguire l'escape dei caratteri di virgolette per consentire l' `--properties` analisi corretta del valore JSON. Con questa modifica, i comandi per creare i gemelli della chat sono simili ai seguenti:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Questa operazione è riportata nello screenshot seguente.
    
    L'output di ogni comando visualizzerà informazioni sul dispositivo gemello creato correttamente, incluse le proprietà per i dispositivi gemelli che sono stati inizializzati con loro.

1. È possibile verificare che i gemelli siano stati creati con il comando [**AZ DT Twin query**](/cli/azure/dt/twin#az_dt_twin_query) come illustrato di seguito. La query mostrata trova tutti i dispositivi gemelli digitali nell'istanza di Azure Digital gemelli.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Cercare i *room0*, *Room1*, *floor0* e *floor1* gemelli nei risultati. Di seguito è riportato un estratto che mostra parte del risultato della query.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Screenshot di Cloud Shell che mostra il risultato parziale della query gemella, inclusi room0 e room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Modificare un gemello digitale

È anche possibile modificare le proprietà di un gemello creato. 

1. Eseguire questo comando [**AZ DT Twin Update**](/cli/azure/dt/twin#az_dt_twin_update) per modificare *room0* roomname da *room0* a *PresidentialSuite*:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Se si usa Cloud Shell nell'ambiente PowerShell, potrebbe essere necessario eseguire l'escape dei caratteri di virgolette per consentire l' `--json-patch` analisi corretta del valore JSON. Con questa modifica, il comando per aggiornare il gemello ha un aspetto simile al seguente:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Questa operazione è riportata nello screenshot seguente.
    
    L'output di questo comando visualizzerà le informazioni correnti del dispositivo gemello e dovrebbe essere visualizzato il nuovo valore per `RoomName` nel risultato.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Screenshot di Cloud Shell che mostra il risultato del comando Update, che include un Roomname di PresidentialSuite." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. È possibile verificare che l'aggiornamento sia stato eseguito correttamente eseguendo il comando [**AZ DT Twin Show**](/cli/azure/dt/twin#az_dt_twin_show) per visualizzare le informazioni di *room0*:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    L'output dovrebbe riflettere il nome aggiornato.

## <a name="create-a-graph-by-adding-relationships"></a>Creare un grafo con l'aggiunta di relazioni

Successivamente, è possibile creare **relazioni** tra questi gemelli, per connetterli in un [**grafo dei gemelli**](concepts-twins-graph.md). I grafi dei gemelli vengono usati per rappresentare l'intero ambiente. 

I tipi di relazioni che è possibile creare da un gemello a un altro vengono definiti all'interno dei [modelli](#model-a-physical-environment-with-dtdl) caricati in precedenza. La [definizione del modello per *floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) specifica che i piani possono avere un tipo di relazione denominato *Contains*. In questo modo è possibile creare una relazione di tipo *Contains* da ogni singolo *piano* alla stanza corrispondente che contiene.

Per aggiungere una relazione, usare il comando [**AZ DT Twin Relationship create**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_create) . Specificare il gemello da cui provengono la relazione, il tipo di relazione e il gemello a cui si connette la relazione. Infine, assegnare un ID univoco alla relazione. Se è stata definita una relazione con proprietà, è possibile inizializzare le proprietà della relazione anche in questo comando.

1. Eseguire il codice seguente per aggiungere una relazione di tipo *Contains* da ognuno *dei gemelli* di *piano* creato in precedenza nel dispositivo gemello corrispondente. Le relazioni sono denominate *relationship0* e *relationship1*.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >La relazione *Contains* nel [modello *floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) è stata definita anche con due proprietà, `ownershipUser` e `ownershipDepartment` , pertanto è anche possibile fornire argomenti con i valori iniziali per questi valori quando si creano le relazioni.
    > Per creare una relazione con queste proprietà inizializzate, aggiungere l' `--properties` opzione a uno dei comandi precedenti, come indicato di seguito:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Se si usa Cloud Shell nell'ambiente PowerShell, potrebbe essere necessario eseguire l'escape dei caratteri di virgolette per consentire l' `--properties` analisi corretta del valore JSON.
    
    L'output di ogni comando visualizzerà informazioni sulla relazione creata correttamente.

1. È possibile verificare le relazioni con uno dei comandi seguenti, che eseguono query sulle relazioni nell'istanza di Azure Digital gemelli.
    * Per visualizzare tutte le relazioni provenienti da ogni piano (visualizzando le relazioni da un lato):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Per visualizzare tutte le relazioni in arrivo in ogni stanza (visualizzando la relazione dal lato "altro"):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Per cercare queste relazioni singolarmente, in base all'ID:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

I gemelli e le relazioni configurati in questa esercitazione formano il grafo concettuale seguente:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagramma che mostra un grafo concettuale. floor0 è connesso tramite relationship0 a room0 e floor1 è connesso tramite relationship1 a Room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Eseguire una query sul grafo dei gemelli per rispondere alla domande sull'ambiente

Una delle principali funzionalità di Gemelli digitali di Azure è la possibilità di eseguire [query](concepts-query-language.md) sul grafo dei gemelli in modo semplice ed efficiente per trovare le risposte alle domande sull'ambiente. Nell'interfaccia della riga di comando di Azure questa operazione viene eseguita con il comando [**AZ DT Twin query**](/cli/azure/dt/twin#az_dt_twin_query) .

Eseguire le query seguenti nell'Cloud Shell per rispondere ad alcune domande sull'ambiente di esempio.

1. **Quali sono tutte le entità dell'ambiente rappresentate nei dispositivi gemelli digitali di Azure?** (query su tutto)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    In questo modo è possibile ottenere una panoramica dell'ambiente e assicurarsi che tutti gli elementi siano rappresentati come previsto all'interno di Gemelli digitali di Azure. Il risultato è un output contenente ogni gemello digitale con i relativi dettagli. Ecco un estratto:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Screenshot di Cloud Shell che mostra il risultato parziale della query gemella, inclusi room0 e room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >È possibile riconoscere che questo è lo stesso comando usato nella sezione creare i dispositivi [*digitali gemelli*](#create-digital-twins) in precedenza per trovare tutti i dispositivi gemelli digitali di Azure nell'istanza.

1. **Quali sono tutte le stanze dell'ambiente?** (query per modello)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    È possibile limitare la query ai gemelli di un determinato tipo, per ottenere informazioni più specifiche su quello che rappresentano. Il risultato di questa operazione mostra *room0* e *room1*, ma **non** mostra *floor0* o *floor1* (perché si tratta di piani, non di stanze).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Screenshot del Cloud Shell che mostra il risultato della query del modello, che include solo room0 e room1." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Quali sono tutte le stanze in *floor0*?** (query per relazione)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    È possibile eseguire query in base alle relazioni nel grafo, per ottenere informazioni sul modo in cui i gemelli sono connessi o limitare la query a una determinata area. In *floor0* è presente solo *room0*, che quindi è l'unica stanza nel risultato.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Screenshot del Cloud Shell che mostra il risultato della query di relazione, che include room0." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Si noti che viene eseguita una query sull'ID di un gemello, ad esempio *floor0* nella query precedente, usando il campo dei metadati `$dtId` . 
    >
    >Quando si usa Cloud Shell per eseguire una query con campi di metadati come questo che iniziano con `$` , è necessario eseguire l'escape dell'oggetto `$` con un apice inverso per consentire cloud Shell la conoscenza non è una variabile e deve essere usata come valore letterale nel testo della query. Questa operazione è riportata nello screenshot precedente.

1. **Quali sono gli altri gemelli dell'ambiente con una temperatura superiore a 75?** (query per proprietà)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    È possibile eseguire una query sul grafo in base alle proprietà per rispondere a una serie di domande, anche per trovare outlier nell'ambiente che potrebbero richiedere attenzione. Sono supportati anche altri operatori di confronto, ossia *<* , *>* , *=* o *!=* . I risultati mostrano *room1*, perché ha una temperatura pari a 80.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Screenshot di Cloud Shell che mostra il risultato della query di proprietà, che include solo Room1." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Quali sono tutte le stanze in *floor0* con una temperatura superiore a 75?** (query composta)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    È anche possibile combinare le query precedenti come in SQL, usando operatori di combinazione come `AND`, `OR`, `NOT`. Questa query usa `AND` per rendere più specifica la query precedente sulle temperature dei gemelli. Il risultato include ora solo le stanze con temperature superiori a 75 che si trovano in *floor0*, ovvero in questo caso nessuna stanza. Il set di risultati è vuoto.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Screenshot di Cloud Shell che mostra il risultato di una query composta, che non include alcun elemento." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, è possibile scegliere le risorse che si desidera rimuovere, a seconda di ciò che si desidera eseguire successivamente.

* **Se si prevede di continuare con l'esercitazione successiva**, è possibile mantenere le risorse impostate qui e riusare l'istanza di Azure Digital Twins senza cancellare alcun elemento.

* **Se si vuole continuare a usare l'istanza di Azure Digital gemelli, ma si cancellano tutti i modelli, i gemelli e le relazioni**, è possibile usare i comandi [**AZ DT Twin Relationship**](/cli/azure/dt/twin/relationship#az_dt_twin_relationship_delete)Delete, [**AZ DT gemell Delete**](/cli/azure/dt/twin#az_dt_twin_delete)e [**AZ DT Model Delete**](/cli/azure/dt/model#az_dt_model_delete) per cancellare rispettivamente le relazioni, i dispositivi gemelli e i modelli nell'istanza.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Potrebbe anche essere necessario eliminare i file di modello creati nel computer locale.

## <a name="next-steps"></a>Passaggi successivi 

Questa esercitazione è stata avviata con i dispositivi gemelli digitali di Azure creando un grafo nell'istanza usando l'interfaccia della riga di comando di Azure. Sono stati creati modelli, gemelli digitali e relazioni per formare un grafo. Sono state anche eseguite alcune query sul grafo per avere un'idea dei tipi di domande a cui i gemelli digitali di Azure possono rispondere su un ambiente.

Passare all'esercitazione successiva per combinare i dispositivi gemelli digitali di Azure con altri servizi di Azure per completare uno scenario end-to-end basato sui dati:
> [!div class="nextstepaction"]
> [*Esercitazione: Connettere una soluzione end-to-end*](tutorial-end-to-end.md)