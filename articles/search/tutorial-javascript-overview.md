---
title: "Esercitazione su JavaScript: Panoramica dell'integrazione di ricerca"
titleSuffix: Azure Cognitive Search
description: Panoramica tecnica e configurazione per aggiungere la ricerca a un sito Web e distribuirla nell'app Web statica di Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726882"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1-Panoramica dell'aggiunta della ricerca a un sito Web

Questa esercitazione consente di compilare un sito Web per eseguire ricerche in un catalogo di libri e quindi di distribuire il sito Web in un'app Web statica di Azure. 

L'applicazione è disponibile: 
* [Esempio](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Sito Web demo-aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Che cosa fa l'esempio? 

Questo sito Web di esempio fornisce l'accesso a un catalogo di 10.000 libri. Un utente può eseguire ricerche nel catalogo immettendo il testo nella barra di ricerca. Mentre l'utente immette testo, il sito Web usa la funzionalità di suggerimento dell'indice di ricerca per completare il testo. Al termine della query, l'elenco di libri viene visualizzato con una parte dei dettagli. Un utente può selezionare un libro per visualizzare tutti i dettagli, archiviati nell'indice di ricerca, del libro. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Questo sito Web di esempio fornisce l'accesso a un catalogo di 10.000 libri. Un utente può eseguire ricerche nel catalogo immettendo il testo nella barra di ricerca. Mentre l'utente immette testo, il sito Web usa la funzionalità di suggerimento dell'indice di ricerca per completare il testo. Al termine della ricerca, l'elenco di libri viene visualizzato con una parte dei dettagli. Un utente può selezionare un libro per visualizzare tutti i dettagli, archiviati nell'indice di ricerca, del libro.":::

L'esperienza di ricerca include: 

* Search: fornisce funzionalità di ricerca per l'applicazione.
* Suggerisci: fornisce suggerimenti durante la digitazione dell'utente nella barra di ricerca.
* Ricerca documenti: Cerca un documento in base all'ID per recuperare tutto il contenuto per la pagina dei dettagli.

## <a name="how-is-the-sample-organized"></a>Come è organizzato l'esempio?

Nell' [esempio](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) sono inclusi gli elementi seguenti:

|App|Scopo|GitHub<br>Archivio<br>Location|
|--|--|--|
|Client|App REACT (livello di presentazione) per visualizzare i libri, con la ricerca. Chiama l'app per le funzioni di Azure. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Server|App per le funzioni di Azure (livello aziendale): chiama l'API di Azure ricerca cognitiva usando JavaScript SDK |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Inserimento in blocco|File JavaScript per creare l'indice e aggiungere documenti.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Installare quanto segue per l'ambiente di sviluppo locale. 

- [Node.js 12 o 14](https://nodejs.org/en/download)
    - Se nel computer locale è installata una versione diversa di Node.js, prendere in considerazione l'uso di [node Version Manager](https://github.com/nvm-sh/nvm) (NVM) o di un contenitore docker.  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) e le estensioni seguenti
    - [Azure Resources](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups) (Risorse di Azure)
    - [Azure ricerca cognitiva 0.2.0 +](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [App Web statica di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Facoltativo:
    - Questa esercitazione non esegue localmente l'API per le funzioni di Azure, ma se si intende eseguirla localmente, è necessario installare [Azure-Functions-Core-Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) a livello globale con il comando bash seguente: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Creare un fork e clonare l'esempio di ricerca con git

Il fork del repository di esempio è essenziale per poter distribuire l'app Web statica. Le app Web determinano le azioni di compilazione e il contenuto di distribuzione in base alla propria posizione di fork di GitHub. L'esecuzione del codice nell'app Web statica è remota, con app Web statiche di Azure che leggono dal codice nell'esempio fork.

1. In GitHub biforcare il [repository di esempio](https://github.com/Azure-Samples/azure-search-javascript-samples). 

    Completare il processo di Fork nel Web browser con l'account GitHub. Questa esercitazione usa il fork come parte della distribuzione in un'app Web statica di Azure. 

1. Nel terminale bash scaricare l'applicazione di esempio nel computer locale. 

    Sostituire `YOUR-GITHUB-ALIAS` con l'alias github. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. In Visual Studio Code aprire la cartella locale del repository clonato. Le attività rimanenti vengono eseguite da Visual Studio Code, a meno che non sia specificato.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Creare un gruppo di risorse per le risorse di Azure

1. In Visual Studio Code aprire la [barra attività](https://code.visualstudio.com/docs/getstarted/userinterface)e selezionare l'icona Azure. 
1. Nella barra laterale, **fare clic con il pulsante destro del mouse sulla sottoscrizione di Azure** nell' `Resource Groups` area e scegliere **Crea gruppo di risorse**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Nella barra laterale * * fare clic con il pulsante destro del mouse sulla sottoscrizione di Azure * * nell'area &quot;gruppi di risorse&quot; e selezionare * * Crea gruppo di risorse * *.":::
1. Immettere un nome per il gruppo di risorse, ad esempio `cognitive-search-website-tutorial` . 
1. Selezionare una località vicina.
1. Quando si creano le risorse di ricerca cognitiva e app Web statiche, più avanti nell'esercitazione usare questo gruppo di risorse. 

    La creazione di un gruppo di risorse offre un'unità logica per gestire le risorse, inclusa la relativa eliminazione al termine dell'uso.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un indice di ricerca e caricare i documenti](tutorial-javascript-create-load-index.md)
* [Distribuire l'app Web statica](tutorial-javascript-deploy-static-web-app.md)
