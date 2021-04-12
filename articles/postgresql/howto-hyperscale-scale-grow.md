---
title: Ridimensionare un gruppo di server-iperscala (CITUS)-database di Azure per PostgreSQL
description: Modificare le risorse di memoria, disco e CPU del gruppo di server per gestire un aumento del carico
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012530"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Ridimensionare un gruppo di server di scalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) offre la scalabilità self-service per gestire un aumento del carico. Il portale di Azure semplifica l'aggiunta di nuovi nodi del ruolo di lavoro e per aumentare il vcore dei nodi esistenti. L'aggiunta di nodi non comporta tempi di inattività e anche lo trasferimento di partizioni nei nuovi nodi (chiamato [ribilanciamento](howto-hyperscale-scale-rebalance.md)delle partizioni) si verifica senza interrompere le query.

## <a name="add-worker-nodes"></a>Aggiunta di nodi di lavoro

Per aggiungere nodi, passare alla scheda **calcolo e archiviazione** nel gruppo di server di iperscalabilità (CITUS).  Quando si trascina il dispositivo di scorrimento per il **numero di nodi di lavoro** , viene modificato il valore

> [!NOTE]
>
> Un gruppo di server con iperscalabilità (CITUS) creato con il [livello Basic (anteprima)](concepts-hyperscale-tiers.md) non ha alcun lavoro. Aumentando il numero di thread di lavoro si laurea automaticamente il gruppo di server al livello standard.
> Al termine della laurea di un gruppo di server al livello standard, non è possibile eseguire il downgrade al livello Basic.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Dispositivi di scorrimento delle risorse":::

Fare clic sul pulsante **Salva** per rendere effettivo il valore modificato.

> [!NOTE]
> Una volta aumentato e salvato, il numero di nodi di lavoro non può essere ridotto mediante il dispositivo di scorrimento.

> [!NOTE]
> Per sfruttare i vantaggi dei nuovi nodi aggiunti è necessario [ribilanciare le partizioni delle tabelle distribuite](howto-hyperscale-scale-rebalance.md), ovvero trasferire alcune partizioni dai nodi esistenti a quelle nuove. [](concepts-hyperscale-distributed-data.md#shards)

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentare o ridurre il numero di vCore nei nodi

Oltre ad aggiungere nuovi nodi, è possibile aumentare la capacità dei nodi esistenti. La regolazione della capacità di calcolo verso l'alto e verso il basso può essere utile per gli esperimenti sulle prestazioni e le modifiche a breve o a lungo termine delle richieste di traffico.

Per modificare il vcore per tutti i nodi del ruolo di lavoro, modificare il dispositivo di scorrimento **Vcore** in **configurazione (per nodo** del ruolo di lavoro). Il vcore del nodo coordinatore può essere regolato in modo indipendente. Regolare il dispositivo di scorrimento **Vcore** in  **configurazione (nodo coordinatore)**.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
- Ribilanciare le partizioni della [tabella distribuita](howto-hyperscale-scale-rebalance.md) in modo che tutti i nodi di lavoro possano partecipare alle query parallele
