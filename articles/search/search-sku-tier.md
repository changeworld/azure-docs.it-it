---
title: Scegliere un piano tariffario
titleSuffix: Azure Cognitive Search
description: 'È possibile eseguire il provisioning di Azure ricerca cognitiva in questi livelli: gratuito, Basic e standard, mentre standard è disponibile in diverse configurazioni di risorse e livelli di capacità.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4c58968cb6a38a10433915ec8fa00336ccad301e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216411"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Scegliere un piano tariffario per Azure ricerca cognitiva

Quando si [Crea un servizio di ricerca](search-create-service-portal.md), è necessario scegliere un piano tariffario fisso per la durata del servizio. Il livello selezionato determina:

+ Quantità di indici e altri oggetti che è possibile creare (limiti massimi)
+ Dimensione e velocità delle partizioni (archiviazione fisica)
+ Velocità fatturabile, costo mensile fisso, ma anche costo incrementale se si aggiungono partizioni o repliche

Inoltre, alcune [funzionalità Premium](#premium-features) sono caratterizzate da requisiti di livello.

## <a name="tier-descriptions"></a>Descrizioni dei livelli

I livelli includono **gratuito**, **Basic**, **standard** e con **ottimizzazione** per l'archiviazione. Sono disponibili standard e ottimizzati per l'archiviazione con diverse configurazioni e capacità.

Lo screenshot seguente di portale di Azure Mostra i livelli disponibili, meno i prezzi (che è possibile trovare nel portale e nella pagina dei [prezzi](https://azure.microsoft.com/pricing/details/search/). 

![Piani tariffari di Azure ricerca cognitiva](media/search-sku-tier/tiers.png "Piani tariffari di Azure ricerca cognitiva")

**Gratuito** consente di creare un servizio di ricerca limitato per progetti più piccoli, come l'esecuzione di esercitazioni ed esempi di codice. Internamente, le repliche e le partizioni vengono condivise tra più Sottoscrittori. Non è possibile ridimensionare un servizio gratuito o eseguire carichi di lavoro significativi.

**Basic** e **standard** sono i livelli fatturabili più comunemente usati, con l'impostazione predefinita **standard** . Con le risorse dedicate sotto il controllo, è possibile distribuire progetti di dimensioni maggiori, ottimizzare le prestazioni e aumentare la capacità.

Alcuni livelli sono ottimizzati per determinati tipi di lavoro. Ad esempio, **standard 3 High Density (S3 HD)** è una *modalità di hosting* per S3, in cui l'hardware sottostante è ottimizzato per un numero elevato di indici più piccoli ed è destinato a scenari di multi-tenant. S3 HD ha lo stesso addebito per unità come S3, ma l'hardware è ottimizzato per le letture di file veloci su un numero elevato di indici più piccoli.

I livelli **ottimizzati** per l'archiviazione offrono una capacità di archiviazione superiore a un prezzo inferiore per TB rispetto ai livelli standard. Il compromesso principale è la latenza delle query più elevata, che è necessario convalidare per i requisiti specifici dell'applicazione. Per ulteriori informazioni sulle considerazioni sulle prestazioni di questo livello, vedere [considerazioni sulle prestazioni e sull'ottimizzazione](search-performance-optimization.md).

Per ulteriori informazioni sui vari [livelli, vedere l'articolo](https://azure.microsoft.com/pricing/details/search/)relativo ai [limiti del servizio in Azure ricerca cognitiva](search-limits-quotas-capacity.md) e nella pagina del portale quando si esegue il provisioning di un servizio.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilità delle funzionalità per livello

La maggior parte delle funzionalità sono disponibili in tutti i livelli, incluso il livello gratuito. In alcuni casi, il livello scelto influirà sulla capacità di implementare una funzionalità. La tabella seguente descrive i vincoli di funzionalità correlati al livello di servizio.

| Funzionalità | Limitazioni |
|---------|-------------|
| [indicizzatori](search-indexer-overview.md) | Gli indicizzatori non sono disponibili in S3 HD.  |
| [Arricchimento con intelligenza artificiale](search-security-manage-encryption-keys.md) | Viene eseguito sul livello gratuito, ma non consigliato. |
| [Chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) | Non disponibile per il livello gratuito. |
| [Accesso al firewall IP](service-configure-firewall.md) | Non disponibile per il livello gratuito. |
| [Endpoint privato (integrazione con collegamento privato di Azure)](service-create-private-endpoint.md) | Per le connessioni in ingresso a un servizio di ricerca, non disponibile nel livello gratuito. Per le connessioni in uscita da indicizzatori ad altre risorse di Azure, non disponibile in versione gratuita o S3 HD. Per gli indicizzatori che usano skillsets, non disponibile su gratuito, Basic, S1 o S3 HD.|

Le funzionalità a elevato utilizzo di risorse potrebbero non funzionare correttamente a meno che non si fornisca una capacità sufficiente. Ad esempio, l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) ha competenze a esecuzione prolungata che si timeout su un servizio gratuito, a meno che il set di dati non sia di piccole dimensioni.

## <a name="billable-events"></a>Eventi fatturabili

Una soluzione basata su ricerca cognitiva di Azure può comportare costi nei modi seguenti:

+ [Costo del servizio](#service-costs) stesso, che esegue 24x7, con una configurazione minima, ovvero una partizione e una replica, alla tariffa di base. È possibile considerare questo problema come il costo fisso per l'esecuzione del servizio.

+ Aggiunta di capacità (repliche o partizioni), in cui i costi aumentano a incrementi della velocità fatturabile

+ Addebiti per la larghezza di banda (trasferimento dati in uscita)

+ Servizi aggiuntivi richiesti per funzionalità o funzionalità specifiche:

  + Arricchimento AI (richiede [Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + Archivio informazioni (richiede [archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/))
  + arricchimento incrementale (richiede [archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/), si applica all'arricchimento di intelligenza artificiale)
  + chiavi gestite dal cliente e crittografia doppia (richiede [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + endpoint privati per un modello di accesso senza Internet (richiede un [collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Costi del servizio

A differenza delle macchine virtuali o di altre risorse che possono essere "sospese" per evitare addebiti, un servizio Azure ricerca cognitiva è sempre disponibile su hardware dedicato per l'uso esclusivo. Di conseguenza, la creazione di un servizio è un evento fatturabile che inizia al momento della creazione del servizio e termina quando si elimina il servizio. 

L'addebito minimo è la prima unità di ricerca (una replica x una partizione) alla velocità fatturabile. Questo valore minimo è fisso per la durata del servizio perché il servizio non può essere eseguito con un valore inferiore a questa configurazione. Oltre al minimo, è possibile aggiungere repliche e partizioni indipendentemente l'una dall'altra. Gli aumenti incrementali della capacità tramite repliche e partizioni aumenteranno la fattura in base alla formula seguente: [(repliche x partizioni x frequenza)](#search-units), in cui la tariffa addebitata dipende dal piano tariffario selezionato.

Quando si stima il costo di una soluzione di ricerca, tenere presente che i prezzi e la capacità non sono lineari. (Raddoppiare la capacità più del doppio del costo). Per un esempio di come funziona la formula, vedere [How to allocate repliche e partizioni](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Tariffe per la larghezza di banda

L'uso di [indicizzatori](search-indexer-overview.md) può influire sulla fatturazione se l'origine dati di Azure si trova in un'area diversa da Azure ricerca cognitiva. In questo scenario potrebbe essere previsto un costo per lo trasferimento dei dati in uscita dall'origine dati di Azure ad Azure ricerca cognitiva. Per informazioni dettagliate, vedere le pagine relative ai prezzi della piattaforma dati di Azure in questione.

È possibile eliminare completamente i dati in uscita se si crea il servizio ricerca cognitiva di Azure nella stessa area dei dati. Di seguito sono riportate alcune informazioni della [pagina dei prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Dati in ingresso: Microsoft non prevede alcun addebito per i dati in ingresso per nessun servizio in Azure. 

+ Dati in uscita: i dati in uscita fanno riferimento ai risultati della query. Ricerca cognitiva non viene addebitato alcun costo per i dati in uscita, ma sono possibili addebiti in uscita da Azure se i servizi si trovano in aree diverse. Questi costi non sono in realtà parte della fattura di Azure ricerca cognitiva. Sono citati qui perché se si inviano risultati ad altre aree o app non di Azure, è possibile che si verifichino i costi riportati nella fattura complessiva.

### <a name="ai-enrichment-with-cognitive-services"></a>Arricchimento di intelligenza artificiale con servizi cognitivi

Per l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md), è opportuno pianificare il [collegamento di una risorsa di servizi cognitivi di Azure fatturabile](cognitive-search-attach-cognitive-services.md), nella stessa area del ricerca cognitiva di Azure, a livello di prezzo S0 per l'elaborazione con pagamento in base al consumo. Non esiste un costo fisso associato al fissaggio dei servizi cognitivi. Paghi solo per l'elaborazione che ti serve.

| Operazione | Impatto della fatturazione |
|-----------|----------------|
| Cracking del documento, estrazione del testo | Gratuito |
| Cracking di documenti, estrazione di immagini | Fatturato in base al numero di immagini estratte dai documenti. In una [configurazione dell'indicizzatore](/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** è il parametro che attiva l'estrazione dell'immagine. Se **imageAction** è impostato su "None" (impostazione predefinita), non verrà addebitato l'estrazione dell'immagine. La velocità di estrazione delle immagini è documentata nella pagina dei [Dettagli dei prezzi](https://azure.microsoft.com/pricing/details/search/) per Azure ricerca cognitiva.|
| [Competenze cognitive predefinite](cognitive-search-predefined-skills.md) | Fatturato alla stessa tariffa di se l'attività è stata eseguita usando direttamente servizi cognitivi. |
| Competenze personalizzate | Una competenza personalizzata è la funzionalità fornita dall'utente. Il costo dell'utilizzo di un'abilità personalizzata dipende interamente dal fatto che il codice personalizzato chiami altri servizi a consumo. |

La funzionalità di [arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) consente di fornire una cache che consente all'indicizzatore di essere più efficiente quando si eseguono solo le competenze cognitive necessarie se si modificano le competenze in futuro, risparmiando tempo e denaro.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Formula di fatturazione (R x P = SU)

Il concetto di fatturazione più importante da comprendere per le operazioni di ricerca cognitiva di Azure è l' *unità di ricerca* (su). Poiché ricerca cognitiva di Azure dipende da repliche e partizioni per l'indicizzazione e le query, non ha senso fatturare solo uno o l'altro. Al contrario, la fatturazione si basa su una combinazione di entrambi gli elementi.

SU è il prodotto delle *repliche* e delle *partizioni* usate da un servizio: **(R x P = su)**.

Ogni servizio inizia con una SU (una replica moltiplicata per una partizione) come valore minimo. Il valore massimo per qualsiasi servizio è 36 unità di streaming. Questo valore massimo può essere raggiunto in diversi modi: 6 partizioni x 6 repliche o 3 partizioni x 12 repliche, ad esempio. È normale usare una capacità inferiore alla capacità totale (ad esempio, un servizio a 3 repliche, a 3 partizioni fatturato come 9 unità di streaming). Vedere il grafico delle [combinazioni di partizioni e replica](search-capacity-planning.md#chart) per le combinazioni valide.

La tariffa di fatturazione è oraria per SU. Ogni livello ha una velocità progressivamente superiore. I livelli superiori sono dotati di partizioni più grandi e più veloci e contribuiscono a una tariffa oraria complessiva superiore per tale livello. È possibile visualizzare le tariffe per ogni livello nella pagina [Dettagli prezzi](https://azure.microsoft.com/pricing/details/search/) .

La maggior parte dei clienti porta online solo una parte della capacità totale, tenendo il resto di riserva. Per la fatturazione, il numero di partizioni e repliche che si porta online, calcolato dalla formula SU, determina il pagamento su base oraria.

## <a name="next-steps"></a>Passaggi successivi

Gestione costi è parte integrante della pianificazione della capacità. Come passaggio successivo, continuare con l'articolo seguente per istruzioni su come stimare la capacità e gestire i costi.

> [!div class="nextstepaction"]
> [Come gestire i costi e stimare la capacità in Azure ricerca cognitiva](search-sku-manage-costs.md)