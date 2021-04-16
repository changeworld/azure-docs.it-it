---
title: Configurare la replica geografica per le istanze cache di Azure per Redis Premium
description: Informazioni su come replicare le istanze cache di Azure per Redis Premium tra aree di Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 0be2bb59b46dc827001d89f8e0f1be23f35a714d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536099"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Configurare la replica geografica per le istanze cache di Azure per Redis Premium

Questo articolo illustra come configurare una cache di Azure con replica geografica usando l'portale di Azure.

La replica geografica collega due istanze premium cache di Azure per Redis e crea una relazione di replica dei dati. Queste istanze della cache si trovano in genere in aree di Azure diverse, anche se non sono necessarie. Un'istanza funge da primaria e l'altra come secondaria. Il database primario gestisce le richieste di lettura e scrittura e propaga le modifiche al database secondario. Questo processo continua fino a quando non viene rimosso il collegamento tra le due istanze.

> [!NOTE]
> La replica geografica è progettata come soluzione di ripristino di emergenza.
>
>

## <a name="geo-replication-prerequisites"></a>Prerequisiti per la replica geografica

Per configurare la replica geografica tra due cache, è necessario che siano soddisfatti i prerequisiti seguenti:

- Entrambe le cache sono cache [di livello](cache-overview.md#service-tiers) Premium.
- Entrambe le cache sono nella stessa sottoscrizione di Azure.
- La cache collegata secondaria ha le stesse dimensioni della cache o una dimensione della cache collegata maggiore rispetto alla cache collegata primaria.
- Entrambe le cache vengono create e in stato di esecuzione.

Alcune funzionalità non sono supportate con la replica geografica:

- La persistenza non è supportata con la replica geografica.
- Il clustering è supportato se entrambe le cache hanno il clustering abilitato e hanno lo stesso numero di partizioni.
- Sono supportate le cache nella stessa rete virtuale.
- Le cache in diverse VNET sono supportate con avvertenze. Per [altre informazioni, vedere](#can-i-use-geo-replication-with-my-caches-in-a-vnet) È possibile usare la replica geografica con le cache in una rete virtuale?

Dopo aver configurato la replica geografica, alla coppia di cache collegata si applicano le restrizioni seguenti:

- La cache collegata secondaria è di sola lettura. È possibile leggere da essa, ma non è possibile scrivervi dati. Se si sceglie di leggere dall'istanza di Geo-Secondary, è importante notare che ogni volta che viene eseguita una sincronizzazione completa dei dati tra il Geo-Primary e il Geo-Secondary (si verifica quando viene aggiornato Geo-Primary o Geo-Secondary e anche in alcuni scenari di riavvio), l'istanza di Geo-Secondary genererà errori (che informa che è in corso una sincronizzazione completa dei dati) su qualsiasi operazione Redis fino al completamento della sincronizzazione completa dei dati tra Geo-Primary e Geo-Secondary. Le applicazioni che leggono Geo-Seocndary devono essere compilate per eseguire il fall back al Geo-Primary ogni volta che Geo-Seocndary genera tali errori. 
- Tutti i dati presenti nella cache collegata secondaria prima dell'aggiunta del collegamento vengono rimossi. Se la replica geografica viene rimossa in un secondo momento, tuttavia, i dati replicati rimangono nella cache collegata secondaria.
- Non è possibile [ridimensionare](cache-how-to-scale.md) entrambe le cache mentre le cache sono collegate.
- Non è possibile modificare [il numero di partizioni se nella](cache-how-to-premium-clustering.md) cache è abilitato il clustering.
- Non è possibile abilitare la persistenza in nessuna delle cache.
- È possibile eseguire [l'esportazione](cache-how-to-import-export-data.md#export) da entrambe le cache.
- Non è possibile [importare nella](cache-how-to-import-export-data.md#import) cache collegata secondaria.
- Non è possibile eliminare la cache collegata o il gruppo di risorse che le contiene, fino a quando non si scollegano le cache. Per altre informazioni, vedere [Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se le cache si verificano in aree diverse, i costi di rete in uscita si applicano ai dati spostati tra le aree. Per altre informazioni, vedere [Quanto costa replicare i dati nelle aree di Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Il failover automatico non si verifica tra la cache collegata primaria e secondaria. Per altre informazioni e informazioni su come eseguire il failover di un'applicazione client, vedere Funzionamento del failover nella [cache collegata secondaria](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Aggiungere un collegamento di replica geografica

1. Per collegare due cache per la replica  geografica, fare clic su Replica geografica dal menu Risorsa della cache che si intende usare come cache collegata primaria. Fare quindi clic **sul collegamento Aggiungi replica della cache** nel pannello Replica **geografica.**

    ![Aggiungi collegamento](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Fare clic sul nome della cache secondaria prevista **nell'elenco Cache compatibili** . Se la cache secondaria non viene visualizzata nell'elenco, verificare che siano soddisfatti i prerequisiti di replica geografica per la cache secondaria. [](#geo-replication-prerequisites) Per filtrare le cache in base all'area, fare clic sull'area nella mappa per visualizzare solo le cache **nell'elenco Cache compatibili.**

    ![Cache compatibili con la replica geografica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    È anche possibile avviare il processo di collegamento o visualizzare i dettagli sulla cache secondaria usando il menu di scelta rapida.

    ![Menu di scelta rapida della replica geografica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Fare clic su **Collega** per collegare le due cache e iniziare il processo di replica.

    ![Collega cache](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. È possibile visualizzare lo stato di avanzamento del processo di replica nel pannello **Replica geografica**.

    ![Stato del collegamento](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    È anche possibile visualizzare lo stato del collegamento nel pannello **Panoramica** per le cache primaria e secondaria.

    ![Screenshot che illustra come visualizzare lo stato di collegamento per le cache primaria e secondaria.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Dopo aver completato il processo di replica, **Link status** (Stato collegamento) visualizza **Riuscito**.

    ![Stato della cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    La cache collegata primaria rimane disponibile per l'uso durante il processo di collegamento. La cache collegata secondaria non è disponibile fino al completamento del processo di collegamento.

## <a name="remove-a-geo-replication-link"></a>Rimuovere un collegamento di replica geografica

1. Per rimuovere il collegamento tra due cache e arrestare la replica geografica, fare clic **su Scollega cache** dal pannello **Replica** geografica.
    
    ![Scollega cache](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Al termine del processo di scollegamento, la cache secondaria è disponibile per le operazioni di lettura e scrittura.

>[!NOTE]
>Quando il collegamento di replica geografica viene rimosso, i dati replicati dalla cache collegata primaria rimangono nella cache secondaria.
>
>

## <a name="geo-replication-faq"></a>Domande frequenti sulla replica geografica

- [È possibile usare la replica geografica con una cache di livello Standard o Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [La cache è disponibile per l'uso durante il processo di collegamento o scollegamento?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [È possibile collegare più di due cache?](#can-i-link-more-than-two-caches-together)
- [È possibile collegare due cache da diverse sottoscrizioni di Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [È possibile collegare due cache di dimensioni diverse?](#can-i-link-two-caches-with-different-sizes)
- [È possibile usare la replica geografica con il clustering abilitato?](#can-i-use-geo-replication-with-clustering-enabled)
- [È possibile usare la replica geografica con le cache in una rete virtuale?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Che cos'è la pianificazione della replica per la replica geografica Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo richiede la replica geografica?](#how-long-does-geo-replication-replication-take)
- [Il punto di recupero della replica è garantito?](#is-the-replication-recovery-point-guaranteed)
- [È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per gestire la replica geografica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto costa replicare i dati nelle aree di Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Quale area si deve usare per la cache collegata secondaria?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Come funziona il failover nella cache collegata secondaria?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [È possibile configurare Firewall con la replica geografica?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>È possibile usare la replica geografica con una cache di livello Standard o Basic?

No, la replica geografica è disponibile solo per le cache di livello Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>La cache è disponibile per l'uso durante il processo di collegamento o scollegamento?

- Durante il collegamento, la cache collegata primaria rimane disponibile durante il completamento del processo di collegamento.
- Durante il collegamento, la cache collegata secondaria non è disponibile fino al completamento del processo di collegamento.
- Quando si scollega, entrambe le cache rimangono disponibili durante il completamento del processo di scollegamento.

### <a name="can-i-link-more-than-two-caches-together"></a>È possibile collegare più di due cache?

No, è possibile collegare solo due cache.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>È possibile collegare due cache da diverse sottoscrizioni di Azure?

No, entrambe le cache devono trovarsi nella stessa sottoscrizione di Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>È possibile collegare due cache di dimensioni diverse?

Sì, purché la cache collegata secondaria sia più grande della cache collegata primaria.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>È possibile usare la replica geografica con il clustering abilitato?

Sì, purché entrambe le cache abbiano lo stesso numero di partizioni.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>È possibile usare la replica geografica con le cache in una rete virtuale?

Sì, la replica geografica delle cache nelle VNET è supportata con avvertenze:

- La replica geografica tra le cache nella stessa rete virtuale è supportata.
- È supportata anche la replica geografica tra cache in diverse VNET.
  - Se le reti virtuali si verificano nella stessa area, è possibile connetterle usando il [peering](../virtual-network/virtual-network-peering-overview.md) reti virtuali o una connessione da rete virtuale a [rete virtuale del gateway VPN.](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
  - Se le reti virtuali si trova in aree diverse, la replica geografica con il peering reti virtuali è supportata, ma una macchina virtuale client nella rete virtuale 1 (area 1) non sarà in grado di accedere alla cache nella rete virtuale 2 (area 2) tramite il nome DNS a causa di un vincolo con i servizi di bilanciamento del carico interno Basic. Per altre informazioni sui vincoli di peering reti virtuali, vedere Rete virtuale [- Peering - Requisiti e vincoli.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) La soluzione consigliata consiste nell'usare una connessione da rete virtuale a rete virtuale del gateway VPN.
  
Con [questo modello di Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)è possibile distribuire rapidamente due cache con replica geografica in una rete virtuale connessa con una connessione da rete virtuale a rete virtuale del gateway VPN.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Che cos'è la pianificazione della replica per la replica geografica Redis?

La replica è continua e asincrona e non avviene in base a una pianificazione specifica. Tutte le scritture eseguite nel database primario vengono replicate immediatamente e in modo asincrono nel database secondario.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo richiede la replica geografica?

La replica è incrementale, asincrona e continua e il tempo impiegato non è molto diverso dalla latenza tra le aree. In determinate circostanze, la cache secondaria potrebbe essere necessaria per eseguire una sincronizzazione completa dei dati dal database primario. Il tempo di replica in questo caso dipende da diversi fattori, ad esempio il carico sulla cache primaria, la larghezza di banda di rete disponibile e la latenza tra aree. È stato rilevato che il tempo di replica per una coppia completa con replica geografica da 53 GB può essere compreso tra 5 e 10 minuti.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Il punto di recupero della replica è garantito?

Per le cache in modalità con replica geografica, la persistenza è disabilitata. Se una coppia con replica geografica è scollegata, ad esempio un failover avviato dal cliente, la cache collegata secondaria mantiene i dati sincronizzati fino a quel momento. In tali situazioni non è garantito alcun punto di ripristino.

Per ottenere un punto di ripristino, eseguire [l'esportazione](cache-how-to-import-export-data.md#export) da una delle due cache. In un secondo momento [è possibile importare](cache-how-to-import-export-data.md#import) nella cache collegata primaria.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per gestire la replica geografica?

Sì, la replica geografica può essere gestita usando portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere la documentazione di [PowerShell o](/powershell/module/az.rediscache/#redis_cache) la documentazione dell'interfaccia della [riga di comando di Azure.](/cli/azure/redis/server-link)

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto costa replicare i dati nelle aree di Azure?

Quando si usa la replica geografica, i dati della cache collegata primaria vengono replicati nella cache collegata secondaria. Il trasferimento dei dati non viene addebitato se le due cache collegate si verificano nella stessa area. Se le due cache collegate si trovano in aree diverse, l'addebito per il trasferimento dei dati è il costo in uscita della rete per lo spostamento dei dati in entrambe le aree. Per altre informazioni, vedere [Dettagli sui prezzi della larghezza di banda.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?

Le cache con replica geografica e i relativi gruppi di risorse non possono essere eliminati durante il collegamento finché non si rimuove il collegamento di replica geografica. Se si tenta di eliminare il gruppo di risorse che contiene una o entrambe le cache collegate, vengono eliminate le altre risorse nel gruppo di risorse, ma il gruppo di risorse rimane nello stato `deleting` mentre le cache collegate nel gruppo di risorse rimangono nello stato `running`. Per eliminare completamente il gruppo di risorse e le cache collegate al suo interno, scollegare le cache come descritto in [Rimuovere un collegamento di replica geografica.](#remove-a-geo-replication-link)

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Quale area si deve usare per la cache collegata secondaria?

In generale, è consigliabile che la cache esista nella stessa area di Azure dell'applicazione che vi accede. Per le applicazioni con aree primarie e di fallback separate, è consigliabile che le cache primaria e secondaria esistano nelle stesse aree. Per altre informazioni sulle aree abbinate, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Come funziona il failover nella cache collegata secondaria?

Il failover automatico tra aree di Azure non è supportato per le cache con replica geografica. In uno scenario di ripristino di emergenza, i clienti devono visualizzare l'intero stack di applicazioni in modo coordinato nell'area di backup. Consentire ai singoli componenti dell'applicazione di decidere quando passare ai backup da soli può influire negativamente sulle prestazioni. Uno dei vantaggi principali di Redis è che si tratta di un archivio a bassa latenza. Se l'applicazione principale del cliente si trova in un'area diversa rispetto alla cache, il tempo di round trip aggiunto avrebbe un impatto notevole sulle prestazioni. Per questo motivo, si evita automaticamente il failover a causa di problemi di disponibilità temporanei.

Per avviare un failover avviato dal cliente, scollegare prima le cache. Modificare quindi il client Redis in modo che usi l'endpoint di connessione della cache secondaria (precedentemente collegata). Quando le due cache vengono scollegate, la cache secondaria diventa di nuovo una normale cache di lettura/scrittura e accetta le richieste direttamente dai client Redis.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>È possibile configurare un firewall con la replica geografica?

Sì, è possibile configurare un [firewall con](./cache-configure.md#firewall) la replica geografica. Per il funzionamento della replica geografica insieme a un firewall, assicurarsi che l'indirizzo IP della cache secondaria sia aggiunto alle regole del firewall della cache primaria.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle cache di Azure per Redis avanzate.

* [cache di Azure per Redis di servizio](cache-overview.md#service-tiers)
* [Disponibilità elevata per cache di Azure per Redis](cache-high-availability.md)
