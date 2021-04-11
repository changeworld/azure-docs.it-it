---
title: Basic Tier Preview-overscale (CITUS)-database di Azure per PostgreSQL
description: Livello Basic a nodo singolo per database di Azure per PostgreSQL-iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024046"
---
# <a name="basic-tier-preview"></a>Livello Basic (anteprima)

> [!IMPORTANT]
> Il livello Basic con iperscalabilità (CITUS) è attualmente in anteprima.  Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

Il livello Basic in database di Azure per PostgreSQL-iperscalabilità (CITUS) è un modo semplice per creare un piccolo gruppo di server che è possibile ridimensionare in un secondo momento. Mentre i gruppi di server nel livello standard hanno un nodo coordinatore e almeno due nodi del ruolo di lavoro, il livello Basic esegue tutti gli elementi in un singolo nodo di database.

Oltre a usare un minor numero di nodi, il livello Basic include tutte le funzionalità del livello standard. Come il livello standard, supporta la disponibilità elevata, le repliche di lettura e l'archiviazione tabelle a colonne, tra le altre funzionalità.

## <a name="choosing-basic-vs-standard-tier"></a>Scelta del livello Basic vs standard

Il livello Basic può essere un'opzione di distribuzione economica e conveniente per lo sviluppo iniziale, i test e l'integrazione continua. Usa un singolo nodo di database e presenta la stessa API SQL del livello standard. È possibile testare le applicazioni con il livello Basic e successivamente passare [al livello standard](howto-hyperscale-scale-grow.md#add-worker-nodes) con la certezza che l'interfaccia rimanga invariata.

Il livello Basic è adatto anche per carichi di lavoro più piccoli nell'ambiente di produzione (una volta emersa dalla versione di anteprima alla disponibilità generale). È disponibile spazio per la scalabilità verticale *all'interno* del livello Basic, aumentando il numero di Vcore del server.

Quando la scalabilità è necessaria immediatamente, usare il livello standard. Il gruppo di server più piccolo consentito è costituito da un nodo coordinatore e due ruoli di lavoro. È possibile scegliere di usare più nodi in base al caso d'uso, come descritto nelle procedure per il [ridimensionamento iniziale](howto-hyperscale-scale-initial.md) .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [provisioning del livello Basic](quickstart-create-hyperscale-basic-tier.md)
* Quando si è pronti, vedere la pagina relativa alla [laurea](howto-hyperscale-scale-grow.md#add-worker-nodes) dal livello Basic al livello standard
* L'opzione di [archiviazione a colonne](concepts-hyperscale-columnar.md) è disponibile nel livello Basic e standard
