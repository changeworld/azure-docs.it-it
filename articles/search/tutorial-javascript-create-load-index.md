---
title: 'Esercitazione su JavaScript: aggiungere la ricerca alle app Web'
titleSuffix: Azure Cognitive Search
description: Creare l'indice e importare i dati CSV nell'indice di ricerca con JavaScript usando NPM SDK @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726913"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2-creare e caricare l'indice di ricerca con JavaScript

Continua a compilare il sito Web abilitato per la ricerca:
* Creazione di una risorsa di ricerca con l'estensione VS Code
* Creazione di un nuovo indice e importazione dei dati con JavaScript mediante lo script di esempio e Azure SDK [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Creare una risorsa Ricerca di Azure 

Creare una nuova risorsa di ricerca con l'estensione [Azure ricerca cognitiva](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) per Visual Studio Code.

1. In Visual Studio Code aprire la [barra attività](https://code.visualstudio.com/docs/getstarted/userinterface)e selezionare l'icona Azure. 

1. Nella barra laterale, **fare clic con il pulsante destro del mouse sulla sottoscrizione di Azure** nell' `Azure: Cognitive Search` area e selezionare **Crea nuovo servizio di ricerca**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Nella barra laterale, fare clic con il pulsante destro del mouse sulla sottoscrizione di Azure nell'area * * Azure: ricerca cognitiva * * e selezionare * * Crea nuovo servizio di ricerca * *.":::

1. Seguire le istruzioni per fornire le seguenti informazioni:

    |Prompt|Immettere|
    |--|--|
    |Immettere un nome univoco globale per la nuova servizio di ricerca.|**Ricordare questo nome**. Questo nome di risorsa diventa parte dell'endpoint della risorsa.|
    |Selezionare un gruppo di risorse per le nuove risorse|Usare il gruppo di risorse creato per questa esercitazione.|
    |Selezionare lo SKU per il servizio di ricerca.|Per questa esercitazione selezionare **gratuito** . Non è possibile modificare il piano tariffario dello SKU dopo la creazione del servizio.|
    |Selezionare un percorso per le nuove risorse.|Selezionare un'area nelle vicinanze.|

1. Una volta completate le richieste, la nuova risorsa di ricerca viene creata. 

## <a name="get-your-search-resource-admin-key"></a>Ottenere la chiave di amministrazione della risorsa di ricerca

Ottenere la chiave di amministrazione della risorsa di ricerca con l'estensione Visual Studio Code. 

1. Nella barra laterale di Visual Studio Code, fare clic con il pulsante destro del mouse sulla risorsa di ricerca e scegliere **copia chiave amministratore**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Nella barra laterale, fare clic con il pulsante destro del mouse sulla risorsa di ricerca e selezionare * * Copy admin Key * *.":::

1. Mantieni questa chiave di amministratore. dovrai usarla in [una sezione successiva](#prepare-the-bulk-import-script-for-search). 

## <a name="prepare-the-bulk-import-script-for-search"></a>Preparare lo script di importazione bulk per la ricerca

Lo script usa Azure SDK per ricerca cognitiva:

* [pacchetto NPM @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Documentazione di riferimento](/javascript/api/overview/azure/search-documents-readme)

1. In Visual Studio Code aprire il `bulk_insert_books.js` file nella sottodirectory,  `search-web/bulk-insert` , sostituire le variabili seguenti con i valori personalizzati per l'autenticazione con l'SDK di ricerca di Azure:

    * NOME-RICERCA-RISORSA
    * YOUR-SEARCH-ADMIN-KEY

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Aprire un terminale integrato in Visual Studio per la sottodirectory della directory del progetto `search-web/bulk-insert` ed eseguire il comando seguente per installare le dipendenze. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Eseguire lo script di importazione bulk per la ricerca

1. Continuare a usare il terminale integrato in Visual Studio per la sottodirectory della directory del progetto,, per eseguire il `search-web/bulk-insert` comando bash seguente per eseguire lo `bulk_insert_books.js` script:

    ```javascript
    npm start
    ```

1. Durante l'esecuzione del codice, nella console viene visualizzato lo stato di avanzamento. 
1. Al termine del caricamento, l'ultima istruzione stampata nella console viene "completata".

## <a name="review-the-new-search-index"></a>Esaminare il nuovo indice di ricerca

Al termine del caricamento, l'indice di ricerca è pronto per l'uso. Esaminare il nuovo indice.

1. In Visual Studio Code aprire l'estensione Azure ricerca cognitiva e selezionare la risorsa di ricerca.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="In Visual Studio Code aprire l'estensione Azure ricerca cognitiva e aprire la risorsa di ricerca.":::

1. Espandere indici, quindi documenti, quindi `good-books` selezionare un documento per visualizzare tutti i dati specifici del documento.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Espandere indici, quindi &quot;libri validi&quot; e quindi selezionare un documento.":::

## <a name="copy-your-search-resource-name"></a>Copiare il nome della risorsa di ricerca

Prendere nota del **nome della risorsa di ricerca**. Questa operazione sarà necessaria per connettere l'app per le funzioni di Azure alla risorsa di ricerca. 

> [!CAUTION]
> Sebbene sia possibile tentare di usare la chiave di amministrazione della ricerca nella funzione di Azure, questo non segue il principio dei privilegi minimi. La funzione di Azure userà la chiave di query per conformarsi al privilegio minimo. 

## <a name="next-steps"></a>Passaggi successivi

[Distribuire l'app Web statica](tutorial-javascript-deploy-static-web-app.md)