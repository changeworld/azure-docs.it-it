---
title: 'Guida introduttiva: creare un gruppo di server di livello Basic-iperscalabilità (CITUS)-database di Azure per PostgreSQL'
description: Inizia a usare il livello Basic di database di Azure per PostgreSQL (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024080"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Creare un gruppo di server di livello Basic con iperscalabilità (CITUS) nel portale di Azure

Database di Azure per PostgreSQL-iperscalabilità (CITUS) è un servizio gestito usato per eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Il [livello Basic](concepts-hyperscale-tiers.md) è un'opzione di distribuzione comoda per lo sviluppo e il test iniziali.

Questa Guida introduttiva illustra come creare un gruppo di server di livello Basic con iperscalabilità (CITUS) usando il portale di Azure. Si eseguirà il provisioning del gruppo di server e si verificherà che sia possibile connettersi a esso per eseguire le query.

> [!IMPORTANT]
> Il livello Basic con iperscalabilità (CITUS) è attualmente in anteprima.  Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato illustrato come effettuare il provisioning di un gruppo di server Hyperscale (Citus). È stata stabilita la connessione al gruppo con psql, è stato creato uno schema e sono stati distribuiti i dati.

- Seguire un'esercitazione su come [compilare applicazioni multi-tenant scalabili](./tutorial-design-database-hyperscale-multi-tenant.md)
- Determinare le [dimensioni iniziali](howto-hyperscale-scale-initial.md) ottimali per il gruppo di server
