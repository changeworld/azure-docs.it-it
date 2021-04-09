---
title: 'Esercitazione: Importare e gestire API - Gestione API di Azure e Visual Studio Code | Microsoft Docs'
description: Questa esercitazione illustra come usare l'estensione Gestione API di Azure per Visual Studio Code per importare, testare e gestire le API.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0dea8e43d5f09b84c5795bc257cf3331ad919fcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649547"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Esercitazione: Usare l'estensione Gestione API per Visual Studio Code per importare e gestire API

Questa esercitazione illustra come usare l'estensione gestione API per Visual Studio Code per operazioni comuni in gestione API. Usare il noto ambiente Visual Studio Code per importare, aggiornare, testare e gestire le API.

Si apprenderà come:

> [!div class="checklist"]
> * Importare un'API in Gestione API
> * Modificare l'API
> * Applicare i criteri di Gestione API
> * Testare l'API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API nell'estensione Gestione API":::

Per un'introduzione alle altre funzionalità di Gestione API, vedere le esercitazioni su Gestione API con il [portale di Azure](import-and-publish.md).

## <a name="prerequisites"></a>Prerequisiti
- Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md)
- Assicurarsi di aver installato [Visual Studio Code](https://code.visualstudio.com/) e la versione più recente dell' [estensione gestione API di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Creare un'istanza di Gestione API](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importare un'API

Nell'esempio seguente una specifica OpenAPI in formato JSON viene importata in Gestione API. Microsoft fornisce l'API back-end usata in questo esempio e la ospita in Azure all'indirizzo `https://conferenceapi.azurewebsites.net?format=json`.

1. In Visual Studio Code selezionare l'icona di Azure dalla barra attività.
1. Nel riquadro di Esplora risorse espandere l'istanza di Gestione API creata.
1. Fare clic con il pulsante destro del mouse su **API** e selezionare **Import from OpenAPI Link** (Importa da collegamento OpenAPI). 
1. Quando richiesto, immettere i valori seguenti:
    1. Un **collegamento OpenAPI** per il contenuto in formato JSON. Per questo esempio: *https://conferenceapi.azurewebsites.net?format=json* .
    Questo URL è il servizio che implementa l'API di esempio. Gestione API inoltra le richieste a questo indirizzo.
    1. Un **nome di API**, ad esempio *demo-conference-api*, univoca nell'istanza di Gestione API. Questo nome può contenere solo lettere, numeri e trattini. Il primo e l'ultimo carattere devono essere alfabetici o numerici. Questo nome viene usato nel percorso per chiamare l'API.

Dopo l'importazione, l'API viene visualizzata nel riquadro di Esplora risorse e le operazioni disponibili per le API vengono visualizzate nel nodo **Operazioni**.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="API importata nel riquadro di Esplora risorse":::

## <a name="edit-the-api"></a>Modificare l'API

È possibile modificare l'API in Visual Studio Code. Modificare ad esempio la descrizione JSON di Gestione risorse dell'API nella finestra dell'editor per rimuovere il protocollo **http** usato per accedere all'API. Selezionare quindi **File** > **Salva**.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Modificare la descrizione JSON":::

Per modificare il formato OpenAPI, fare clic con il pulsante destro del mouse sul nome dell'API nel riquadro di Esplora risorse e selezionare **Edit OpenAPI** (Modifica OpenAPI). Apportare le modifiche necessarie e quindi selezionare **File** > **Salva**.

## <a name="apply-policies-to-the-api"></a>Applicare criteri all'API 

Gestione API offre [criteri](api-management-policies.md) che è possibile configurare per le API. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale sulla richiesta o la risposta di un'API. I criteri possono essere globali, ovvero applicabili a tutte le API nell'istanza di Gestione API, oppure possono essere limitati a un'API o a un'operazione API specifica.

Questa sezione illustra come applicare alcuni criteri in uscita comuni all'API per trasformare la risposta dell'API. I criteri in questo esempio modificano le intestazioni della risposta e nascondono gli URL back-end originali visualizzati nel corpo della risposta.

1. Nel riquadro di Esplora risorse selezionare **Criteri** nell'API *demo-conference-api* importata. Il file dei criteri verrà aperto nella finestra dell'editor. Questo file consente di configurare i criteri per tutte le operazioni nell'API. 

1. Aggiornare il file con il contenuto seguente nell'elemento `<outbound>`:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * Il primo criterio `set-header` aggiunge un'intestazione della risposta personalizzata a scopo dimostrativo.
    * Il secondo criterio `set-header` elimina l'eventuale intestazione **X-Powered-By** esistente. Questa intestazione può rivelare il framework dell'applicazione usato nel back-end dell'API, di conseguenza spesso gli autori la rimuovono.
    * Il criterio `redirect-content-urls` riscrive (maschera) i collegamenti nel corpo della risposta, in modo che facciano riferimento ai collegamenti equivalenti tramite il gateway di Gestione API.
    
1. Salvare il file. Se richiesto, selezionare **Carica** per caricare il file nel cloud.

## <a name="test-the-api"></a>Testare l'API

### <a name="get-the-subscription-key"></a>Recuperare la chiave di sottoscrizione

Per testare l'API importata e i criteri applicati, è necessaria una chiave di sottoscrizione per l'istanza di Gestione API.

1. Nel riquadro di Esplora risorse fare clic con il pulsante destro del mouse sul nome dell'istanza di Gestione API.
1. Selezionare **Copy Subscription Key** (Copia chiave di sottoscrizione).

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Opzione per copiare la chiave di sottoscrizione":::

### <a name="test-an-api-operation"></a>Testare un'operazione API

1. Nel riquadro di Esplora risorse espandere il nodo **Operazioni** nell'API *demo-conference-api* importata.
1. Selezionare un'operazione, ad esempio *GetSpeakers*.
1. Nella finestra dell'editor, accanto a **Ocp-Apim-Subscription-Key**, sostituire `{{SubscriptionKey}}` con la chiave di sottoscrizione copiata.
1. Selezionare **Invia richiesta**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Inviare la richiesta API da Visual Studio Code":::

Quando la richiesta ha esito positivo, il back-end risponde con **200 OK** e alcuni dati.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="Operazione di test API":::

Notare i dettagli seguenti nella risposta:
* Alla risposta viene aggiunta l'intestazione **Custom**.
* L'intestazione **X-Powered-By** non viene visualizzata nella risposta.
* Gli URL dei back-end API vengono reindirizzati al gateway di Gestione API, in questo caso `https://apim-hello-world.azure-api.net/demo-conference-api`.

### <a name="trace-the-api-operation"></a>Analizzare l'operazione API

Per informazioni dettagliate sull'analisi che consentono di eseguire il debug dell'operazione API, selezionare il collegamento visualizzato accanto a **Ocp-APIM-Trace-Location**. 

Il file JSON in tale percorso contiene informazioni sull'analisi delle operazioni in ingresso, back-end e in uscita, in modo che sia possibile determinare dove si verificano i problemi dopo che è stata effettuata la richiesta.

> [!TIP]
> Quando si testano operazioni API, l'estensione Gestione API consente il [debug dei criteri](api-management-debug-policies.md) facoltativo (disponibile nel livello di servizio per sviluppatori).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessaria, rimuovere l'istanza di Gestione API facendo clic con il pulsante destro del mouse e scegliendo **Apri nel portale** per [eliminare il servizio Gestione API](get-started-create-service-instance.md#clean-up-resources) e il relativo gruppo di risorse.

In alternativa, è possibile selezionare **Elimina Gestione API** per eliminare solo l'istanza di Gestione API. Questa operazione non ne elimina il gruppo di risorse.

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Eliminare l'istanza di Gestione API da VS Code":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state introdotte diverse funzionalità dell'estensione Gestione API per Visual Studio Code che è possibile usare per importare e gestire le API. Si è appreso come:

> [!div class="checklist"]
> * Importare un'API in Gestione API
> * Modificare l'API
> * Applicare i criteri di Gestione API
> * Testare l'API

L'estensione Gestione API offre funzionalità aggiuntive per l'uso delle API. Ad esempio, è possibile usare [criteri di debug](api-management-debug-policies.md) (disponibili nel livello di servizio per sviluppatori) oppure creare e gestire [valori denominati](api-management-howto-properties.md).