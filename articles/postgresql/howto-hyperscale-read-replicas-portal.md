---
title: Gestire le repliche di lettura-portale di Azure-database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Informazioni su come gestire le repliche di lettura database di Azure per PostgreSQL-iperscalabilità (CITUS) dal portale di Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024036"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Creare e gestire le repliche di lettura in database di Azure per PostgreSQL-iperscalabilità (CITUS) dal portale di Azure

> [!IMPORTANT]
> Le repliche Read in iperscala (CITUS) sono attualmente in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> È possibile visualizzare un elenco completo di altre nuove funzionalità in [Anteprima funzionalità per l'iperscalabilità (CITUS)](hyperscale-preview-features.md).

Questo articolo illustra come creare e gestire le repliche di lettura in iperscala (CITUS) dal portale di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Prerequisiti

Un [gruppo di server con iperscalabilità (CITUS)](quickstart-create-hyperscale-portal.md) come primario.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

Per creare una replica in lettura, seguire questi passaggi:

1. Selezionare un gruppo di server di database di Azure per PostgreSQL da usare come primario. 

2. Nella barra laterale del gruppo di server, in **Gestione gruppi di server**, selezionare **replica**.

3. Selezionare **Aggiungi replica**.

4. Immettere un nome per la replica in lettura. 

5. Fare clic su **OK** per confermare la creazione della replica.

Una volta creata, la replica di lettura può essere visualizzata dalla finestra di **replica** .

> [!IMPORTANT]
>
> Vedere la [sezione Considerazioni relativa alla panoramica di Read replica](concepts-hyperscale-read-replicas.md#considerations).
>
> Prima di aggiornare l'impostazione di un gruppo di server primario a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

## <a name="delete-a-primary-server-group"></a>Eliminare un gruppo di server primario

Per eliminare un gruppo di server primario, utilizzare gli stessi passaggi di per eliminare un gruppo di server CITUS (autoscale) autonomo. 

> [!IMPORTANT]
>
> Quando si elimina un gruppo di server primario, la replica in tutte le repliche di lettura viene arrestata. Le repliche di lettura diventano gruppi di server autonomi che ora supportano sia le letture sia le Scritture.

Per eliminare un gruppo di server dalla portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare il gruppo di server database primario di Azure per PostgreSQL.

2. Aprire la pagina **Panoramica** per il gruppo di server. Selezionare **Elimina**.
 
3. Immettere il nome del gruppo di server primario da eliminare. Selezionare **Elimina** per confermare l'eliminazione del gruppo di server primario.
 

## <a name="delete-a-replica"></a>Eliminare una replica

È possibile eliminare una replica di lettura in modo analogo a come si elimina un gruppo di server primario.

- Nel portale di Azure aprire la pagina **Panoramica** relativa alla replica in lettura. Selezionare **Elimina**.
 
È anche possibile eliminare la replica in lettura dalla finestra **Replica** seguendo la procedura seguente:

1. Nella portale di Azure selezionare il gruppo di server primary overscale (CITUS).

2. Nel menu gruppo di server, in **Gestione gruppi di server**, selezionare **replica**.

3. Selezionare la replica in lettura da eliminare.
 
4. Selezionare **Elimina replica**.
 
5. Immettere il nome della replica da eliminare. Scegliere **Elimina** per confermare l'eliminazione della replica.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [come leggere le repliche in database di Azure per PostgreSQL-iperscalabilità (CITUS)](concepts-hyperscale-read-replicas.md).
