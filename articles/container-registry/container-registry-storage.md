---
title: Archiviazione immagini del contenitore
description: Informazioni dettagliate su come archiviare le immagini del contenitore e altri elementi in Azure Container Registry, inclusi sicurezza, ridondanza e capacità.
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047746"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Archiviazione immagini del contenitore in Registro Azure Container

Ogni registro contenitori di Azure [Basic, standard e Premium](container-registry-skus.md) trae vantaggio dalle funzionalità avanzate di archiviazione di Azure, inclusa la crittografia inattiva. Le sezioni seguenti descrivono le funzionalità e i limiti dell'archiviazione immagini in Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Crittografia dei dati inattivi

Tutte le immagini del contenitore e altri elementi nel registro di sistema vengono crittografati a riposo. Azure esegue automaticamente la crittografia di un'immagine prima di archiviarla e la decrittografa in tempo reale quando l'utente, un'applicazione o un servizio eseguono il pull dell'immagine. Facoltativamente, applicare un livello di crittografia aggiuntivo con una [chiave gestita dal cliente](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Archiviazione a livello di area

Azure Container Registry archivia i dati nell'area in cui viene creato il registro di sistema, per consentire ai clienti di soddisfare i requisiti di residenza e conformità dei dati. In tutte le aree, ad eccezione del Brasile meridionale e dell'Asia sudorientale, Azure può archiviare anche i dati del registro di sistema in un'area abbinata nella stessa area geografica. Nelle aree del Brasile meridionale e sudorientale, i dati del registro di sistema sono sempre limitati all'area, per soddisfare i requisiti di residenza dei dati per tali aree.

Se si verifica un'interruzione a livello di area, i dati del registro di sistema potrebbero non essere più disponibili e non vengono recuperati automaticamente I clienti che desiderano archiviare i dati del registro di sistema in più aree per ottenere prestazioni migliori tra aree geografiche diverse o che vogliono avere resilienza in caso di interruzione a livello di area devono abilitare la [replica geografica](container-registry-geo-replication.md).

## <a name="geo-replication"></a>Replica geografica

Per gli scenari che richiedono una garanzia a disponibilità elevata, è consigliabile usare la funzionalità di [replica geografica](container-registry-geo-replication.md) dei registri Premium. La replica geografica consente di evitare la perdita di accesso al registro di sistema in caso di errore a livello di area. La replica geografica offre anche altri vantaggi, come l'archiviazione immagini vicina alla rete per push/pull più rapidi negli scenari di distribuzione o sviluppo distribuiti.

## <a name="zone-redundancy"></a>Ridondanza della zona

Per semplificare la creazione di un registro contenitori di Azure resiliente e a disponibilità elevata, abilitare facoltativamente la [ridondanza della zona](zone-redundancy.md) in aree di Azure selezionate. Una funzionalità del livello di servizio Premium, la ridondanza della zona usa le [zone di disponibilità](../availability-zones/az-overview.md) di Azure per replicare il registro di sistema in almeno tre zone separate in ogni area abilitata. Combinare la replica geografica e la ridondanza della zona per migliorare l'affidabilità e le prestazioni di un registro. 

## <a name="scalable-storage"></a>Archiviazione scalabile:

Azure Container Registry consente di creare tutti i repository, le immagini, i livelli o i tag necessari, fino al [limite di archiviazione del registro di sistema](container-registry-skus.md#service-tier-features-and-limits). 

Un numero elevato di repository e tag può influire sulle prestazioni del registro di sistema. Eliminare periodicamente i repository, i tag e le immagini non usati come parte della routine di manutenzione del registro di sistema e, facoltativamente, impostare un [criterio di conservazione](container-registry-retention-policy.md) per i manifesti senza tag. Le risorse del registro di sistema eliminate, ad esempio repository, immagini e tag, *non possono* essere recuperate dopo l'eliminazione. Per altre informazioni sull'eliminazione delle risorse del registro, vedere [Eliminare le immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="storage-cost"></a>Costo di archiviazione

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Registro Azure Container][pricing].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui Registro Container Basic, Standard e Premium, vedere [SKU di Registro Azure Container](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
