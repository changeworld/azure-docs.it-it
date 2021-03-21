---
title: Ripulitura della cache in Azure front door standard/Premium (anteprima)
description: Questo articolo fornisce informazioni su come ripulire la cache in un front-end di Azure standard/Premium.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099811"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Ripulitura della cache in Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Azure front door standard/Premium memorizza nella cache gli asset fino alla scadenza della durata (TTL) dell'asset. Ogni volta che un client richiede un asset con TTL scaduto, l'ambiente di Azure front door recupera una nuova copia aggiornata dell'asset per soddisfare la richiesta e quindi archivia la cache aggiornata.

È consigliabile assicurarsi che gli utenti ottengano sempre la copia più recente degli asset. Per eseguire questa operazione, è possibile eseguire il versioning degli asset per ogni aggiornamento e pubblicarli come nuovi URL. Azure front door standard/Premium recupererà immediatamente i nuovi asset per le richieste client successive. In alcuni casi potrebbe essere necessario ripulire i contenuti memorizzati nella cache da tutti i nodi perimetrali e forzarli tutti a recuperare nuovi asset aggiornati. Il motivo per cui si vuole ripulire il contenuto memorizzato nella cache è perché sono stati apportati nuovi aggiornamenti all'applicazione o si vuole aggiornare gli asset che contengono informazioni non corrette.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per informazioni sul funzionamento della memorizzazione nella cache, vedere [caching front door di Azure](concept-caching.md) .

## <a name="configure-cache-purge"></a>Configura ripulitura cache

1. Passare alla pagina Panoramica del profilo di sportello anteriore di Azure con gli asset da ripulire, quindi selezionare **Ripulisci cache**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Screenshot della ripulitura della cache nella pagina panoramica.":::

1. Selezionare l'endpoint e il dominio che si vuole ripulire dai nodi perimetrali. *(È possibile selezionare più di un dominio)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Screenshot della pagina di eliminazione della cache.":::

1. Per cancellare tutti gli asset, selezionare **Ripulisci tutti gli asset per i domini selezionati**. In caso contrario, in **percorsi** immettere il percorso di ogni asset che si desidera eliminare.

Questi formati sono supportati negli elenchi di percorsi da eliminare:

* **Eliminazione a percorso singolo**: eliminare singoli asset specificando il percorso completo dell'asset (senza il protocollo e il dominio), con l'estensione di file, ad esempio,/Pictures/strasbourg.png.
* **Eliminazione dominio radice**: Elimina la radice dell'endpoint con "/*" nel percorso.

Le eliminazioni della cache sullo standard/Preium di Azure front door non fanno distinzione tra maiuscole e minuscole. Inoltre, sono indipendenti dalla stringa di query, vale a dire che l'eliminazione di un URL eliminerà tutte le varianti della stringa di query. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).
