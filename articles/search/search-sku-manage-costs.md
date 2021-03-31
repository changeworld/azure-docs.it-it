---
title: Stimare i costi
titleSuffix: Azure Cognitive Search
description: Informazioni sugli eventi fatturabili, sul modello di determinazione dei prezzi e sui suggerimenti per la gestione dei costi di esecuzione di un servizio ricerca cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539553"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Come stimare e gestire i costi di un servizio ricerca cognitiva di Azure

In questo articolo vengono fornite informazioni sul modello di determinazione dei prezzi, sugli eventi fatturabili e sui suggerimenti per la gestione dei costi di esecuzione di un servizio ricerca cognitiva di Azure.

## <a name="pricing-model"></a>Modello di prezzi

L'architettura di scalabilità in Azure ricerca cognitiva si basa su combinazioni flessibili di repliche e partizioni, in modo da poter variare la capacità a seconda che sia necessaria una maggiore potenza di query o di indicizzazione e paghi solo gli elementi necessari.

La quantità di risorse usate dal servizio di ricerca, moltiplicata per la tariffa di fatturazione stabilita dal livello di servizio, determina il costo di esecuzione del servizio. I costi e la capacità sono strettamente associati. Quando si stimano i costi, comprendere la capacità necessaria per eseguire i carichi di lavoro di indicizzazione e query ti offre la migliore idea dei costi previsti.

Ai fini della fatturazione, è necessario tenere presenti due semplici formule:

| Formula | Descrizione |
|---------|-------------|
| **R x P = SU** | Il numero di repliche utilizzate, moltiplicato per il numero di partizioni utilizzate, equivale alla quantità di *unità di ricerca* (su) utilizzata da un servizio. Un SU è un'unità di risorsa e può essere una partizione o una replica. |
| **Velocità di fatturazione SU * = spesa mensile** | Il numero di unità di streaming moltiplicate per la tariffa di fatturazione del livello in cui è stato effettuato il provisioning del servizio è il fattore principale della fattura mensile complessiva. Alcune funzionalità o carichi di lavoro hanno dipendenze da altri servizi di Azure, che possono aumentare il costo della soluzione a livello di sottoscrizione. La sezione eventi fatturabili seguente identifica le funzionalità che possono essere aggiunte alla fattura. |

Ogni servizio inizia con una SU (una replica moltiplicata per una partizione) come valore minimo. Il valore massimo per qualsiasi servizio è 36 unità di streaming. Questo valore massimo può essere raggiunto in diversi modi: 6 partizioni x 6 repliche o 3 partizioni x 12 repliche, ad esempio. È normale usare una capacità inferiore alla capacità totale (ad esempio, un servizio a 3 repliche, a 3 partizioni fatturato come 9 unità di streaming). Vedere il grafico delle [combinazioni di partizioni e replica](search-capacity-planning.md#chart) per le combinazioni valide.

La tariffa di fatturazione è oraria per SU. Ogni livello ha una velocità progressivamente superiore. I livelli superiori sono dotati di partizioni più grandi e più veloci e contribuiscono a una tariffa oraria complessiva superiore per tale livello. È possibile visualizzare le tariffe per ogni livello nella pagina [Dettagli prezzi](https://azure.microsoft.com/pricing/details/search/) .

La maggior parte dei clienti porta online solo una parte della capacità totale, tenendo il resto di riserva. Per la fatturazione, il numero di partizioni e repliche che si porta online, calcolato dalla formula SU, determina il pagamento su base oraria. 

## <a name="billable-events"></a>Eventi fatturabili

Una soluzione basata su ricerca cognitiva di Azure può comportare costi nei modi seguenti:

+ [Costo del servizio](#service-costs) stesso, che esegue 24x7, con una configurazione minima, ovvero una partizione e una replica, alla tariffa di base. È possibile considerare questo problema come il costo fisso per l'esecuzione del servizio.

+ Aggiunta di capacità (repliche o partizioni), in cui i costi aumentano a incrementi della velocità fatturabile. Se la disponibilità elevata è un requisito aziendale, saranno necessarie 3 repliche.

+ Addebiti per la larghezza di banda (trasferimento dati in uscita)

+ Servizi aggiuntivi richiesti per funzionalità o funzionalità specifiche:

  + Arricchimento AI (richiede [Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + Archivio informazioni (richiede [archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/))
  + arricchimento incrementale (richiede [archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/), si applica all'arricchimento di intelligenza artificiale)
  + chiavi gestite dal cliente e crittografia doppia (richiede [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + endpoint privati per un modello di accesso senza Internet (richiede un [collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Costi del servizio

A differenza delle macchine virtuali o di altre risorse che possono essere "sospese" per evitare addebiti, un servizio Azure ricerca cognitiva è sempre disponibile su hardware dedicato per l'uso esclusivo. Di conseguenza, la creazione di un servizio è un evento fatturabile che inizia al momento della creazione del servizio e termina quando si elimina il servizio. 

L'addebito minimo è la prima unità di ricerca (una replica x una partizione) alla velocità fatturabile. Questo valore minimo è fisso per la durata del servizio perché il servizio non può essere eseguito con un valore inferiore a questa configurazione. 

Oltre al minimo, è possibile aggiungere repliche e partizioni indipendentemente l'una dall'altra. Gli aumenti incrementali della capacità con le repliche e le partizioni aumenteranno la fattura in base alla formula seguente: **(repliche x partizioni x frequenza di fatturazione)**, in cui la tariffa addebitata dipende dal piano tariffario selezionato.

Quando si stima il costo di una soluzione di ricerca, tenere presente che i prezzi e la capacità non sono lineari (il raddoppio della capacità è maggiore del doppio del costo). Per un esempio di come funziona la formula, vedere [How to allocate repliche e partizioni](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Tariffe per la larghezza di banda

L'uso di [indicizzatori](search-indexer-overview.md) può influire sulla fatturazione se l'origine dati di Azure si trova in un'area diversa da Azure ricerca cognitiva. In questo scenario potrebbe essere previsto un costo per lo trasferimento dei dati in uscita dall'origine dati di Azure ad Azure ricerca cognitiva. Per informazioni dettagliate, vedere le pagine relative ai prezzi della piattaforma dati di Azure in questione.

È possibile eliminare completamente i dati in uscita se si crea il servizio ricerca cognitiva di Azure nella stessa area dei dati. Di seguito sono riportate alcune informazioni della [pagina dei prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Dati in ingresso: Microsoft non prevede alcun addebito per i dati in ingresso per nessun servizio in Azure. 

+ Dati in uscita: i dati in uscita fanno riferimento ai risultati della query. Ricerca cognitiva non viene addebitato alcun costo per i dati in uscita, ma sono possibili addebiti in uscita da Azure se i servizi si trovano in aree diverse.

  Questi costi non sono in realtà parte della fattura di Azure ricerca cognitiva. Sono citati qui perché se si inviano risultati ad altre aree o app non di Azure, è possibile che si verifichino i costi riportati nella fattura complessiva.

### <a name="ai-enrichment-with-cognitive-services"></a>Arricchimento di intelligenza artificiale con servizi cognitivi

Per l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md), è opportuno pianificare il [collegamento di una risorsa di servizi cognitivi di Azure fatturabile](cognitive-search-attach-cognitive-services.md), nella stessa area del ricerca cognitiva di Azure, a livello di prezzo S0 per l'elaborazione con pagamento in base al consumo. Non esiste un costo fisso associato al fissaggio dei servizi cognitivi. Paghi solo per l'elaborazione che ti serve.

| Operazione | Impatto della fatturazione |
|-----------|----------------|
| Cracking del documento, estrazione del testo | Gratuito |
| Cracking di documenti, estrazione di immagini | Fatturato in base al numero di immagini estratte dai documenti. In una [configurazione dell'indicizzatore](/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** è il parametro che attiva l'estrazione dell'immagine. Se **imageAction** è impostato su "None" (impostazione predefinita), non verrà addebitato l'estrazione dell'immagine. La velocità di estrazione delle immagini è documentata nella pagina dei [Dettagli dei prezzi](https://azure.microsoft.com/pricing/details/search/) per Azure ricerca cognitiva.|
| [Competenze cognitive predefinite](cognitive-search-predefined-skills.md) | Fatturato alla stessa tariffa di se l'attività è stata eseguita usando direttamente servizi cognitivi. |
| Competenze personalizzate | Una competenza personalizzata è la funzionalità fornita dall'utente. Il costo dell'utilizzo di un'abilità personalizzata dipende interamente dal fatto che il codice personalizzato chiami altri servizi a consumo. |

La funzionalità di [arricchimento incrementale (anteprima)](cognitive-search-incremental-indexing-conceptual.md) consente di fornire una cache che consente all'indicizzatore di essere più efficiente quando si eseguono solo le competenze cognitive necessarie se si modificano le competenze in futuro, risparmiando tempo e denaro.

## <a name="tips-for-managing-costs"></a>Suggerimenti per la gestione dei costi

Le linee guida seguenti consentono di ridurre i costi o gestire i costi in modo più efficace:

+ Creare tutte le risorse nella stessa area o in un minor numero di aree possibile per ridurre al minimo o eliminare gli addebiti per la larghezza di banda.

+ Consolidare tutti i servizi in un gruppo di risorse, ad esempio ricerca cognitiva di Azure, servizi cognitivi e qualsiasi altro servizio di Azure usato nella soluzione. Nella portale di Azure trovare il gruppo di risorse e usare i comandi di **Gestione costi** per informazioni dettagliate sulla spesa effettiva e proiettata.

+ Prendere in considerazione l'app Web di Azure per l'applicazione front-end in modo che le richieste e le risposte rientrino entro il limite di data center.

+ Scalabilità verticale per operazioni con utilizzo intensivo di risorse, ad esempio l'indicizzazione, quindi riadatta verso il basso per carichi di lavoro di query normali. Iniziare con la configurazione minima per ricerca cognitiva di Azure (uno SU composto da una partizione e una replica), quindi monitorare le attività degli utenti per identificare i modelli di utilizzo che indicano la necessità di una maggiore capacità. Se è disponibile un modello stimabile, è possibile sincronizzare la scala con l'attività (per automatizzare questa operazione è necessario scrivere il codice).

+ Gestione costi è integrato nell'infrastruttura di Azure. Per ulteriori informazioni sul monitoraggio di costi, strumenti e API, vedere [fatturazione e gestione dei costi](../cost-management-billing/cost-management-billing-overview.md) .

L'arresto di un servizio di ricerca su base temporanea non è possibile. Le risorse dedicate sono sempre operative, allocate per l'uso esclusivo per la durata del servizio. L'eliminazione di un servizio è permanente ed elimina anche i dati associati.

Per quanto riguarda il servizio stesso, l'unico modo per abbassare la fattura consiste nel ridurre le repliche e le partizioni a un livello che fornisce comunque una [conformità](https://azure.microsoft.com/support/legal/sla/search/v1_0/)accettabile per le prestazioni e il contratto di servizio oppure creare un servizio a un livello inferiore (le tariffe orarie S1 sono inferiori alle tariffe S2 o S3). Supponendo di effettuare il provisioning del servizio nella parte inferiore delle proiezioni di carico, se si aumenta il servizio, è possibile creare un secondo servizio a più livelli, ricompilare gli indici nel secondo servizio e quindi eliminare il primo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come monitorare e gestire i costi nella sottoscrizione di Azure.

> [!div class="nextstepaction"]
> [Documentazione di Gestione dei costi e fatturazione di Azure](../cost-management-billing/cost-management-billing-overview.md)