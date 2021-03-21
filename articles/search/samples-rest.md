---
title: Esempi REST
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice REST di Azure ricerca cognitiva demo che usano le API REST di ricerca o di gestione.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98956530"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Esempi di codice REST per Azure ricerca cognitiva

Informazioni sugli esempi di API REST che illustrano la funzionalità e il flusso di lavoro di una soluzione Azure ricerca cognitiva. Questi esempi usano le [**API REST di ricerca**](/rest/api/searchservice).

REST è l'interfaccia di programmazione definitiva per Azure ricerca cognitiva e tutte le operazioni che possono essere richiamate a livello di codice sono disponibili prima in REST e quindi negli SDK. Per questo motivo, la maggior parte degli esempi nella documentazione sfruttano le API REST per illustrare o spiegare concetti importanti.

Gli esempi REST vengono in genere sviluppati e testati nel post, ma è possibile usare qualsiasi client che supporta le chiamate HTTP:

+ Iniziare con la [Guida introduttiva: creare un indice di Azure ricerca cognitiva usando le API REST](search-get-started-rest.md) per informazioni sulla formulazione di chiamate http.
+ Provare [Visual Studio Code estensione per ricerca cognitiva di Azure](search-get-started-vs-code.md), attualmente in anteprima, se si lavora in Visual Studio Code.

## <a name="doc-samples"></a>Esempi di documenti

Gli esempi di codice del team di ricerca cognitiva illustrano le funzionalità e i flussi di lavoro. Molti di questi esempi fanno riferimento a esercitazioni, guide introduttive e articoli sulle procedure. È possibile trovare questi esempi in [**Azure-Samples/Azure-Search-posting-Samples**](https://github.com/Azure-Samples/azure-search-postman-samples) in GitHub.

| Esempi | Articolo |
|---------|---------|
| [Guida introduttiva](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca usando le API REST](search-get-started-rest.md). Questo articolo illustra il flusso di lavoro di base per la creazione, il caricamento e l'esecuzione di query su un indice di ricerca usando dati di esempio. |
| [Esercitazione](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Codice sorgente per [esercitazione: usare REST e intelligenza artificiale per generare contenuto ricercabile da BLOB di Azure](cognitive-search-tutorial-blob.md). Questo articolo illustra come creare un oggetto di competenze che scorre i BLOB di Azure per estrarre le informazioni e dedurre la struttura.|
| [Debug-sessioni](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Codice sorgente per [esercitazione: diagnosi, ripristino e commit delle modifiche apportate al tuo competenze](cognitive-search-tutorial-debug-sessions.md). Questo articolo illustra come usare una sessione di debug di un Skills nel portale di Azure. REST viene usato per creare gli oggetti usati durante il debug.|
| [analizzatori personalizzati](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Codice sorgente per [esercitazione: creare un analizzatore personalizzato per i numeri di telefono](tutorial-create-custom-analyzer.md). Questo articolo illustra come usare gli analizzatori per mantenere i modelli e i caratteri speciali nei contenuti disponibili per la ricerca.|
| [Archivio informazioni](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Codice sorgente per [la creazione di un archivio informazioni con REST e postazione](knowledge-store-create-rest.md). In questo articolo vengono illustrati i passaggi necessari per popolare un archivio informazioni utilizzato per i flussi di lavoro di Knowledge mining. |
| [proiezioni](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Codice sorgente per [la modalità di modellazione ed esportazione di arricchimenti](knowledge-store-projections-examples.md). In questo articolo viene illustrato come specificare le strutture dei dati fisici in un archivio informazioni.|
| [BLOB con crittografia degli indici](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Codice sorgente [che illustra come indicizzare i BLOB crittografati usando gli indicizzatori BLOB e skillsets](search-howto-index-encrypted-blobs.md). Questo articolo illustra come indicizzare i documenti nell'archivio BLOB di Azure che sono stati crittografati in precedenza con Azure Key Vault. |

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="other-samples"></a>Altri esempi

Gli esempi seguenti sono pubblicati anche dal team di ricerca cognitiva, ma non vi viene fatto riferimento nella documentazione. I file Leggimi associati forniscono istruzioni di utilizzo.

| Esempi | Descrizione |
|---------|-------------|
| [Query-esempi](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Raccolte di post che dimostrano le varie tecniche di query, tra cui la ricerca fuzzy, la ricerca di espressioni regolari e con caratteri jolly, il completamento automatico e così via. |