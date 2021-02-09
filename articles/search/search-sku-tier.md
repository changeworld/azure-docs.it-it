---
title: Scegliere un piano tariffario
titleSuffix: Azure Cognitive Search
description: 'Informazioni sui piani tariffari (o SKU) per ricerca cognitiva di Azure. È possibile eseguire il provisioning di un servizio di ricerca in questi livelli: gratuito, Basic e standard. Standard è disponibile in diverse configurazioni di risorse e livelli di capacità.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: df218c2a4c066343ef571c6f24554ecaa806b639
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987879"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Scegliere un piano tariffario per Azure ricerca cognitiva

Parte della [creazione di un servizio di ricerca](search-create-service-portal.md) significa la scelta di un piano tariffario (o SKU) fisso per la durata del servizio. Prezzi, o il costo mensile stimato per l'esecuzione del servizio, vengono visualizzati nella pagina **Seleziona piano tariffario** del portale quando si crea il servizio. Se invece si esegue il provisioning tramite PowerShell o l'interfaccia della riga di comando di Azure, il livello viene specificato tramite il **`-Sku`** parametro ed è necessario controllare i [prezzi dei servizi](https://azure.microsoft.com/pricing/details/search/) per ottenere informazioni sui costi stimati.

Il livello selezionato determina:

+ Numero massimo di indici e altri oggetti consentiti nel servizio
+ Dimensione e velocità delle partizioni (archiviazione fisica)
+ Tariffa fatturabile come costo mensile fisso, ma anche un costo incrementale se si aggiunge capacità

In alcune istanze, il livello scelto determina la disponibilità delle [funzionalità Premium](#premium-features).

> [!NOTE]
> Cerchi informazioni sugli SKU di Azure? Inizia a usare i [prezzi di Azure](https://azure.microsoft.com/pricing/) e scorri verso il basso per visualizzare i collegamenti alle pagine relative ai prezzi per servizio.

## <a name="tier-descriptions"></a>Descrizioni dei livelli

I livelli includono **gratuito**, **Basic**, **standard** e con **ottimizzazione** per l'archiviazione. Sono disponibili standard e ottimizzati per l'archiviazione con diverse configurazioni e capacità. Lo screenshot seguente di portale di Azure Mostra i livelli disponibili, meno i prezzi (che è possibile trovare nel portale e nella pagina dei [prezzi](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Grafico piano tariffario" border="true":::

**Gratuito** consente di creare un servizio di ricerca limitato per progetti più piccoli, come l'esecuzione di esercitazioni ed esempi di codice. Internamente, le risorse di sistema vengono condivise tra più Sottoscrittori. Non è possibile ridimensionare un servizio gratuito o eseguire carichi di lavoro significativi.

**Basic** e **standard** sono i livelli fatturabili più comunemente usati, il cui **standard** è l'impostazione predefinita, perché offre una maggiore flessibilità nel ridimensionamento dei carichi di lavoro. Con le risorse dedicate sotto il controllo, è possibile distribuire progetti di dimensioni maggiori, ottimizzare le prestazioni e aumentare la capacità.

Alcuni livelli sono progettati per determinati tipi di lavoro. Ad esempio, **standard 3 High Density (S3 HD)** è una *modalità di hosting* per S3, in cui l'hardware sottostante è ottimizzato per un numero elevato di indici più piccoli ed è destinato a scenari di multi-tenant. S3 HD ha lo stesso addebito per unità come S3, ma l'hardware è ottimizzato per le letture di file veloci su un numero elevato di indici più piccoli.

I livelli **ottimizzati** per l'archiviazione offrono una capacità di archiviazione superiore a un prezzo inferiore per TB rispetto ai livelli standard. Il compromesso principale è la latenza delle query più elevata, che è necessario convalidare per i requisiti specifici dell'applicazione. Per ulteriori informazioni sulle considerazioni sulle prestazioni di questo livello, vedere [considerazioni sulle prestazioni e sull'ottimizzazione](search-performance-optimization.md).

Per ulteriori informazioni sui vari [livelli, vedere l'articolo](https://azure.microsoft.com/pricing/details/search/)relativo ai [limiti del servizio in Azure ricerca cognitiva](search-limits-quotas-capacity.md) e nella pagina del portale quando si esegue il provisioning di un servizio.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Disponibilità delle funzionalità per livello

La maggior parte delle funzionalità sono disponibili in tutti i livelli, incluso il livello gratuito. In alcuni casi, il livello scelto influirà sulla capacità di implementare una funzionalità. La tabella seguente descrive i vincoli di funzionalità correlati al livello di servizio.

| Funzionalità | Limitazioni |
|---------|-------------|
| [indicizzatori](search-indexer-overview.md) | Gli indicizzatori non sono disponibili in S3 HD.  |
| [Arricchimento con intelligenza artificiale](search-security-manage-encryption-keys.md) | Viene eseguito sul livello gratuito, ma non consigliato. |
| [Identità gestite o attendibili per l'accesso in uscita (indicizzatore)](search-howto-managed-identities-data-sources.md) | Non disponibile per il livello gratuito.|
| [Chiavi di crittografia gestite dal cliente](search-security-manage-encryption-keys.md) | Non disponibile per il livello gratuito. |
| [Accesso al firewall IP](service-configure-firewall.md) | Non disponibile per il livello gratuito. |
| [Endpoint privato (integrazione con collegamento privato di Azure)](service-create-private-endpoint.md) | Per le connessioni in ingresso a un servizio di ricerca, non disponibile nel livello gratuito. Per le connessioni in uscita da indicizzatori ad altre risorse di Azure, non disponibile in versione gratuita o S3 HD. Per gli indicizzatori che usano skillsets, non disponibile su gratuito, Basic, S1 o S3 HD.| 
| [Zone di disponibilità](search-performance-optimization.md) | Non disponibile per il livello gratuito e il livello Basic. |

Le funzionalità a elevato utilizzo di risorse potrebbero non funzionare correttamente a meno che non si fornisca una capacità sufficiente. Ad esempio, l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) ha competenze a esecuzione prolungata che si timeout su un servizio gratuito, a meno che il set di dati non sia di piccole dimensioni.

## <a name="upper-limits"></a>Limiti superiori

I livelli determinano lo spazio di archiviazione massimo del servizio stesso, nonché il numero massimo di indici, indicizzatori, origini dati, skillsets e mappe sinonimi che è possibile creare. Per una ripartizione completa di tutti i limiti, vedere [limiti dei servizi in Azure ricerca cognitiva](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Dimensioni e velocità della partizione

I prezzi dei livelli includono i dettagli sull'archiviazione per partizione che va da 2 GB per Basic, fino a 2 TB per i livelli di archiviazione ottimizzati (L2). Le altre caratteristiche hardware, ad esempio la velocità delle operazioni, la latenza e le velocità di trasferimento, non vengono pubblicate, ma i livelli progettati per architetture di soluzioni specifiche sono basati su hardware con le funzionalità che supportano tali scenari. Per altre informazioni sulle partizioni, vedere [stimare e gestire la capacità](search-capacity-planning.md) e [la scalabilità per le prestazioni](search-performance-optimization.md).

## <a name="billing-rates"></a>Tariffe di fatturazione

I livelli presentano tariffe di fatturazione diverse, con tariffe più elevate per i livelli eseguiti su hardware più costoso o offrono funzionalità più costose. La tariffa per la fatturazione per livello è disponibile nelle pagine relative ai [prezzi di Azure](https://azure.microsoft.com/pricing/details/search/) per ricerca cognitiva di Azure.

Una volta creato un servizio, la tariffa di fatturazione diventa un *costo fisso* per l'esecuzione del servizio intorno al clock e un *costo incrementale* se si sceglie di aggiungere ulteriore capacità.

I servizi di ricerca sono allocate risorse di calcolo sotto forma di *partizioni* (per l'archiviazione) e *repliche* (istanze del motore di query). Inizialmente, viene creato un servizio con uno di questi e la tariffa di fatturazione è inclusa in entrambe le risorse. Tuttavia, se si ridimensiona la capacità, i costi aumentano o diminuiscono a incrementi della velocità fatturabile.

Nell'esempio seguente viene illustrato questo concetto. Si supponga una tariffa di fatturazione ipotetica di $100 al mese. Se si mantiene il servizio di ricerca con la capacità iniziale di una partizione e una replica, $100 è ciò che è possibile prevedere per pagare alla fine del mese. Tuttavia, se si aggiungono altre due repliche per ottenere la disponibilità elevata, la fattura mensile aumenta a $300 ($100 per la prima coppia di partizioni di replica, seguita da $200 per le due repliche).

Questo modello di determinazione dei prezzi si basa sul concetto di applicazione della velocità di fatturazione al numero di *unità di ricerca* (su) usato da un servizio di ricerca. Viene inizialmente eseguito il provisioning di tutti i servizi in una unità di streaming, ma è possibile aumentare il numero di unità di streaming aggiungendo partizioni o repliche per gestire carichi di lavoro di dimensioni maggiori. Per ulteriori informazioni, vedere [come stimare i costi di un servizio di ricerca](search-sku-manage-costs.md).

## <a name="next-steps"></a>Passaggi successivi

Il modo migliore per scegliere un piano tariffario consiste nell'iniziare con un livello di costo inferiore, quindi consentire esperienza e test per informare la decisione di tenere il servizio o crearne uno nuovo a un livello superiore. Per i passaggi successivi, si consiglia di creare un servizio di ricerca in un livello in grado di supportare il livello di test proposto, quindi esaminare le linee guida seguenti per indicazioni sulla stima dei costi e della capacità.

+ [Creare un servizio di ricerca](search-create-service-portal.md)
+ [Stimare i costi](search-sku-manage-costs.md)
+ [Stima della capacità](search-sku-manage-costs.md)