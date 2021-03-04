---
title: Archiviazione immagini del contenitore
description: Informazioni dettagliate su come archiviare le immagini del contenitore e altri elementi in Azure Container Registry, inclusi sicurezza, ridondanza e capacità.
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036011"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Archiviazione immagini del contenitore in Registro Azure Container

Ogni Registro Azure Container [Basic, Standard e Premium](container-registry-skus.md) beneficia delle funzionalità di archiviazione avanzate di Azure, come ad esempio la crittografia dei dati inattivi per la sicurezza dei dati di immagine e la ridondanza geografica per la protezione dei dati di immagine. Le sezioni seguenti descrivono sia le funzionalità che i limiti di archiviazione immagini in Registro Azure Container.

## <a name="encryption-at-rest"></a>Crittografia dei dati inattivi

Tutte le immagini del contenitore e altri elementi nel registro di sistema vengono crittografati a riposo. Azure esegue automaticamente la crittografia di un'immagine prima di archiviarla e la decrittografa in tempo reale quando l'utente, un'applicazione o un servizio eseguono il pull dell'immagine. Facoltativamente, applicare un livello di crittografia aggiuntivo con una [chiave gestita dal cliente](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Archiviazione con ridondanza geografica

Per i registri contenitori distribuiti nella maggior parte delle aree, Azure usa uno schema di archiviazione con ridondanza geografica per prevenire la perdita delle immagini del contenitore e di altri artefatti. Azure Container Registry replica automaticamente le immagini del contenitore in più data center geograficamente distanti, impedendone la perdita in caso di errore di archiviazione a livello di area.

> [!IMPORTANT]
> * Se si verifica un errore di archiviazione a livello di area, è possibile recuperare i dati del registro solo contattando il supporto tecnico di Azure. 
> * Per quanto riguarda i requisiti di residenza dei dati in Brasile meridionale e Asia sudorientale, i dati Container Registry di Azure in tali aree sono archiviati [solo in locale](https://azure.microsoft.com/global-infrastructure/geographies/). Per l'Asia sudorientale, tutti i dati vengono archiviati in Singapore. Per il Brasile meridionale, tutti i dati vengono archiviati in Brasile. Quando l'area viene persa a causa di un'emergenza significativa, Microsoft non sarà in grado di ripristinare i dati di Azure Container Registry.

## <a name="geo-replication"></a>Replica geografica

Per gli scenari che richiedono una disponibilità ancora più elevata, è consigliabile usare la funzione di [replica geografica](container-registry-geo-replication.md) dei registri Premium. La replica geografica scongiura la perdita dell'accesso al registro in caso di guasto *totale* a livello regionale, non solo di un guasto nel sistema di archiviazione. La replica geografica offre anche altri vantaggi, come l'archiviazione immagini vicina alla rete per push/pull più rapidi negli scenari di distribuzione o sviluppo distribuiti.

## <a name="zone-redundancy"></a>Ridondanza della zona

Per creare un registro contenitori di Azure resiliente e a disponibilità elevata, abilitare facoltativamente la [ridondanza della zona](zone-redundancy.md) in aree di Azure selezionate. Una funzionalità del livello di servizio Premium, la ridondanza della zona usa le [zone di disponibilità](../availability-zones/az-overview.md) di Azure per replicare il registro di sistema in almeno tre zone separate in ogni area abilitata. Combinare la replica geografica e la ridondanza della zona per migliorare l'affidabilità e le prestazioni di un registro. 

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
