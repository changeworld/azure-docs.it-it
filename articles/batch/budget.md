---
title: Ottenere l'analisi dei costi e impostare i budget per Azure Batch
description: Informazioni su come ottenere un'analisi dei costi, impostare un budget e ridurre i costi per le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro di batch.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99091328"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Ottenere l'analisi dei costi e impostare i budget per Azure Batch

Questo argomento consente di comprendere i costi che possono essere associati a Azure Batch, come impostare un budget per un pool o un account batch e come ridurre i costi per i carichi di lavoro di batch.

## <a name="understand-costs-associated-with-batch-resources"></a>Informazioni sui costi associati alle risorse batch

Non sono previsti costi per l'uso di Azure Batch stesso, anche se possono essere addebitati costi per le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro di batch. È possibile che i costi siano sostenuti dalle macchine virtuali (VM) in un pool, dal trasferimento dei dati dalla VM o da eventuali dati di input o di output archiviati nel cloud.

### <a name="virtual-machines"></a>Macchine virtuali

Le macchine virtuali sono la risorsa più significativa usata per l'elaborazione batch. Il costo di uso delle macchine virtuali per Batch viene calcolato in base al tipo, alla quantità e alla durata dell'uso. Le opzioni di fatturazione delle macchine virtuali includono il [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o la [prenotazione](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento anticipato). Entrambe le opzioni di pagamento hanno vantaggi diversi a seconda del carico di lavoro di calcolo e influiscono in modo diverso sulla fattura.

Ogni VM in un pool creato con la [configurazione della macchina virtuale](nodes-and-pools.md#virtual-machine-configuration) ha un disco del sistema operativo associato che usa dischi gestiti di Azure. I dischi gestiti di Azure presentano costi aggiuntivi e anche altri livelli di prestazioni del disco hanno costi diversi.

### <a name="storage"></a>Archiviazione

Quando si distribuiscono applicazioni ai nodi Batch (VM) tramite [pacchetti dell'applicazione](batch-application-packages.md), vengono inoltre addebitati i costi per le risorse di Archiviazione di Azure usate dai pacchetti dell'applicazione. Viene addebitata anche l'archiviazione di tutti i file di input o di output, ad esempio file di risorse e altri dati di log.

In generale, il costo dei dati di archiviazione associati a Batch è molto inferiore al costo delle risorse di calcolo.

### <a name="networking-resources"></a>Risorse di rete

I pool di batch usano risorse di rete, alcune delle quali presentano costi associati. In particolare, per i pool di configurazione delle macchine virtuali vengono usati i bilanciamento del carico standard, che richiedono indirizzi IP statici. I servizi di bilanciamento del carico usati da batch sono visibili per gli [account](accounts.md#batch-accounts) configurati in modalità sottoscrizione utente, ma non quelli in modalità servizio batch.

I bilanciamento del carico standard comportano addebiti per tutti i dati passati da e verso le VM del pool di batch. Selezionare le API batch che recuperano i dati dai nodi del pool (ad esempio Ottieni attività/file nodo), i pacchetti dell'applicazione di attività, i file di risorse/output e le immagini del contenitore comporteranno anche addebiti.

### <a name="additional-services"></a>Servizi aggiuntivi

A seconda dei servizi usati con la soluzione Batch, è possibile che vengano addebitati costi aggiuntivi. Per determinare il costo di ogni servizio aggiuntivo, fare riferimento al [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/). I servizi comunemente utilizzati con batch che possono avere costi associati includono:

- Application Insights
- Data Factory
- Monitoraggio di Azure
- Rete virtuale
- Macchine virtuali con applicazioni grafiche

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Visualizzazione dell'analisi dei costi e creazione di un budget per un pool

Nella portale di Azure è possibile creare budget e avvisi di spesa per i pool di batch o gli account batch. I budget e gli avvisi sono utili per notificare agli stakeholder i rischi di overspending, anche se è possibile che si verifichi un ritardo nella spesa degli avvisi e un budget leggermente superiore.

> [!NOTE]
> Il pool in questo esempio usa la **configurazione della macchina virtuale**, consigliata per la maggior parte dei pool e prevede costi basati sulla struttura dei prezzi delle macchine virtuali. I pool che usano la **configurazione dei servizi cloud** vengono addebitati in base alla struttura dei prezzi dei servizi cloud.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Visualizzazione dell'analisi dei costi per un pool di batch

1. Nella portale di Azure digitare o selezionare **Gestione costi e fatturazione** .
1. Selezionare la sottoscrizione nella sezione **ambiti di fatturazione** .
1. In **Gestione dei costi** selezionare **Analisi dei costi**.
1. Selezionare **Aggiungi filtro**. Nel primo elenco a discesa selezionare **risorsa**
1. Nel secondo elenco a discesa selezionare il pool di Batch. Quando il pool è selezionato, verrà visualizzata l'analisi dei costi per il pool, in modo analogo all'esempio illustrato di seguito.
    ![Screenshot che illustra l'analisi dei costi di un pool nell'portale di Azure.](./media/batch-budget/pool-cost-analysis.png)

L'analisi dei costi risultante mostra il costo del pool, nonché le risorse che contribuiscono a questo costo. In questo esempio, le macchine virtuali usate nel pool sono la risorsa più costosa.

### <a name="create-a-budget-for-a-batch-pool"></a>Creare un budget per un pool di batch

1. Nella pagina **analisi costi** selezionare **budget: None**.
1. Selezionare **Crea nuovo budget >**.
1. Utilizzare la finestra risultante per configurare un budget specifico per il pool. Per altre informazioni, vedere [esercitazione: creare e gestire i budget di Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Ridurre i costi associati alla Azure Batch

A seconda dello scenario, è possibile che si desideri ridurre il più possibile i costi. Prendere in considerazione l'uso di una o più di queste strategie per ottimizzare l'efficienza dei carichi di lavoro e ridurre i costi potenziali.

### <a name="use-low-priority-virtual-machines"></a>Usare macchine virtuali con priorità bassa

Le [macchine virtuali con priorità bassa](batch-low-pri-vms.md) riducono il costo dei carichi di lavoro di batch sfruttando la capacità di calcolo in eccesso in Azure. Quando si specificano le macchine virtuali con priorità bassa nei pool, Batch può usare questa capacità in eccesso per eseguire il carico di lavoro. Quando si usano macchine virtuali con priorità bassa anziché macchine virtuali dedicate, è possibile che si verifichi un notevole risparmio sui costi.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Selezionare un tipo di disco del sistema operativo della macchina virtuale standard

Azure offre più [tipi di dischi del sistema operativo VM](../virtual-machines/disks-types.md). La maggior parte delle serie di macchine virtuali presentano dimensioni che supportano sia l'archiviazione standard che l'archiviazione premium. Quando si selezionano le dimensioni della macchina virtuale per un pool, batch configura i dischi del sistema operativo SSD Premium. Quando viene selezionata la dimensione della macchina virtuale "non s", viene usato il tipo di disco HDD standard più economico. Ad esempio, i dischi del sistema operativo SSD Premium vengono usati per `Standard_D2s_v3`, mentre i dischi del sistema operativo HDD standard vengono usati per `Standard_D2_v3`.

SSD Premium dischi del sistema operativo sono più costosi, ma offrono prestazioni superiori. Le macchine virtuali con dischi Premium possono iniziare leggermente più rapidamente rispetto alle macchine virtuali con dischi standard del sistema operativo HDD. Con batch, il disco del sistema operativo spesso non viene usato molto, perché le applicazioni e i file delle attività si trovano nel disco SSD temporaneo della macchina virtuale. Per questo motivo, è spesso possibile selezionare le dimensioni della macchina virtuale "non-s" per evitare di pagare il costo maggiore per l'unità SSD Premium di cui viene effettuato il provisioning quando si specifica la dimensione di una VM.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Prenotazioni di acquisto per le istanze di macchine virtuali

Se si prevede di usare batch per un lungo periodo di tempo, è possibile ridurre il costo delle macchine virtuali usando le [prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) per i carichi di lavoro. Una tariffa di prenotazione è notevolmente inferiore rispetto a una tariffa con pagamento in base al consumo. Le istanze di macchine virtuali usate senza prenotazione vengono addebitate in base alla tariffa con pagamento in base al consumo. Quando si acquista una prenotazione, viene applicato lo sconto relativo alla prenotazione.

### <a name="use-automatic-scaling"></a>Usare il ridimensionamento automatico

Il [ridimensionamento automatico](batch-automatic-scaling.md) ridimensiona in modo dinamico il numero di macchine virtuali nel pool di Batch in base alle esigenze del processo corrente. Ridimensionando il pool in base alla durata di un processo, il ridimensionamento automatico garantisce che le macchine virtuali vengano aumentate e usate solo quando è presente un processo da eseguire. Al termine del processo o quando non sono presenti processi, le macchine virtuali vengono automaticamente ridimensionate per ridurre le risorse di calcolo. Il ridimensionamento consente di ridurre il costo complessivo della soluzione Batch usando solo le risorse necessarie.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Gestione costi di Azure e fatturazione](../cost-management-billing/cost-management-billing-overview.md)
- Informazioni sull' [uso di macchine virtuali con priorità bassa con batch](batch-low-pri-vms.md).
