---
title: Analisi dei costi e budget
description: Informazioni su come ottenere un'analisi dei costi, impostare un budget e ridurre i costi per le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro di batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679315"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analisi dei costi e budget per Azure Batch

Non sono previsti costi per l'uso di Azure Batch stesso, anche se possono essere addebitati costi per le risorse di calcolo sottostanti e le licenze software usate per eseguire i carichi di lavoro di batch. È possibile che i costi siano sostenuti dalle macchine virtuali (VM) in un pool, dal trasferimento dei dati dalla VM o da eventuali dati di input o di output archiviati nel cloud. Questo argomento consente di capire dove provengono i costi, come impostare un budget per un pool o un account batch e come ridurre i costi per i carichi di lavoro di batch.

## <a name="batch-resources"></a>Risorse di Batch

Le macchine virtuali sono la risorsa più significativa usata per l'elaborazione batch. Il costo di uso delle macchine virtuali per Batch viene calcolato in base al tipo, alla quantità e alla durata dell'uso. Le opzioni di fatturazione delle macchine virtuali includono il [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o la [prenotazione](../cost-management-billing/reservations/save-compute-costs-reservations.md) (pagamento anticipato). Entrambe le opzioni di pagamento hanno vantaggi diversi a seconda del carico di lavoro di calcolo e influiscono in modo diverso sulla fattura.

Quando si distribuiscono applicazioni ai nodi Batch (VM) tramite [pacchetti dell'applicazione](batch-application-packages.md), vengono inoltre addebitati i costi per le risorse di Archiviazione di Azure usate dai pacchetti dell'applicazione. Viene addebitata anche l'archiviazione di tutti i file di input o di output, ad esempio file di risorse e altri dati di log. In generale, il costo dei dati di archiviazione associati a Batch è molto inferiore al costo delle risorse di calcolo. Ogni VM in un pool creato con la [configurazione della macchina virtuale](nodes-and-pools.md#virtual-machine-configuration) ha un disco del sistema operativo associato che usa dischi gestiti di Azure. I dischi gestiti di Azure presentano costi aggiuntivi e anche altri livelli di prestazioni del disco hanno costi diversi.

I pool di Batch usano le risorse di rete. In particolare, per i pool **VirtualMachineConfiguration** vengono usati i bilanciamenti del carico standard, che richiedono indirizzi IP statici. I servizi di bilanciamento del carico usati da Batch sono visibili per gli account di **sottoscrizione utente**, ma non sono visibili per account del **servizio Batch**. I servizi di bilanciamento del carico standard comportano addebiti per tutti i dati trasmessi da e verso le macchine virtuali del pool di Batch; le API Batch che recuperano i dati dai nodi del pool (ad esempio Get Task/Node File), i pacchetti dell'applicazione di attività, i file di risorse/output e le immagini del contenitore comporteranno addebiti.

### <a name="additional-services"></a>Servizi aggiuntivi

I servizi che non includono macchine virtuali e spazio di archiviazione possono essere specificati nel costo dell'account Batch.

Altri servizi comunemente usati con Batch possono includere:

- Application Insights
- Data Factory
- Monitoraggio di Azure
- Rete virtuale
- Macchine virtuali con applicazioni grafiche

A seconda dei servizi usati con la soluzione Batch, è possibile che vengano addebitati costi aggiuntivi. Per determinare il costo di ogni servizio aggiuntivo, fare riferimento al [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analisi dei costi e budget per un pool

Nella portale di Azure è possibile creare budget e avvisi di spesa per i pool di batch o gli account batch. I budget e gli avvisi sono utili per notificare agli stakeholder i rischi di overspending, anche se è possibile che si verifichi un ritardo nella spesa degli avvisi e un budget leggermente superiore.

In questo esempio viene illustrata l'analisi dei costi di un singolo pool di Batch.

1. Nella portale di Azure digitare o selezionare **Gestione costi e fatturazione** .
1. Selezionare la sottoscrizione nella sezione **ambiti di fatturazione** .
1. In **Gestione dei costi** selezionare **Analisi dei costi**.
1. Selezionare **Aggiungi filtro**. Nel primo elenco a discesa selezionare **risorsa**
1. Nel secondo elenco a discesa selezionare il pool di Batch. Quando il pool è selezionato, verrà visualizzata l'analisi dei costi per il pool, in modo analogo all'esempio illustrato di seguito.
    ![Screenshot che illustra l'analisi dei costi di un pool nell'portale di Azure.](./media/batch-budget/pool-cost-analysis.png)

L'analisi dei costi risultante mostra il costo del pool, nonché le risorse che contribuiscono a questo costo. In questo esempio, le macchine virtuali usate nel pool sono la risorsa più costosa.

Per creare un budget per il pool, selezionare **budget: None**, quindi selezionare **crea nuovo budget >**. Usare ora la finestra per [configurare un budget](../cost-management-billing/costs/tutorial-acm-create-budgets.md) specifico per il pool.

> [!NOTE]
> Azure Batch è basato sulla tecnologia di Servizi cloud di Azure e di Macchine virtuali di Azure. Scegliendo la **configurazione di Servizi cloud**, i costi verranno addebitati in base alla struttura dei prezzi di Servizi cloud. Scegliendo la **configurazione della macchina virtuale**, i costi verranno addebitati in base alla struttura dei prezzi di Macchine virtuali. Nell'esempio riportato in questa pagina viene utilizzata la **configurazione della macchina virtuale**, consigliata per la maggior parte dei pool di batch.

## <a name="minimize-cost"></a>Ridurre i costi

L'uso di più macchine virtuali e servizi di Azure per periodi prolungati di tempo può essere costoso. Prendere in considerazione l'uso di queste strategie per ottimizzare l'efficienza dei carichi di lavoro e ridurre i costi.

### <a name="low-priority-virtual-machines"></a>Macchine virtuali con priorità bassa

Le [macchine virtuali con priorità bassa](batch-low-pri-vms.md) riducono il costo dei carichi di lavoro di batch sfruttando la capacità di calcolo in eccesso in Azure. Quando si specificano le macchine virtuali con priorità bassa nei pool, Batch può usare questa capacità in eccesso per eseguire il carico di lavoro. Quando si usano macchine virtuali con priorità bassa anziché macchine virtuali dedicate, è possibile che si verifichi un notevole risparmio sui costi.

### <a name="virtual-machine-os-disk-type"></a>Tipo di disco del sistema operativo per macchina virtuale

Azure offre più [tipi di dischi del sistema operativo VM](../virtual-machines/disks-types.md). La maggior parte delle serie di macchine virtuali presentano dimensioni che supportano sia l'archiviazione standard che l'archiviazione premium. Quando si selezionano le dimensioni della macchina virtuale per un pool, batch configura i dischi del sistema operativo SSD Premium. Quando viene selezionata la dimensione della macchina virtuale "non s", viene usato il tipo di disco HDD standard più economico. Ad esempio, i dischi del sistema operativo SSD Premium vengono usati per `Standard_D2s_v3`, mentre i dischi del sistema operativo HDD standard vengono usati per `Standard_D2_v3`.

SSD Premium dischi del sistema operativo sono più costosi, ma offrono prestazioni superiori. Le macchine virtuali con dischi Premium possono iniziare leggermente più rapidamente rispetto alle macchine virtuali con dischi standard del sistema operativo HDD. Con batch, il disco del sistema operativo spesso non viene usato molto, perché le applicazioni e i file delle attività si trovano nel disco SSD temporaneo della macchina virtuale. Per questo motivo, è spesso possibile selezionare le dimensioni della macchina virtuale "non-s" per evitare di pagare il costo maggiore per l'unità SSD Premium di cui viene effettuato il provisioning quando si specifica la dimensione di una VM.

### <a name="reserved-virtual-machine-instances"></a>Istanze di macchine virtuale riservate

Se si prevede di usare batch per un lungo periodo di tempo, è possibile ridurre il costo delle macchine virtuali usando le [prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) per i carichi di lavoro. Una tariffa di prenotazione è notevolmente inferiore rispetto a una tariffa con pagamento in base al consumo. Le istanze di macchine virtuali usate senza prenotazione vengono addebitate in base alla tariffa con pagamento in base al consumo. Quando si acquista una prenotazione, viene applicato lo sconto relativo alla prenotazione.

### <a name="automatic-scaling"></a>Ridimensionamento automatico

Il [ridimensionamento automatico](batch-automatic-scaling.md) ridimensiona in modo dinamico il numero di macchine virtuali nel pool di Batch in base alle esigenze del processo corrente. Ridimensionando il pool in base alla durata di un processo, il ridimensionamento automatico garantisce che le macchine virtuali vengano aumentate e usate solo quando è presente un processo da eseguire. Al termine del processo o quando non sono presenti processi, le macchine virtuali vengono automaticamente ridimensionate per ridurre le risorse di calcolo. Il ridimensionamento consente di ridurre il costo complessivo della soluzione Batch usando solo le risorse necessarie.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione e il monitoraggio di soluzioni Batch.  
- Informazioni sull' [uso di macchine virtuali con priorità bassa con batch](batch-low-pri-vms.md).
