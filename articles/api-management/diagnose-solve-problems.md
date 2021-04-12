---
title: Gestione API di Azure consente di diagnosticare e risolvere i problemi
description: Informazioni su come risolvere i problemi relativi all'API in gestione API di Azure con lo strumento diagnostica e risolvi nel portale di Azure.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652403"
---
# <a name="azure-api-management-diagnostics-overview"></a>Panoramica di diagnostica di gestione API di Azure

Quando si compila e si gestisce un'API in gestione API di Azure, si desidera prepararsi a eventuali problemi che possono verificarsi, da 404 errori non trovati a 502 Errore del gateway errato. La diagnostica di gestione API è un'esperienza intelligente e interattiva che consente di risolvere i problemi relativi all'API pubblicata in gestione API senza che sia necessaria alcuna configurazione. Quando si verificano problemi con le API pubblicate, la diagnostica di gestione API rileva gli errori e fornisce le informazioni appropriate per risolvere rapidamente i problemi e risolvere il problema.

Anche se questa esperienza è particolarmente utile quando si riscontrano problemi con l'API nelle ultime 24 ore, tutti i grafici di diagnostica sono sempre disponibili per l'analisi.

## <a name="open-api-management-diagnostics"></a>Aprire la diagnostica di gestione API

Per accedere alla diagnostica di gestione API, passare all'istanza del servizio gestione API nella [portale di Azure](https://portal.azure.com). Nel percorso di spostamento a sinistra selezionare **diagnostica e Risolvi i problemi**.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Screenshot mostra come passare a diagnostica.":::



## <a name="intelligent-search"></a>Ricerca intelligente

È possibile cercare problemi o problemi nella barra di ricerca nella parte superiore della pagina. La ricerca consente inoltre di trovare gli strumenti che possono aiutare a risolvere i problemi. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="screenshot della ricerca intelligente.":::


## <a name="troubleshooting-categories"></a>Categorie di risoluzione dei problemi

È possibile risolvere i problemi relativi alle categorie. I problemi comuni relativi alle prestazioni dell'API, al gateway, ai criteri API e al livello di servizio possono essere tutti analizzati all'interno di ogni categoria. Ogni categoria fornisce anche controlli di diagnostica più specifici. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="screenshot della panoramica delle categorie.":::


### <a name="availability-and-performance"></a>Disponibilità e prestazioni

Controllare i problemi di disponibilità e prestazioni dell'API in questa categoria. Dopo aver selezionato questo riquadro categoria, verranno visualizzati alcuni controlli comuni in un'interfaccia interattiva. Fare clic su ogni controllo per approfondire le specifiche di ogni problema. Il controllo fornirà anche un grafico che mostra le prestazioni dell'API e un riepilogo dei problemi relativi alle prestazioni. Ad esempio, il servizio API potrebbe avere avuto un errore e un timeout 5xx nell'ultima ora del back-end. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Screenshot 1 del controllo interattivo dell'interfaccia.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Schermata 2 del controllo interattivo dell'interfaccia.":::

### <a name="api-policies"></a>Criteri API

Questa categoria rileva gli errori e notifica i problemi relativi ai criteri. 

Un'interfaccia interattiva simile consente di consultare le metriche dei dati per semplificare la risoluzione dei problemi relativi alla configurazione dei criteri API.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="screenshot del riquadro Categoria criteri API.":::

### <a name="gateway-performance"></a>Prestazioni dei gateway 

Per le richieste o le risposte del gateway o per eventuali errori 4xx o 5xx sul gateway, usare questa categoria per monitorare e risolvere i problemi. Analogamente, è possibile usare l'interfaccia interattiva per approfondire l'area specifica in cui si vuole verificare le prestazioni del gateway API. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="screenshot del riquadro categoria prestazioni gateway.":::

### <a name="service-upgrade"></a>Aggiornamento del servizio

Questa categoria controlla il livello di servizio (SKU) attualmente in uso e ricorda di eseguire l'aggiornamento per evitare eventuali problemi che potrebbero essere correlati a tale livello. La stessa interfaccia interattiva consente di approfondire la grafica e il risultato di un controllo di riepilogo. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="screenshot del riquadro categoria aggiornamento servizio.":::

## <a name="search-documentation"></a>Cerca nella documentazione

Per ulteriori informazioni sugli strumenti di diagnostica e risoluzione dei problemi, è possibile cercare la documentazione per la risoluzione dei problemi correlati al problema. Dopo aver eseguito la diagnostica nel servizio, selezionare la **documentazione di ricerca** nell'interfaccia interattiva. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="schermata 1 di come usare la funzione di ricerca della documentazione.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="schermata 2 di come usare la documentazione di ricerca.":::


## <a name="next-steps"></a>Passaggi successivi

* Usare anche l' [analisi API](howto-use-analytics.md) per analizzare l'utilizzo e le prestazioni delle API. 
* Si desidera risolvere i problemi relativi alle app Web con la diagnostica? Leggilo [qui](../app-service/overview-diagnostics.md)
* Utilizzare la diagnostica per verificare i problemi dei servizi Kubernetes di Azure. Vedere [diagnostica su AKS](../aks/concepts-diagnostics.md)
* Pubblicare domande o commenti e suggerimenti in [UserVoice](https://feedback.azure.com/forums/248703-api-management) aggiungendo "[diag]" nel titolo.
