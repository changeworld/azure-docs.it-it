---
title: Configurare la replica geografica attiva per la cache di Azure aziendale per le istanze di redis
description: Informazioni su come replicare la cache di Azure per le istanze di redis Enterprise tra le aree di Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: d52998e477e44f89a35a70f7e2b5d49cbc68e386
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662794"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Configurare la replica geografica attiva per le istanze di cache di Azure aziendale per le istanze di redis (anteprima)

Questo articolo illustra come configurare una cache di Azure con replica geografica attiva usando il portale di Azure.

La replica geografica attiva raggruppa due o più cache di Azure aziendale per le istanze di redis in un'unica cache che si estende su più aree di Azure. Tutte le istanze agiscono come primari locali. Un'applicazione decide quali istanze usare per le richieste di lettura e scrittura.

## <a name="create-or-join-an-active-geo-replication-group"></a>Creare o partecipare a un gruppo di replica geografica attivo

> [!IMPORTANT]
> La replica geografica attiva deve essere abilitata al momento della creazione di una cache di Azure per Redis.
>
>

1. Nella nuova interfaccia utente di creazione **della cache Redis** fare clic su **Configura** per configurare la **replica geografica attiva** nella scheda **Avanzate** .

    ![Configurare la replica geografica attiva](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Consente di creare un nuovo gruppo di replica, per una prima istanza di cache, oppure di selezionarne uno esistente nell'elenco.

    ![Collega cache](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Fare clic su **Configura** per terminare.

    ![Collega cache](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Ripetere i passaggi precedenti per ogni istanza della cache aggiuntiva nel gruppo di replica geografica.

## <a name="remove-from-an-active-geo-replication-group"></a>Rimuovere da un gruppo di replica geografica attivo

Per rimuovere un'istanza della cache da un gruppo di replica geografica attiva, è sufficiente eliminare l'istanza. Le istanze rimanenti verranno riconfigurate automaticamente.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzionalità di cache di Azure per Redis.

* [Cache di Azure per i livelli di servizio Redis](cache-overview.md#service-tiers)
* [Disponibilità elevata per cache di Azure per Redis](cache-high-availability.md)
