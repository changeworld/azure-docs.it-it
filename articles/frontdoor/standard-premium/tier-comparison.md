---
title: Confronto tra SKU Standard/Premium di Azure front door
description: Questo articolo fornisce una panoramica delle differenze tra le funzionalità e gli SKU standard e Premium di Azure front door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 0f5ecef1716a503b7d27d0f5cdde15a4560c0e61
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100247"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Panoramica dello SKU di Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Il front-end di Azure è disponibile per 3 SKU diversi, [sportello anteriore](../front-door-overview.md)di Azure, Azure front door standard (anteprima) e Azure front door Premium (anteprima). Gli SKU standard e Premium di Azure front door combinano le funzionalità di Azure front door, la rete CDN standard di Azure di Microsoft, Azure WAF in una singola piattaforma di rete CDN cloud sicura con protezione dalle minacce intelligente.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere le [**Condizioni supplementari per l'uso delle anteprime di Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Lo **SKU standard di Azure front door** è:

    * Ottimizzazione per la distribuzione di contenuti
    * Offerta di accelerazione contenuto sia statica che dinamica
    * Bilanciamento del carico globale
    * Offload SSL
    * Gestione di domini e certificati
    * Analisi del traffico migliorata 
    * Funzionalità di sicurezza di base

* Lo **SKU di Azure front door Premium** si basa sulle funzionalità dello SKU standard e aggiunge:

    * Funzionalità di sicurezza estese tra WAF
    * Protezione BOT
    * Supporto del collegamento privato
    * Integrazione con Microsoft Threat Intelligence e analisi della sicurezza. 

![Diagramma che mostra un confronto tra gli SKU della porta anteriore.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Confronto tra le funzionalità

| Funzionalità |      Standard      |  Premium |
|----------|:-------------:|------:|
| Domini personalizzati | Sì | Sì |
| Offload SSL | Sì | Sì |
| Memorizzazione nella cache |  Sì  | Sì |
| Compressione | Sì | Sì   |
| Bilanciamento del carico globale | Sì  | Sì |
| Routing di livello 7 | Sì | Sì |
| Riscrittura URL | Sì | Sì |
| Motore regole | Sì | Sì |
| Origine privata (collegamento privato) | No | Sì |
| WAF | No | Sì |
| Protezione bot | No | Sì |
| Metriche e diagnostica migliorate | Sì | Sì |
| Report sul traffico | Sì | Sì |
| Report sulla sicurezza | No | Sì | 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un portello anteriore](create-front-door-portal.md)
