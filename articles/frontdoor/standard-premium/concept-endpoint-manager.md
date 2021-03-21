---
title: 'Sportello anteriore di Azure: Gestione endpoint'
description: Questo articolo fornisce una panoramica di Azure front door Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100102"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>Che cos'è Azure front door standard/Premium (Preview) Endpoint Manager?

> [!NOTE]
> * Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizzare i [documenti di Azure front door](../front-door-overview.md).

Gestione endpoint offre una panoramica degli endpoint configurati per il front-end di Azure. Un endpoint è un raggruppamento logico di domini e delle configurazioni associate. Gestione endpoint consente di gestire la raccolta di endpoint per operazioni CRUD (creazione, lettura, aggiornamento ed eliminazione). È possibile gestire gli elementi seguenti per gli endpoint tramite Gestione endpoint:

* Domains
* Gruppi di origine
* Route
* Sicurezza

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Screenshot di Gestione endpoint senza configurazioni." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Gestione endpoint elenca il numero di istanze di ogni elemento creati in un endpoint. Verrà visualizzato anche lo stato dell'associazione per ogni elemento. Ad esempio, è possibile creare più domini e gruppi di origine e assegnare l'associazione tra di essi con diverse route.

> [!IMPORTANT]
> * Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere le [**Condizioni supplementari per l'uso delle anteprime di Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="linked-view"></a>Visualizzazione collegata

Con la visualizzazione collegata all'interno di Endpoint Manager, è possibile identificare facilmente l'associazione tra gli elementi front-end di Azure, ad esempio:

* Quali domini sono associati all'endpoint corrente?
* Quale gruppo di origine è associato a quale dominio?
* Quale criterio WAF è associato al dominio?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Screenshot di Gestione endpoint con configurazioni." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).
