---
title: Eseguire la migrazione della configurazione del pool di batch dai servizi cloud alle macchine virtuali
description: Informazioni su come aggiornare la configurazione del pool alla configurazione più recente e consigliata
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: 52e1762dc8e81b3eb7e1bce388d91dfd2c76191a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937713"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Eseguire la migrazione della configurazione del pool di batch dai servizi cloud alle macchine virtuali

I pool di batch possono essere creati usando [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) o [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' è la configurazione consigliata perché supporta tutte le funzionalità batch. i pool ' cloudServiceConfiguration ' non supportano tutte le funzionalità e non sono state pianificate nuove funzionalità.

Se si usano i pool ' cloudServiceConfiguration ', si consiglia di passare all'uso dei pool ' virtualMachineConfiguration '. Questo articolo descrive come eseguire la migrazione alla configurazione ' virtualMachineConfiguration ' consigliata.

## <a name="new-pools-are-required"></a>Sono necessari nuovi pool

Non è possibile aggiornare i pool attivi esistenti da' cloudServiceConfiguration ' a' virtualMachineConfiguration '. è necessario creare nuovi pool. La creazione di pool con ' virtualMachineConfiguration ' è supportata da tutte le API batch, dagli strumenti da riga di comando portale di Azure e dall'interfaccia utente Batch Explorer.

Le esercitazioni su [.NET](tutorial-parallel-dotnet.md) e [Python](tutorial-parallel-python.md) forniscono esempi di creazione di pool con "virtualMachineConfiguration".

## <a name="pool-configuration-differences"></a>Differenze di configurazione del pool

Quando si aggiorna la configurazione del pool, è necessario considerare quanto segue:

- i nodi del pool ' cloudServiceConfiguration ' sono sempre sistemi operativi Windows, i pool ' virtualMachineConfiguration ' possono essere di tipo Linux o Windows.
- Rispetto ai pool ' cloudServiceConfiguration ', i pool ' virtualMachineConfiguration ' hanno un set più completo di funzionalità, ad esempio il supporto dei contenitori, i dischi dati e la crittografia del disco.
- i nodi del pool ' virtualMachineConfiguration ' usano dischi del sistema operativo gestiti. Il [tipo di disco gestito](../virtual-machines/disks-types.md) usato per ogni nodo dipende dalle dimensioni della macchina virtuale scelte per il pool. Se per il pool è specificata la dimensione della VM, ad esempio "Standard_D2s_v3", viene usata un'unità SSD Premium. Se viene specificata una dimensione della macchina virtuale "non s", ad esempio "Standard_D2_v3", viene usato un disco rigido standard.

   > [!IMPORTANT]
   > Come per le macchine virtuali e i set di scalabilità di macchine virtuali, il disco gestito del sistema operativo usato per ogni nodo comporta un costo, che è aggiuntivo per il costo delle macchine virtuali. Non è previsto alcun costo del disco del sistema operativo per i nodi ' cloudServiceConfiguration ' durante la creazione del disco del sistema operativo nell'unità SSD locale dei nodi.

- I tempi di avvio e di eliminazione del pool e del nodo possono variare leggermente tra i pool ' cloudServiceConfiguration ' e i pool ' virtualMachineConfiguration '.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [configurazioni del pool](nodes-and-pools.md#configurations).
- Altre informazioni sulle [procedure](best-practices.md#pools)consigliate per il pool.
- Informazioni di riferimento sull'API REST per l' [aggiunta del pool](https://docs.microsoft.com/rest/api/batchservice/pool/add) e [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration).
