---
title: BLOB modificati ed eliminati
titleSuffix: Azure Cognitive Search
description: Dopo la compilazione di un indice di ricerca iniziale che importa dall'archivio BLOB di Azure, l'indicizzazione successiva può prelevare solo i BLOB modificati o eliminati. Questo articolo illustra i dettagli.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: b23dabb4388331de9e37ee9db1d4b9d727ccde68
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430561"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Come configurare il rilevamento delle modifiche e dell'eliminazione per i BLOB in Azure ricerca cognitiva indicizzazione

Dopo la creazione di un indice di ricerca iniziale, potrebbe essere necessario che i processi indicizzatori successivi rilevino solo documenti nuovi e modificati. Per il contenuto della ricerca originato dall'archiviazione BLOB di Azure, il rilevamento delle modifiche viene eseguito automaticamente quando si usa una pianificazione per attivare l'indicizzazione. Per impostazione predefinita, il servizio indicizza solo i BLOB modificati, come determinato dal timestamp del BLOB `LastModified` . Diversamente da altre origini dati supportate dagli indicizzatori di ricerca, i BLOB hanno sempre un timestamp, che elimina la necessità di configurare manualmente un criterio di rilevamento delle modifiche.

Sebbene il rilevamento delle modifiche sia specificato, il rilevamento dell'eliminazione non lo è. Se si vuole rilevare i documenti eliminati, assicurarsi di usare un approccio di "eliminazione temporanea". Se si eliminano completamente i BLOB, i documenti corrispondenti non verranno rimossi dall'indice della ricerca.

Esistono due modi per implementare l'approccio di eliminazione temporanea:

+ Eliminazione temporanea BLOB nativi (anteprima), descritta di seguito
+ [Eliminazione temporanea mediante metadati personalizzati](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Eliminazione temporanea BLOB nativa (anteprima)

Per questo approccio di rilevamento delle eliminazioni, ricerca cognitiva dipende dalla funzionalità di [eliminazione temporanea BLOB nativa](../storage/blobs/soft-delete-blob-overview.md) nell'archivio BLOB di Azure per determinare se i BLOB sono passati a uno stato eliminato temporaneamente. Quando vengono rilevati BLOB in questo stato, un indicizzatore di ricerca utilizza queste informazioni per rimuovere il documento corrispondente dall'indice.

> [!IMPORTANT]
> Il supporto per l'eliminazione temporanea dei BLOB nativi è in anteprima. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità è disponibile nell' [API REST versione 2020-06-30-Preview](./search-api-preview.md) . Attualmente non è disponibile alcun portale o supporto per .NET SDK.

### <a name="prerequisites"></a>Prerequisiti

+ [Abilitare l'eliminazione temporanea per i BLOB](../storage/blobs/soft-delete-blob-enable.md).
+ I BLOB devono trovarsi in un contenitore di archiviazione BLOB di Azure. Il criterio di eliminazione temporanea ricerca cognitiva BLOB nativo non è supportato per i BLOB di Azure Data Lake Storage Gen2.
+ È necessario eseguire il mapping delle chiavi del documento per i documenti nell'indice a una proprietà BLOB o a metadati BLOB.
+ Per configurare il supporto per l'eliminazione temporanea, è necessario usare l'API REST di anteprima ( `api-version=2020-06-30-Preview` ).

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Come configurare il rilevamento dell'eliminazione usando l'eliminazione temporanea nativa

1. Nell'archivio BLOB, quando si Abilita l'eliminazione temporanea, impostare i criteri di conservazione su un valore molto superiore rispetto alla pianificazione dell'intervallo di indicizzatore. In questo modo, se si verifica un problema durante l'esecuzione dell'indicizzatore o se si dispone di un numero elevato di documenti da indicizzare, l'indicizzatore potrebbe elaborare i BLOB eliminati temporaneamente. Gli indicizzatori di Azure ricerca cognitiva elimineranno un documento dall'indice solo se elabora il BLOB mentre si trova in uno stato di eliminazione temporanea.

1. In ricerca cognitiva impostare i criteri di rilevamento dell'eliminazione temporanea dei BLOB nativi nell'origine dati. Di seguito è illustrato un esempio. Poiché questa funzionalità è in anteprima, è necessario usare l'API REST di anteprima.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Eseguire l'indicizzatore](/rest/api/searchservice/run-indexer) o impostare l'indicizzatore per l'esecuzione in base a [una pianificazione](search-howto-schedule-indexers.md). Quando l'indicizzatore viene eseguito ed elabora un BLOB con uno stato di eliminazione temporanea, il documento di ricerca corrispondente verrà rimosso dall'indice.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Reindicizzazione di BLOB non eliminati (usando i criteri nativi di eliminazione temporanea)

Se si ripristina un BLOB eliminato temporaneamente nell'archivio BLOB, l'indicizzatore non sempre lo Reindicizza. Questo perché l'indicizzatore usa il timestamp del BLOB `LastModified` per determinare se è necessaria l'indicizzazione. Quando viene annullato l'eliminazione di un BLOB eliminato temporaneamente, il timestamp non viene `LastModified` aggiornato, quindi se l'indicizzatore ha già elaborato BLOB con `LastModified` timestamp più recenti, non verrà reindicizzato il BLOB non eliminato. 

Per assicurarsi che un BLOB non eliminato venga reindicizzato, sarà necessario aggiornare il timestamp del BLOB `LastModified` . Un modo per eseguire questa operazione consiste nel risalvare i metadati del BLOB. Non è necessario modificare i metadati, ma se si risalvano i metadati, il timestamp del BLOB verrà aggiornato `LastModified` in modo che l'indicizzatore lo rilevi.

## <a name="soft-delete-using-custom-metadata"></a>Eliminazione temporanea mediante metadati personalizzati

Questo metodo usa i metadati di un BLOB per determinare se un documento di ricerca deve essere rimosso dall'indice. Questo metodo richiede due azioni separate, eliminando il documento di ricerca dall'indice, seguito dall'eliminazione del BLOB in archiviazione di Azure.

Sono necessari alcuni passaggi per l'archiviazione BLOB e la ricerca cognitiva, ma non esistono altre dipendenze di funzionalità. Questa funzionalità è supportata nelle API disponibili a livello generale.

1. Aggiungere una coppia chiave-valore dei metadati personalizzata al BLOB per indicare ad Azure ricerca cognitiva che è stata eliminata logicamente.

1. Configurare un criterio di rilevamento della colonna di eliminazione temporanea nell'origine dati. Il criterio illustrato sotto, ad esempio, considera l'eliminazione di un BLOB se ha una proprietà di metadati `IsDeleted` con il valore `true`:

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Dopo che l'indicizzatore ha elaborato il BLOB ed eliminato il documento dall'indice, è possibile eliminare il BLOB nell'archivio BLOB di Azure.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Reindicizzazione di BLOB non eliminati (mediante metadati personalizzati)

Dopo l'elaborazione di un BLOB eliminato da parte di un indicizzatore e la rimozione del documento di ricerca corrispondente dall'indice, il BLOB non verrà rivisitato se viene ripristinato in un secondo momento se il timestamp del BLOB `LastModified` è antecedente all'ultima esecuzione dell'indicizzatore.

Se si vuole reindicizzare il documento, modificare per il `"softDeleteMarkerValue" : "false"` BLOB e rieseguire l'indicizzatore.

## <a name="next-steps"></a>Passaggi successivi

+ [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
+ [Come configurare un indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Panoramica dell'indicizzazione BLOB](search-blob-storage-integration.md)