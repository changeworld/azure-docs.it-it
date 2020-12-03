---
title: Informazioni sulle funzionalità di ricerca di Azure (anteprima)
description: Questo articolo illustra come Azure competenza consente l'individuazione dei dati tramite le funzionalità di ricerca.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552700"
---
# <a name="understand-search-features-in-azure-purview"></a>Informazioni sulle funzionalità di ricerca di Azure

Questo articolo fornisce una panoramica dell'esperienza di ricerca in Azure. La ricerca è una funzionalità della piattaforma principale di competenza, che consente l'individuazione dei dati e le esperienze di governance dei dati in un'organizzazione.

## <a name="search"></a>Cerca

L'esperienza di ricerca di competenza è basata su un indice di ricerca gestito. Dopo che un'origine dati è stata registrata con l'ambito di competenza, i relativi metadati vengono indicizzati dal servizio di ricerca per facilitare l'individuazione. L'indice fornisce funzionalità di rilevanza della ricerca e completa le richieste di ricerca eseguendo una query su milioni di asset di metadati. La ricerca consente di individuare, comprendere e usare i dati per ottenere il massimo dal loro valore.

L'esperienza di ricerca in ambito è un processo in tre fasi:

1. Nella casella di ricerca viene visualizzata la cronologia contenente le parole chiave e le risorse usate di recente.
1. Quando si inizia a digitare le sequenze di tasti, la ricerca suggerisce le parole chiave e gli asset corrispondenti. 
1. La pagina dei risultati della ricerca viene visualizzata con asset corrispondenti alla parola chiave immessa.

## <a name="reduce-the-time-to-discover-data"></a>Ridurre il tempo necessario per individuare i dati

L'individuazione dati è il primo passaggio per un carico di lavoro di analisi dei dati o governance dei dati per i consumer di dati L'individuazione dei dati può richiedere molto tempo, in quanto è possibile che non si sappia dove trovare i dati desiderati. Anche dopo aver trovato i dati, è possibile che si verifichino dubbi su come sia possibile considerare attendibili i dati e intraprendere le dipendenze. 

L'obiettivo della ricerca in Azure è quello di velocizzare il processo di individuazione dei dati, fornendo movimenti, per trovare rapidamente i dati più importanti.

## <a name="recent-search-and-suggestions"></a>Ricerca e suggerimenti recenti

Spesso è possibile che si stiano lavorando contemporaneamente su più progetti. Per semplificare la ripresa dei progetti precedenti, la funzionalità di ricerca delle competenze consente di visualizzare le parole chiave e i suggerimenti di ricerca recenti. Inoltre, è possibile gestire la cronologia di ricerca recente selezionando **Visualizza tutto** dall'elenco a discesa della casella di ricerca.

## <a name="filters"></a>Filtri

I filtri (noti anche come *facet*) sono progettati per completare la ricerca. I filtri vengono visualizzati nella pagina dei risultati della ricerca. I filtri nella pagina dei risultati della ricerca includono classificazione, etichetta di riservatezza, origine dati e proprietari. Gli utenti possono selezionare valori specifici in un filtro per visualizzare solo gli asset di dati corrispondenti e limitare i risultati della ricerca agli asset corrispondenti.

## <a name="hit-highlighting"></a>Evidenziazione dei risultati

Le parole chiave corrispondenti nella pagina dei risultati della ricerca vengono evidenziate per semplificare l'identificazione del motivo per cui un asset di dati è stato restituito dalla ricerca. La parola chiave match può verificarsi in più campi, ad esempio il nome, la descrizione e il proprietario dell'asset di dati.

Potrebbe non essere ovvio perché un asset di dati è incluso nella ricerca, anche con l'evidenziazione abilitata. Tutte le proprietà vengono ricercate per impostazione predefinita. Un asset di dati potrebbe pertanto essere restituito a causa di una corrispondenza in una proprietà a livello di colonna. Poiché più utenti possono annotare gli asset di dati con le rispettive classificazioni e descrizioni, non tutti i metadati vengono visualizzati nell'elenco dei risultati della ricerca.

## <a name="sort"></a>Ordina

Gli utenti hanno due opzioni per ordinare i risultati della ricerca. Possono eseguire l'ordinamento in base al nome dell'asset o per impostazione predefinita.

## <a name="search-relevance"></a>Pertinenza della ricerca

Pertinenza è l'ordinamento predefinito nella pagina dei risultati della ricerca. La pertinenza della ricerca trova i documenti che includono la parola chiave Search, some o all. Gli asset che contengono molte istanze della parola chiave di ricerca sono classificati in un livello superiore. Inoltre, i meccanismi di assegnazione dei punteggi personalizzati sono costantemente ottimizzati per migliorare la pertinenza della ricerca.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: creare un account Azure per la competenza nel portale di Azure](create-catalog-portal.md)
* [Guida introduttiva: creare un account Azure per la competenza usando Azure PowerShell CLI di Azure](create-catalog-powershell.md)
* [Guida introduttiva: usare ambito studio](use-purview-studio.md)
