---
title: Eseguire attività contemporaneamente per ottimizzare l'uso dei nodi di calcolo Batch
description: Aumentare l'efficienza e ridurre i costi usando un numero minore di nodi di calcolo ed eseguendo attività in parallelo in ogni nodo in un pool Azure Batch dati
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 81648f30a7a02f702dcb189aa7df27e5a82e2b07
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389299"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Eseguire attività contemporaneamente per ottimizzare l'uso dei nodi di calcolo Batch

È possibile ottimizzare l'utilizzo delle risorse in un numero inferiore di nodi di calcolo nel pool eseguendo più attività contemporaneamente in ogni nodo.

Sebbene alcuni scenari funzionino meglio con tutte le risorse di un nodo dedicate a una singola attività, alcuni carichi di lavoro possono visualizzare tempi di processo più brevi e costi inferiori quando più attività condividono tali risorse. Prendere in considerazione gli scenari seguenti:

- **Ridurre al minimo il trasferimento** dei dati per le attività in grado di condividere i dati. È possibile ridurre notevolmente gli addebiti per il trasferimento dei dati copiando i dati condivisi in un numero inferiore di nodi e quindi eseguendo attività in parallelo in ogni nodo. Questa opzione è praticabile soprattutto se i dati da copiare in ogni nodo devono essere trasferiti tra aree geografiche.
- **Ottimizzare l'utilizzo** della memoria per le attività che richiedono una grande quantità di memoria, ma solo durante brevi periodi di tempo e in momenti variabili durante l'esecuzione. È possibile usare un numero minore di istanze dei nodi, ma di dimensioni maggiori e con più memoria per gestire in modo efficiente i picchi. Questi nodi avranno più attività in esecuzione in parallelo in ogni nodo, ma ogni attività può sfruttare la quantità di memoria dei nodi in momenti diversi.
- **Ridurre i limiti dei numeri di nodo** quando è necessaria la comunicazione tra nodi all'interno di un pool. Attualmente, per i pool configurati per la comunicazione tra nodi è previsto un limite di 50 nodi di calcolo, quindi, se ogni nodo nel pool può eseguire attività in parallelo, è possibile eseguire simultaneamente un maggior numero di attività.
- **Replicare un cluster di calcolo locale,** ad esempio quando si sposta per la prima volta un ambiente di calcolo in Azure. Se la soluzione locale corrente esegue più attività per ogni nodo di calcolo, è possibile aumentare il numero massimo di attività dei nodi per rispecchiare maggiormente tale configurazione.

## <a name="example-scenario"></a>Scenario di esempio

Si supponga ad esempio un'applicazione di attività con requisiti di CPU e memoria tali che i [nodi \_ D1 Standard](../cloud-services/cloud-services-sizes-specs.md#d-series) siano sufficienti. Tuttavia, per completare il processo nel tempo necessario, sono necessari 1.000 di questi nodi.

Invece di usare nodi Standard\_D1 con 1 core CPU, è possibile usare nodi [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#d-series) con 16 core ognuno e abilitare l'esecuzione parallela delle attività. Ciò significa che è possibile usare 16 volte meno nodi. Invece di 1.000 nodi, ne sarebbero necessari solo 63. Se sono necessari file di applicazioni di grandi dimensioni o dati di riferimento per ogni nodo, la durata e l'efficienza del processo vengono migliorate, poiché i dati vengono copiati solo in 63 nodi.

## <a name="enable-parallel-task-execution"></a>Abilitare l'esecuzione parallela di attività

I nodi di calcolo per l'esecuzione di attività parallele vengono configurati a livello di pool. Con la libreria Batch .NET impostare la [proprietà CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) quando si crea un pool. Se si usa l'API REST Batch, impostare [l'elemento taskSlotsPerNode](/rest/api/batchservice/pool/add) nel corpo della richiesta durante la creazione del pool.

> [!NOTE]
> È possibile impostare `taskSlotsPerNode` l'elemento e la proprietà [TaskSlotsPerNode solo](/dotnet/api/microsoft.azure.batch.cloudpool) in fase di creazione del pool. Non possono essere modificati dopo la creazione di un pool.

Azure Batch consente di impostare gli slot attività per nodo fino a (4x) il numero di core del nodo. Ad esempio, se il pool è configurato con nodi di grandi dimensioni (quattro core), è possibile impostare il valore di `taskSlotsPerNode` su 16. Tuttavia, indipendentemente dal numero di core del nodo, non è possibile avere più di 256 slot di attività per nodo. Per informazioni dettagliate sul numero di core per ognuna delle dimensioni del nodo, vedere [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md). Per altre informazioni sui limiti del servizio, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

> [!TIP]
> Verificare di tener conto del valore `taskSlotsPerNode` durante la creazione di una [formula di scalabilità automatica](/rest/api/batchservice/pool/enableautoscale) per il pool. Ad esempio, l'impatto di un aumento delle attività per nodo può influire in modo significativo su una formula che valuta `$RunningTasks` . Per altre informazioni, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool Azure Batch dati](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Specificare la distribuzione delle attività

Quando si abilitano attività simultanee, è importante specificare come si desidera distribuire le attività tra i nodi del pool.

La proprietà [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) consente di specificare che le attività vengano assegnate in modo uniforme in tutti i nodi del pool ("distribuzione"). In alternativa, è possibile specificare che più attività possibili vengano assegnate a ciascun nodo prima di essere assegnate a un altro nodo del pool ("imballaggio").

Si consideri ad esempio il pool di nodi [ \_ D14 Standard](../cloud-services/cloud-services-sizes-specs.md#d-series) (nell'esempio precedente) configurato con un valore [CloudPool.TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) pari a 16. Se [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) è configurato con [computeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) di *Pack,* massimizza l'utilizzo di tutti i 16 core di ogni nodo e consente a un [pool](batch-automatic-scaling.md) di scalabilità automatica di rimuovere i nodi inutilizzati (nodi senza attività assegnate) dal pool. Ciò consente di ridurre al minimo l'utilizzo delle risorse e di generare un risparmio sui costi.

## <a name="define-variable-slots-per-task"></a>Definire slot variabili per ogni attività

Un'attività può essere definita con la proprietà [CloudTask.RequiredSlots,](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) specificando il numero di slot necessari per l'esecuzione in un nodo di calcolo. Il valore predefinito è 1. È possibile impostare slot di attività variabili se le attività hanno pesi diversi rispetto all'utilizzo delle risorse nel nodo di calcolo. Ciò consente a ogni nodo di calcolo di avere un numero ragionevole di attività in esecuzione simultanee senza risorse di sistema eccessivo, ad esempio CPU o memoria.

Ad esempio, per un pool con la proprietà , è possibile inviare attività multi-core necessarie a elevato utilizzo di CPU con , mentre altre attività `taskSlotsPerNode = 8` possono essere impostate su `requiredSlots = 8` `requiredSlots = 1` . Quando questo carico di lavoro misto viene pianificato, le attività a elevato utilizzo di CPU verranno eseguite esclusivamente nei nodi di calcolo, mentre altre attività possono essere eseguite simultaneamente (fino a otto attività contemporaneamente) in altri nodi. In questo modo è possibile bilanciare il carico di lavoro tra nodi di calcolo e migliorare l'efficienza di utilizzo delle risorse.

Assicurarsi di non specificare che un'attività sia maggiore di . `requiredSlots` `taskSlotsPerNode` In questo modo l'attività non sarà mai in grado di essere eseguita. Il servizio Batch attualmente non convalida questo conflitto quando si inviano attività perché un processo potrebbe non avere un pool associato in fase di invio o potrebbe essere modificato in un pool diverso disabilitando/ri-abilitando.

> [!TIP]
> Quando si usano slot di attività variabili, è possibile che le attività di grandi dimensioni con più slot necessari non possano essere pianificate temporaneamente perché non sono disponibili slot sufficienti in qualsiasi nodo di calcolo, anche quando sono ancora presenti slot inattivi in alcuni nodi. È possibile aumentare la priorità del processo per queste attività per aumentare le possibilità di concorrenza per gli slot disponibili nei nodi.
>
> Il servizio Batch genera [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) quando non riesce a pianificare l'esecuzione di un'attività e continua a ripetere la pianificazione fino a quando gli slot necessari non diventano disponibili. È possibile restare in ascolto di tale evento per rilevare potenziali problemi di pianificazione delle attività e attenuare di conseguenza.

## <a name="batch-net-example"></a>Esempio per Batch .NET

I [frammenti di codice](/dotnet/api/microsoft.azure.batch) dell'API Batch .NET seguenti illustrano come creare un pool con più slot di attività per nodo e come inviare un'attività con gli slot necessari.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Creare un pool con più slot attività per nodo

Questo frammento di codice mostra una richiesta di creazione di un pool contenente quattro nodi, con quattro slot di attività consentiti per ogni nodo. Specifica i criteri di pianificazione delle attività che definiscono le attività da inserire in ogni nodo prima di assegnarle a un altro nodo nel pool.

Per altre informazioni sull'aggiunta di pool con l'API Batch .NET, vedere [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Creare un'attività con gli slot necessari

Questo frammento di codice crea un'attività con non predefinita `requiredSlots` . Questa attività verrà eseguita solo quando in un nodo di calcolo sono disponibili slot gratuiti sufficienti.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Elencare i nodi di calcolo con conteggi per l'esecuzione di attività e slot

Questo frammento di codice elenca tutti i nodi di calcolo nel pool e visualizza i conteggi per l'esecuzione di attività e slot di attività per nodo.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Elencare i conteggi delle attività per il processo

Questo frammento di codice ottiene i conteggi delle attività per il processo, che include sia il conteggio delle attività che degli slot di attività per ogni stato dell'attività.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Esempio per Batch REST

I [frammenti di codice dell'API REST](/rest/api/batchservice/) batch seguenti illustrano come creare un pool con più slot di attività per nodo e come inviare un'attività con gli slot necessari.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Creare un pool con più slot di attività per nodo

Questo frammento di codice mostra una richiesta di creazione di un pool che contiene due nodi di grandi dimensioni con un massimo di quattro attività per nodo.

Per altre informazioni sull'aggiunta di pool con l'API REST, vedere [Aggiungere un pool a un account](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "canonical",
      "offer": "ubuntuserver",
      "sku": "18.04-lts"
    },
    "nodeAgentSKUId": "batch.node.ubuntu 16.04"
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Creare un'attività con gli slot necessari

Questo frammento di codice mostra una richiesta di aggiunta di un'attività con non `requiredSlots` predefinita. Questa attività verrà eseguita solo quando nel nodo di calcolo sono disponibili slot gratuiti sufficienti.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Esempio di codice in GitHub

Il [progetto ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) in GitHub illustra l'uso [della proprietà CloudPool.TaskSlotsPerNode.](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode)

Questa applicazione console C# usa la libreria [Batch .NET](/dotnet/api/microsoft.azure.batch) per creare un pool con uno o più nodi di calcolo. Esegue un numero configurabile di attività in tali nodi per simulare un carico variabile. L'output dell'applicazione mostra i nodi che hanno eseguito ogni attività. L'applicazione fornisce inoltre un riepilogo dei parametri e della durata del processo.

Ad esempio, di seguito è riportata la parte di riepilogo dell'output di due esecuzioni diverse dell'applicazione di esempio ParallelTasks. Le durate dei processi mostrate qui non includono il tempo di creazione del pool, poiché ogni processo è stato inviato a un pool creato in precedenza i cui nodi di calcolo si trovavano nello stato *Inattivo* al momento dell'invio.

La prima esecuzione dell'applicazione di esempio mostra che con un singolo nodo nel pool e con l'impostazione predefinita di un'attività per nodo, la durata del processo è superiore a 30 minuti.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La seconda esecuzione dell'esempio illustra una diminuzione significativa nella durata del processo. Questo perché il pool è stato configurato con quattro attività per nodo, consentendo l'esecuzione parallela delle attività per completare il processo in quasi un quarto del tempo.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Passaggi successivi

- Provare a [Batch Explorer](https://azure.github.io/BatchExplorer/) mappa termica. Batch Explorer è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare, eseguire il debug e monitorare le applicazioni di Azure Batch. Quando si esegue l'applicazione di esempio [ParallelTasks,](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) la funzionalità mappa termica Batch Explorer consente di visualizzare facilmente l'esecuzione di attività parallele in ogni nodo.
- Esplorare [Azure Batch esempi in GitHub.](https://github.com/Azure/azure-batch-samples)
- Altre informazioni sulle [dipendenze delle attività batch](batch-task-dependencies.md).
