---
title: Eseguire la migrazione della configurazione del pool di batch dai servizi cloud alle macchine virtuali
description: Informazioni su come aggiornare la configurazione del pool alla configurazione più recente e consigliata
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200568"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Eseguire la migrazione della configurazione del pool di batch dai servizi cloud alla macchina virtuale

Attualmente, i pool di batch possono essere creati tramite [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) o [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). È consigliabile usare solo la configurazione della macchina virtuale, perché questa configurazione supporta tutte le funzionalità batch.

I pool di configurazione dei servizi cloud non supportano alcune delle funzionalità batch correnti e non supportano le funzionalità aggiunte di recente. Non sarà possibile creare nuovi pool ' CloudServiceConfiguration ' o aggiungere nuovi nodi ai pool esistenti dopo il [29 febbraio 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/).

Se le soluzioni batch attualmente utilizzano pool ' cloudServiceConfiguration ', è consigliabile passare a' virtualMachineConfiguration ' il prima possibile. Ciò consentirà di trarre vantaggio da tutte le funzionalità batch, ad esempio una selezione ampliata [di serie di VM](batch-pool-vm-sizes.md), VM Linux, [contenitori](batch-docker-container-workloads.md), [Azure Resource Manager reti virtuali](batch-virtual-network.md)e [crittografia del disco del nodo](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Creare un pool usando la configurazione della macchina virtuale

Non è possibile passare da un pool attivo esistente che usa ' cloudServiceConfiguration ' per usare ' virtualMachineConfiguration '. Sarà invece necessario creare nuovi pool. Dopo aver creato i nuovi pool "virtualMachineConfiguration" e aver replicato tutti i processi e le attività, è possibile eliminare il vecchio ' cloudServiceConfiguration'pools ' che non si sta più usando.

Tutte le API batch, gli strumenti da riga di comando, il portale di Azure e l'interfaccia utente di Batch Explorer consentono di creare pool con ' virtualMachineConfiguration '.

Per una procedura dettagliata del processo di creazione di pool che usano "virtualMachineConfiguration", vedere l'esercitazione su [.NET](tutorial-parallel-dotnet.md) o l' [esercitazione su Python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Differenze di configurazione del pool

Di seguito sono riportate alcune delle differenze principali tra le due configurazioni:

- i nodi del pool ' cloudServiceConfiguration ' usano solo il sistema operativo Windows. i pool ' virtualMachineConfiguration ' possono usare il sistema operativo Linux o Windows.
- Rispetto ai pool ' cloudServiceConfiguration ', i pool ' virtualMachineConfiguration ' hanno un set più completo di funzionalità, ad esempio il supporto dei contenitori, i dischi dati e la crittografia del disco.
- I tempi di avvio e di eliminazione del pool e del nodo possono variare leggermente tra i pool ' cloudServiceConfiguration ' e i pool ' virtualMachineConfiguration '.
- i nodi del pool ' virtualMachineConfiguration ' usano dischi del sistema operativo gestiti. Il [tipo di disco gestito](../virtual-machines/disks-types.md) usato per ogni nodo dipende dalle dimensioni della macchina virtuale scelte per il pool. Se per il pool è specificata la dimensione della VM, ad esempio "Standard_D2s_v3", viene usata un'unità SSD Premium. Se viene specificata una dimensione della macchina virtuale "non s", ad esempio "Standard_D2_v3", viene usato un disco rigido standard.

   > [!IMPORTANT]
   > Come per le macchine virtuali e i set di scalabilità di macchine virtuali, il disco gestito del sistema operativo usato per ogni nodo comporta un costo, che è aggiuntivo per il costo delle macchine virtuali. Non è previsto alcun costo del disco del sistema operativo per i nodi ' cloudServiceConfiguration ', perché il disco del sistema operativo viene creato nell'unità SSD locale dei nodi.

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory pool di attività personalizzati

I pool di Azure Batch possono essere utilizzati per eseguire Data Factory attività personalizzate. È necessario eliminare tutti i pool ' cloudServiceConfiguration ' usati per eseguire attività personalizzate e creare nuovi pool ' virtualMachineConfiguration '.

Quando si creano i nuovi pool per eseguire Data Factory attività personalizzate, attenersi alle procedure seguenti:

- Sospendere tutte le pipeline prima di creare i nuovi pool ed eliminare quelli precedenti per assicurarsi che nessuna esecuzione venga interrotta.
- Lo stesso ID del pool può essere usato per evitare le modifiche alla configurazione del servizio collegato.
- Riprendere le pipeline quando sono stati creati nuovi pool.

Per altre informazioni sull'uso di Azure Batch per eseguire Data Factory attività personalizzate, vedere [Azure batch servizio collegato](../data-factory/compute-linked-services.md#azure-batch-linked-service) e  [attività personalizzate in una pipeline Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [configurazioni del pool](nodes-and-pools.md#configurations).
- Altre informazioni sulle [procedure](best-practices.md#pools)consigliate per il pool.
- Vedere le informazioni di riferimento sull'API REST per l' [aggiunta di pool](/rest/api/batchservice/pool/add) e [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).