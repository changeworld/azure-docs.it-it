---
title: Eseguire i carichi di lavoro su macchine virtuali convenienti con priorità bassa
description: Informazioni su come eseguire il provisioning di macchine virtuali con priorità bassa per ridurre i costi dei carichi di lavoro di Azure Batch.
author: mscurrell
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: seodec18
ms.openlocfilehash: cafc7216e8112640f823ecee1aea055ab78b3fc6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098469"
---
# <a name="use-low-priority-vms-with-batch"></a>Usare le macchine virtuali con priorità bassa in Batch

Azure Batch offre macchine virtuali con priorità bassa per ridurre i costi dei carichi di lavoro Batch. Le macchine virtuali con priorità bassa rendono possibili nuovi tipi di carichi di lavoro Batch consentendo di usare una grande quantità di potenza di calcolo a un costo molto basso.

Le macchine virtuali con priorità bassa sfruttano la capacità in eccesso di Azure. Quando si specificano le macchine virtuali con priorità bassa nei pool, Azure Batch può usare questa capacità in eccesso, quando disponibile.

Il compromesso per l'uso di macchine virtuali con priorità bassa è che queste macchine virtuali potrebbero non essere sempre disponibili per l'allocazione o che potrebbero essere interrotte in qualsiasi momento, a seconda della capacità disponibile. Per questo motivo, le macchine virtuali con priorità bassa sono più adatte per i carichi di lavoro di elaborazione batch e asincroni, in cui il tempo di completamento del processo è flessibile e il lavoro viene distribuito tra più macchine virtuali.

Le macchine virtuali con priorità bassa sono caratterizzate da un prezzo notevolmente ridotto rispetto alle macchine virtuali dedicate. Per i dettagli sui prezzi vedere [Prezzi dei Batch](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Le macchine virtuali Spot](https://azure.microsoft.com/pricing/spot/) sono ora disponibili per [macchine virtuali a istanza singola](../virtual-machines/spot-vms.md) e [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/use-spot.md). Le macchine virtuali Spot sono un'evoluzione delle macchine virtuali con priorità bassa, ma si differenziano per i prezzi e, durante l'allocazione di macchine virtuali Spot, è possibile impostare un prezzo massimo facoltativo.
>
>I pool di Azure Batch inizieranno a supportare le VM spot in futuro, con le nuove versioni delle [API e degli strumenti di batch](./batch-apis-tools.md). Quando è disponibile il supporto per la VM spot, le macchine virtuali con priorità bassa saranno deprecate. continueranno a essere supportate usando le API e le versioni degli strumenti correnti per almeno 12 mesi, per consentire un tempo sufficiente per la migrazione alle macchine virtuali.
>
> Le VM spot saranno supportate solo per i pool di configurazione delle macchine virtuali. Per usare le VM spot, è necessario eseguire la migrazione dei pool di configurazione dei servizi cloud [ai pool di configurazione delle macchine virtuali](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="batch-support-for-low-priority-vms"></a>Supporto batch per le macchine virtuali con priorità bassa

Azure Batch offre diverse funzionalità che semplificano l'uso e i vantaggi dalle macchine virtuali con priorità bassa:

- I pool di batch può contenere sia macchine virtuali dedicate che macchine virtuali con priorità bassa. Il numero di ciascun tipo di macchina virtuale può essere specificato al momento della creazione di un nuovo pool o della modifica di un pool esistente, tramite l'operazione di ridimensionamento esplicito o la scalabilità automatica. L'invio di attività e processi può rimanere invariato indipendentemente dai tipi di macchine virtuali nel pool. È anche possibile configurare un pool in modo che usi solo le macchine virtuali con priorità bassa per eseguire processi nel modo più economico possibile, ma che avvii macchine virtuali dedicate se la capacità scende al di sotto di una soglia minima, per mantenere i processi in esecuzione.
- I pool del servizio Batch rilevano automaticamente il numero di macchine virtuali con priorità bassa. Se le macchine virtuali vengono interrotte o non sono disponibili, batch tenta di sostituire la capacità persa e di tornare alla destinazione.
- Quando le attività vengono interrotte, il servizio Batch rileva e reinserisce automaticamente nella coda le attività da eseguire di nuovo.
- Le macchine virtuali con priorità bassa hanno una quota di vCPU separata che differisce da quella per le macchine virtuali dedicate. La quota per le macchine virtuali con priorità bassa è superiore a quella delle macchine virtuali dedicate, perché le macchine virtuali con priorità bassa sono meno costose. Per altre informazioni, vedere [Quote e limiti del servizio Batch](batch-quota-limit.md#resource-quotas).

> [!NOTE]
> Le macchine virtuali con priorità bassa non sono attualmente supportate per gli account Batch creati in [modalità di sottoscrizione utente](accounts.md).

## <a name="considerations-and-use-cases"></a>Considerazioni e casi d'uso

Molti carichi di lavoro batch sono un'ottima soluzione per le macchine virtuali con priorità bassa. È consigliabile usarli quando i processi sono suddivisi in molte attività parallele o quando si dispone di molti processi che vengono scalati orizzontalmente e distribuiti tra più macchine virtuali.

Esempi di casi d'uso di elaborazione batch adatti all'uso di macchine virtuali con priorità bassa sono:

- **Sviluppo e test**: in particolare, se sono in fase di sviluppo soluzioni su larga scala, è possibile realizzare risparmi significativi. Tutti i tipi di test possono trarre vantaggio, ma i test di carico su larga scala e i test di regressione ne traggono il miglio uso.
- **Integrazione della capacità su richiesta**: le macchine virtuali con priorità bassa possono essere usate per integrare le normali macchine virtuali dedicate. Quando disponibili, i processi possono essere ridimensionati e quindi completati più rapidamente per un costo inferiore; Quando non è disponibile, la linea di base delle macchine virtuali dedicate rimane disponibile.
- **Tempo di esecuzione del processo flessibile**: se c'è flessibilità nei tempi di completamento dei processi, le possibili cadute di capacità vengono considerate tollerabili. I processi delle macchine virtuali con priorità bassa, per giunta, vengono spesso eseguiti più rapidamente e a costi inferiori.

I pool di batch possono essere configurati per l'uso di macchine virtuali con priorità bassa in diversi modi:

- Un pool può usare solo macchine virtuali con priorità bassa. In questo caso, il servizio Batch recupera qualsiasi capacità superata, se disponibile. Questa configurazione è il modo più conveniente per eseguire i processi.
- Le macchine virtuali con priorità bassa possono essere usate in combinazione con macchine virtuali dedicate predefinite di base. Il numero fisso di macchine virtuali dedicate garantisce che ci sia sempre una certa capacità per mantenere l'avanzamento del processo.
- Un pool può usare una combinazione dinamica di VM dedicate e con priorità bassa, in modo che le macchine virtuali con priorità bassa più economiche vengano usate esclusivamente quando disponibili, ma le macchine virtuali dedicate a prezzo pieno vengono aumentate se necessario. Questa configurazione consente di mantenere una quantità minima di capacità disponibile al fine di far avanzare il processo.

Quando si pianifica l'uso di macchine virtuali con priorità bassa, tenere presente quanto segue:

- Per ottimizzare l'uso della capacità in eccedenza in Azure, è possibile aumentare il numero di istanze per i processi appropriati.
- In alcuni casi le macchine virtuali potrebbero non essere disponibili o possono essere superate e ciò comporta una riduzione della capacità per i processi e può provocare un'interruzione e una riesecuzione delle attività.
- Le attività con tempi di esecuzione più brevi tendono a funzionare meglio con macchine virtuali con priorità bassa. I processi con attività più lunghe potrebbero subire maggiormente gli effetti se vengono interrotti. Se le attività con esecuzione prolungata implementano il checkpoint per salvare l'avanzamento durante l'esecuzione, questo effetto può essere ridotto. 
- I processi MPI con tempi di esecuzione lunghi che usano più macchine virtuali non sono adatti all'uso di macchine virtuali con priorità bassa poiché una macchina virtuale superata può causare la necessità riesecuzione del processo.
- I nodi con priorità bassa possono essere contrassegnati come inutilizzabili se [le regole del gruppo di sicurezza di rete (NSG)](batch-virtual-network.md#network-security-groups-specifying-subnet-level-rules) non sono configurate correttamente.

## <a name="create-and-manage-pools-with-low-priority-vms"></a>Creare e gestire pool con macchine virtuali con priorità bassa

Un pool Batch può contenere sia macchine virtuali dedicate sia VM con priorità bassa, definite anche nodi di calcolo. È possibile impostare il numero di nodi di calcolo sia per le macchine virtuali dedicate sia per le VM con priorità bassa. Il numero di nodi di destinazione specifica il numero di macchine virtuali che si desidera avere nel pool.

Ad esempio, per creare un pool con macchine virtuali di Azure (in questo caso VM Linux) con una destinazione di 5 VM dedicate e 20 macchine virtuali con priorità bassa:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

È possibile ottenere il numero corrente di nodi sia per le macchine virtuali dedicate che per quelle con priorità bassa:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

I nodi pool dispongono di una proprietà che indica se il nodo è una macchina virtuale dedicata o con priorità bassa:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Occasionalmente, le macchine virtuali possono essere interrotte. Quando si verifica questa situazione, le attività in esecuzione sulle VM del nodo interrotte vengono riaccodate ed eseguite nuovamente.

Per i pool di configurazione delle macchine virtuali, batch esegue anche le operazioni seguenti:

- Le macchine virtuali interrotte hanno lo stato aggiornato ad **Annullato**. 
- La macchina virtuale viene eliminata in modo efficace e tutti i dati memorizzati in locale nella macchina virtuale andranno persi.
- Un'operazione di elenco dei nodi nel pool restituirà comunque i nodi con precedenza.
- Il pool tenta continuamente raggiungere il numero stabilito di nodi con priorità bassa disponibili. Quando viene individuata una capacità di sostituzione, i nodi mantengono il proprio ID, ma vengono inizializzati di nuovo, attraversando gli stati **Creazione in corso** e **Avvio in corso** prima che siano disponibili per la pianificazione delle attività.
- Il numero delle priorità è disponibile come metrica nel portale di Azure.

## <a name="scale-pools-containing-low-priority-vms"></a>Ridimensionare i pool contenenti macchine virtuali con priorità bassa

Così come avviene per i pool costituiti esclusivamente da macchine virtuali dedicate, è possibile ridimensionare un pool che contiene macchine virtuali con priorità bassa chiamando il metodo di ridimensionamento o mediante la scalabilità automatica.

L'operazione di ridimensionamento del pool richiede un secondo parametro facoltativo che aggiorna il valore di **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

La formula di scalabilità automatica del pool supporta le macchine virtuali con priorità bassa nel modo seguente:

- È possibile ottenere o impostare il valore della variabile definita dal servizio **$TargetLowPriorityNodes**.
- È possibile ottenere il valore della variabile definita dal servizio **$CurrentLowPriorityNodes**.
- È possibile ottenere il valore della variabile definita dal servizio **$PreemptedNodeCount**. Questa variabile restituisce il numero di nodi con lo stato Annullato e consente di aumentare o ridurre il numero di nodi dedicati, a seconda del numero di nodi di annullati che non sono disponibili.

## <a name="configure-jobs-and-tasks"></a>Configurare processi e attività

I processi e le attività richiedono una configurazione aggiuntiva minima per i nodi con priorità bassa. Tenere presente quanto segue:

- La proprietà JobManagerTask di un processo ha una proprietà **AllowLowPriorityNode** . Quando questa proprietà è true, è possibile programmare le attività di gestione dei processi su un nodo dedicato o su un nodo con priorità bassa. Se è false, l'attività del gestore di processi viene pianificata solo per un nodo dedicato.
- La [variabile di ambiente](batch-compute-node-environment-variables.md) AZ_BATCH_NODE_IS_DEDICATED è disponibile per un'applicazione dell'attività in modo che sia in grado di determinare se è in esecuzione con priorità bassa o in un nodo dedicato.

## <a name="view-metrics-for-low-priority-vms"></a>Visualizzare le metriche per le macchine virtuali con priorità bassa

Per i nodi con priorità bassa sono disponibili nuove metriche nel [portale di Azure](https://portal.azure.com). Le metriche sono:

- Numero di nodi a bassa priorità
- Numero di core a bassa priorità
- Numero di nodi annullati

Per visualizzare queste metriche nell'portale di Azure

1. Passare all'account Batch nel portale di Azure.
2. Selezionare **Metrica** dalla sezione **Monitoraggio**.
3. Selezionare la metrica desiderata dall'elenco **metrica** .

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
- Iniziare a pianificare lo spostamento dalle macchine virtuali con priorità bassa alle macchine virtuali Spot. Se si usano macchine virtuali con priorità bassa con i pool di **configurazione dei servizi cloud** , è consigliabile pianificare la migrazione ai pool di [ **configurazione delle macchine virtuali**](nodes-and-pools.md#configurations) .
