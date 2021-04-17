---
title: "Esercitazione: Simulare le risposte dell'API in Gestione API - Portale di Azure | Microsoft Docs"
description: Questa esercitazione illustra come usare Gestione API per impostare un criterio in un'API affinché restituisca una risposta fittizia se il back-end non è disponibile per l'invio di risposte reali.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: a7617a36ed800f1765ed7723568a4b612fcb6518
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587596"
---
# <a name="tutorial-mock-api-responses"></a>Esercitazione: Simulare le risposte di un'API

Le API back-end possono essere importate in un'API di Gestione API o essere create e gestite manualmente. I passaggi di questa esercitazione illustrano come usare Gestione API per creare un'API vuota, gestirla manualmente e quindi impostare un criterio in un'API affinché restituisca una risposta fittizia. Questo metodo consente agli sviluppatori di procedere con l'implementazione e il test dell'istanza di Gestione API anche se il back-end non è disponibile per inviare risposte reali. 

La possibilità di simulare le risposte può essere utile in diversi scenari:

+ Quando la facciata dell'API viene progettata prima e l'implementazione del back-end viene eseguita in seguito o quando il back-end viene sviluppato in parallelo.
+ Quando il back-end è temporaneamente non operativo o non è possibile ridimensionarlo.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'API di test 
> * Aggiungere un'operazione all'API di test
> * Abilitare la simulazione della risposta
> * Testare l'API fittizia


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Risposta API fittizia":::

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Comprendere il [concetto di criteri in Gestione API di Azure](api-management-howto-policies.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Creare un'API di test 

I passaggi descritti in questa sezione illustrano come creare un'API vuota senza back-end 


1. Accedere al portale di Azure e passare all'istanza di Gestione API.
1. Selezionare **API** >  **+ Aggiungi API** > **API vuota**.
1. Nella finestra **Crea un'API vuota** selezionare **Completa**.
1. Immettere *Test API* in **Nome visualizzato**.
1. Selezionare **Senza limiti** per **Prodotti**.
1. Assicurarsi che sia selezionata l'opzione **Gestiti** in **Gateway**.
1. Selezionare **Crea**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Creare un'API vuota":::

## <a name="add-an-operation-to-the-test-api"></a>Aggiungere un'operazione all'API di test

Un'API espone una o più operazioni. In questa sezione aggiungere un'operazione all'API vuota creata. La chiamata all'operazione al termine dei passaggi descritti in questa sezione genera un errore. Non verranno visualizzati errori dopo avere completato i passaggi descritti più avanti nella sezione [Abilitare la simulazione della risposta](#enable-response-mocking).

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare l'API creata nel passaggio precedente.
1. Selezionare **+ Aggiungi operazione**.
1. Nella finestra **Front-end** immettere i valori seguenti.

     | Impostazione             | Valore                             | Descrizione                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome visualizzato**    | *Test call*                       | Nome visualizzato nel [portale per sviluppatori](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **URL** (verbo HTTP) | GET                               | Selezionare uno dei verbi HTTP predefiniti.                                                                                                                                         |
    | **URL**             | */test*                           | Percorso URL per l'API.                                                                                                                                                                       |
    | **Descrizione**     |                                   |  Descrizione facoltativa dell'operazione usata per fornire la documentazione necessaria agli sviluppatori che usano questa API nel portale per sviluppatori.                                                    |
    
1. Selezionare la scheda **Risposte**, sotto i campi URL, Nome visualizzato e Descrizione. In questa scheda immettere le impostazioni per definire schemi, esempi, tipi di contenuto e codici di stato della risposta.
1. Selezionare **+ Aggiungi risposta** e selezionare **200 OK** dall'elenco.
1. Sotto l'intestazione **Rappresentazioni** a destra selezionare **+ Aggiungi rappresentazione**.
1. Immettere *application/json* nella casella di ricerca e selezionare il tipo di contenuto **application/json**.
1. Nella casella di testo **Sample** (Esempio) immettere `{ "sampleField" : "test" }`.
1. Selezionare **Salva**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Aggiungere un'operazione API" border="false":::

Sebbene non sia richiesto per questo esempio, è possibile configurare impostazioni aggiuntive per un'operazione API in altre schede, ad esempio:


|Scheda      |Descrizione  |
|---------|---------|
|**Query**     |  Consente di aggiungere parametri di query. Oltre al nome e alla descrizione, è possibile specificare i valori che da assegnare a un parametro di query. Uno dei valori può essere contrassegnato come predefinito (facoltativo).        |
|**Richiesta**     |  Consente di definire schemi, esempi e tipi di contenuto della richiesta.       |

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per iniziare a usare l'interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Per aggiungere un'operazione all'API di test, eseguire il [comando az apim api operation create:](/cli/azure/apim/api/operation#az_apim_api_operation_create)

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Eseguire il [comando az apim api operation list](/cli/azure/apim/api/operation#az_apim_api_operation_list) per visualizzare tutte le operazioni per un'API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Per rimuovere un'operazione, usare [il comando az apim api operation delete.](/cli/azure/apim/api/operation#az_apim_api_operation_delete) Ottenere l'ID operazione dal comando precedente.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Mantenere questa operazione per l'uso nella parte restante di questo articolo.

---

## <a name="enable-response-mocking"></a>Abilitare la simulazione della risposta

1. Selezionare l'API creata nel passaggio [Creare un'API di test](#create-a-test-api).
1. Selezionare l'operazione di test aggiunta.
1. Nella finestra a destra, assicurarsi che la scheda **Progettazione** sia selezionata.
1. Nella finestra **Elaborazione in ingresso** fare clic su **+ Aggiungi criteri**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Aggiungere i criteri di elaborazione" border="false":::

1. Selezionare **Mock responses** (Simula risposte) dalla raccolta.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Riquadro del criterio Mock responses (Simula risposte)" border="false":::

1. Nella casella di testo **API Management response** (Risposta di Gestione API) digitare **200 OK, application/json**. Questa selezione indica che l'API deve restituire l'esempio di risposta definito nella sezione precedente.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Impostare la risposta fittizia":::

1. Selezionare **Salva**.

    > [!TIP]
    > Una barra gialla con il testo **Mocking is enabled** (Il comportamento fittizio è abilitato) indica che le risposte restituite da Gestione API sono simulate dal [criterio fittizio](api-management-advanced-policies.md#mock-response) e non sono prodotte dal back-end.

## <a name="test-the-mocked-api"></a>Testare l'API fittizia

1. Selezionare l'API creata nel passaggio [Creare un'API di test](#create-a-test-api).
1. Selezionare la scheda **Test**.
1. Verificare che l'API **Test call** sia selezionata. Selezionare **Invia** per eseguire una chiamata di test.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testare l'API fittizia":::

1. La **risposta HTTP** visualizza il codice JSON fornito come esempio nella prima sezione dell'esercitazione.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Simulare la risposta HTTP":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'API di test
> * Aggiungere un'operazione all'API di test
> * Abilitare la simulazione della risposta
> * Testare l'API fittizia

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)
