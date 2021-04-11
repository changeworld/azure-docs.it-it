---
title: 'Esercitazione su JavaScript: distribuire un sito Web abilitato per la ricerca'
titleSuffix: Azure Cognitive Search
description: Distribuire il sito Web abilitato per la ricerca nell'app Web statica di Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726894"
---
# <a name="3---deploy-the-search-enabled-website"></a>3-distribuire il sito Web abilitato per la ricerca

Distribuire il sito Web abilitato alla ricerca come app Web statica di Azure. Questa distribuzione include sia l'app React che l'app per le funzioni.  

L'app Web statica estrae le informazioni e i file per la distribuzione da GitHub usando il fork del repository degli esempi.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Creare un'app Web statica in Visual Studio Code

1. Selezionare **Azure** dalla barra attività, quindi selezionare **app Web statiche** dalla barra laterale. 
1. Fare clic con il pulsante destro del mouse sul nome della sottoscrizione, quindi scegliere **Crea app Web statica (avanzata)**.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Fare clic con il pulsante destro del mouse sul nome della sottoscrizione, quindi selezionare * * Crea app Web statica (avanzata) * *.":::

1. Seguire le istruzioni per fornire le seguenti informazioni:

    |Prompt|Immettere|
    |--|--|
    |Come si vuole creare un'app Web statica?|USA repository GitHub esistente|
    |Scegliere l'organizzazione|Selezionare il _proprio_ alias github come organizzazione.|
    |Scegliere un archivio|Selezionare **Azure-Search-JavaScript-Samples** dall'elenco. |
    |Scegliere il ramo del repository|Selezionare **Master** dall'elenco. |
    |Immettere il nome per la nuova app Web statica.|Creare un nome univoco per la risorsa. Ad esempio, è possibile anteporre il nome al nome del repository, ad esempio, `joansmith-azure-search-javascript-samples` . |
    |Selezionare un gruppo di risorse per le nuove risorse.|Usare il gruppo di risorse creato per questa esercitazione.|
    |Scegliere Compila set di impostazioni per configurare la struttura del progetto predefinita.|Selezionare **Personalizzata**|
    |Selezionare il percorso del codice dell'applicazione|`search-website`|
    |Selezionare il percorso del codice della funzione di Azure|`search-website/api`|
    |Immettere il percorso dell'output di compilazione...|build|
    |Selezionare un percorso per le nuove risorse.|Selezionare un'area nelle vicinanze.|

1. La risorsa viene creata e selezionare **Apri azioni in GitHub** dalle notifiche. Verrà visualizzata una finestra del browser che punta al repository con fork. 

    L'elenco di azioni indica che l'app Web, client e funzioni, è stata inserita correttamente nell'app Web statica di Azure. 

    Attendere il completamento della compilazione e della distribuzione prima di continuare. Il completamento dell'operazione potrebbe richiedere un minuto o due.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Ottenere ricerca cognitiva chiave di query in Visual Studio Code

1. In Visual Studio Code aprire la [barra attività](https://code.visualstudio.com/docs/getstarted/userinterface)e selezionare l'icona Azure. 

1. Nella barra laterale selezionare la sottoscrizione di Azure nell'area **Azure: ricerca cognitiva** , quindi fare clic con il pulsante destro del mouse sulla risorsa di ricerca e scegliere **copia chiave di query**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Nella barra laterale selezionare la sottoscrizione di Azure nell'area * * Azure: ricerca cognitiva * *, quindi fare clic con il pulsante destro del mouse sulla risorsa di ricerca e selezionare * * copia chiave di query * *.":::

1. Mantieni questa chiave di query, che dovrai usare nella sezione successiva. La chiave di query è in grado di eseguire query sull'indice. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Aggiungere le impostazioni di configurazione nel Visual Studio Code

L'app per le funzioni di Azure non restituisce i dati di ricerca finché i segreti di ricerca non sono in impostazioni. 

1. Selezionare **Azure** dalla barra attività, quindi selezionare **app Web statiche** dalla barra laterale. 
1. Espandere la nuova app Web statica finché non vengono visualizzate le **impostazioni dell'applicazione** .
1. Fare clic con il pulsante destro del mouse su **Impostazioni applicazione**, quindi scegliere **Aggiungi nuova impostazione**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Fare clic con il pulsante destro del mouse su * * Impostazioni applicazione * *, quindi selezionare * * Aggiungi nuova impostazione * *.":::

1. Usare le impostazioni seguenti:

    |Impostazione|Valore della risorsa di ricerca|
    |--|--|
    |SearchApiKey|Chiave di query di ricerca|
    |SearchServiceName|Nome della risorsa di ricerca|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Usare la ricerca nell'app Web statica

1. In Visual Studio Code aprire la [barra attività](https://code.visualstudio.com/docs/getstarted/userinterface)e selezionare l'icona Azure.
1. Nella barra laterale, **fare clic con il pulsante destro del mouse sulla sottoscrizione di Azure** nell' `Static web apps` area e individuare l'app Web statica creata per questa esercitazione.
1. Fare clic con il pulsante destro del mouse sul nome dell'app Web statica e selezionare **Sfoglia sito**.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Fare clic con il pulsante destro del mouse sul nome dell'app Web statica e scegliere * * Sfoglia sito * *.":::    

1. Selezionare **Apri** nella finestra di dialogo popup.
1. Nella barra di ricerca del sito Web immettere una query di ricerca `code` , ad esempio, _lentamente_ , in modo che la funzionalità Suggerisci suggerisca i titoli dei libri. Selezionare un suggerimento o continuare a immettere una query personalizzata. Premere invio al termine della query di ricerca. 
1. Esaminare i risultati e quindi selezionare uno dei libri per visualizzare altri dettagli. 

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create in questa esercitazione, eliminare il gruppo di risorse.

1. In Visual Studio Code aprire la [barra attività](https://code.visualstudio.com/docs/getstarted/userinterface)e selezionare l'icona Azure. 

1. Nella barra laterale, **fare clic con il pulsante destro del mouse sulla sottoscrizione di Azure** nell' `Resource Groups` area e individuare il gruppo di risorse creato per questa esercitazione.
1. Fare clic con il pulsante destro del mouse sul nome del gruppo di risorse e scegliere **Elimina**.
    In questo modo vengono eliminate sia le risorse di ricerca che quelle dell'app Web statica.
1. Se non si vuole più usare il fork di GitHub dell'esempio, ricordarsi di eliminarlo su GitHub. Passare alle **Impostazioni** del fork e quindi eliminare il fork. 


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'integrazione della ricerca per il sito Web abilitato alla ricerca](tutorial-javascript-search-query-integration.md)
