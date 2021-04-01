---
title: Rete virtuale gestita
description: Articolo che illustra la rete virtuale gestita in Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/18/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f55251932c8aa8f632bd3b498943ac722f006dee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98569912"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Rete virtuale gestita di Azure Synapse Analytics

Questo articolo illustra la rete virtuale gestita in Azure Synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Rete virtuale dell'area di lavoro gestita

Durante la creazione dell'area di lavoro di Azure Synapse, è possibile scegliere di associarla a una rete virtuale di Microsoft Azure. La rete virtuale associata all'area di lavoro è gestita da Azure Synapse. Questa rete virtuale è definita *rete virtuale dell'area di lavoro gestita*.

La rete virtuale dell'area di lavoro gestita è utile per quattro motivi:

- Con una rete virtuale dell'area di lavoro gestita è possibile scaricare su Azure Synapse la gestione della rete virtuale.
- Non è necessario configurare regole NSG in ingresso sulle reti virtuali per consentire al traffico di gestione di Azure Synapse di entrare nella rete virtuale. Un'errata configurazione di queste regole NSG causa un'interruzione del servizio per i clienti.
- Non è necessario creare una subnet per i cluster Spark in base ai picchi del carico.
- La rete virtuale dell'area di lavoro gestita, insieme agli endpoint privati gestiti, protegge dall'esfiltrazione dei dati. È possibile creare endpoint privati gestiti solo in un'area di lavoro a cui è associata una rete virtuale dell'area di lavoro gestita.

La creazione di un'area di lavoro con una rete virtuale dell'area di lavoro gestita associata garantisce che l'area di lavoro sia isolata da altre aree di lavoro in rete. Azure Synapse fornisce varie funzionalità di analisi in un'area di lavoro: integrazione dei dati, pool di Apache Spark serverless, pool SQL dedicato e pool SQL serverless.

Se l'area di lavoro dispone di una rete virtuale dell'area di lavoro gestita, le risorse di integrazione dei dati e di Spark vengono distribuite in questa rete. Una rete virtuale dell'area di lavoro gestita fornisce anche l'isolamento a livello di utente per le attività di Spark, dal momento che ogni cluster Spark è isolato nella propria subnet.

Il pool SQL dedicato e il pool SQL serverless sono funzionalità multi-tenant e pertanto si trovano all'esterno della rete virtuale dell'area di lavoro gestita. Le comunicazioni all'interno dell'area di lavoro con il pool SQL dedicato e il pool SQL serverless usano collegamenti privati di Azure. Questi collegamenti privati vengono creati automaticamente quando si crea un'area di lavoro con una rete virtuale dell'area di lavoro gestita associata.

>[!IMPORTANT]
>Dopo aver creato l'area di lavoro, non è più possibile modificare questa configurazione dell'area di lavoro. Ad esempio, non è possibile riconfigurare un'area di lavoro a cui non è associata una rete virtuale dell'area di lavoro gestita e associarle una rete virtuale. Analogamente, non è possibile riconfigurare un'area di lavoro a cui è associata una rete virtuale dell'area di lavoro gestita e rimuovere l'associazione della rete virtuale.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Creare un'area di lavoro di Azure Synapse con una rete virtuale dell'area di lavoro gestita

Se non è già stato fatto, registrare il provider di risorse di rete. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse. Scegliere *Microsoft.Network* dall'elenco dei provider di risorse quando si [esegue la registrazione](../../azure-resource-manager/management/resource-providers-and-types.md).

Per creare un'area di lavoro di Azure Synapse con una rete virtuale dell'area di lavoro gestita associata, selezionare la scheda **Rete** nel portale di Azure e quindi la casella di controllo **Abilita rete virtuale gestita**.

Se si lascia deselezionata la casella di controllo, all'area di lavoro non verrà associata una rete virtuale.

>[!IMPORTANT]
>È possibile usare i collegamenti privati solo in un'area di lavoro con una rete virtuale dell'area di lavoro gestita.

![Rete virtuale dell'area di lavoro gestita abilitata](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

Dopo aver scelto di associare una rete virtuale dell'area di lavoro gestita all'area di lavoro, è possibile proteggersi da dati exfiltration consentendo la connettività in uscita dalla rete virtuale dell'area di lavoro gestita solo alle destinazioni approvate usando [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md). Selezionare **Sì** per limitare il traffico in uscita dalla rete virtuale dell'area di lavoro gestita alle destinazioni tramite endpoint privati gestiti. 


>[!IMPORTANT]
>Metastore è disabilitato nelle aree di lavoro sinapsi con rete virtuale gestita con la protezione Exfiltration dati abilitata. Non sarà possibile usare Spark SQL in queste aree di lavoro.

![Traffico in uscita con endpoint privati gestiti](./media/synapse-workspace-managed-vnet/select-outbound-connectivity.png)

Selezionare **No** per consentire il traffico in uscita dall'area di lavoro a qualsiasi destinazione.

È anche possibile controllare le destinazioni in cui vengono creati gli endpoint privati gestiti dall'area di lavoro sinapsi di Azure. Per impostazione predefinita, sono consentiti endpoint privati gestiti per le risorse nello stesso tenant di AAD a cui appartiene la sottoscrizione. Se si vuole creare un endpoint privato gestito a una risorsa in un tenant AAD diverso da quello a cui appartiene la sottoscrizione, è possibile aggiungere il tenant di AAD selezionando **+ Aggiungi**. È possibile selezionare il tenant AAD dall'elenco a discesa o immettere manualmente l'ID tenant di AAD.

![Aggiungere altri tenant di AAD](./media/synapse-workspace-managed-vnet/add-additional-azure-active-directory-tenants.png)

Dopo aver creato l'area di lavoro, è possibile verificare se l'area di lavoro di Azure sinapsi è associata a una rete virtuale dell'area di lavoro gestita selezionando **Panoramica** da portale di Azure.

![Panoramica dell'area di lavoro nel portale di Azure](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Passaggi successivi

Creare un'[area di lavoro di Azure Synapse](../quickstart-create-workspace.md)

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)

[Creare endpoint privati gestiti per le origini dati](./how-to-create-managed-private-endpoints.md)
