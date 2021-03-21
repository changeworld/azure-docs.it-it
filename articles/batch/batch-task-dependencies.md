---
title: Creare relazioni tra attività per eseguire attività
description: Creare attività che dipendono dal completamento di altre attività per l'elaborazione di carichi di lavoro di tipo MapReduce e carichi di lavoro Big Data simili in Azure Batch.
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803936"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Creare relazioni tra attività per eseguire attività che dipendono da altre attività

Con le dipendenze delle attività batch, è possibile creare attività pianificate per l'esecuzione nei nodi di calcolo dopo il completamento di una o più attività padre. Ad esempio, è possibile creare un processo che esegue il rendering di ogni fotogramma di un film 3D con le attività parallele separate. L'attività finale, ovvero "attività di unione", unisce i fotogrammi sottoposti a rendering in un filmato completo solo dopo che è stato eseguito il rendering di tutti i fotogrammi.

Ecco alcuni degli scenari in cui le dipendenze delle attività sono utili:

- Carichi di lavoro di tipo MapReduce nel cloud.
- Processi le cui attività di elaborazione dati può essere espressa come grafo aciclico diretto (DAG).
- Processi di pre-rendering e post-rendering, in cui ogni attività deve essere completata prima di poter avviare quella successiva.
- Qualsiasi altro processo in cui le attività downstream dipendono l'output delle attività upstream.

Per impostazione predefinita, l'esecuzione delle attività dipendenti è pianificata solo dopo il corretto completamento dell'attività padre. Facoltativamente, è possibile specificare un' [azione di dipendenza](#dependency-actions)  per sostituire il comportamento predefinito ed eseguire attività quando l'attività padre ha esito negativo.

## <a name="task-dependencies-with-batch-net"></a>Relazioni tra attività con Batch .NET

Questo articolo illustra la configurazione di relazioni tra attività tramite la libreria [Batch .NET](/dotnet/api/microsoft.azure.batch). Viene illustrato prima come [abilitare le dipendenze tra attività](#enable-task-dependencies) nei processi, quindi viene spiegato come [configurare un'attività con dipendenze](#create-dependent-tasks). Viene inoltre descritto come specificare un'azione di dipendenza per l'esecuzione di attività dipendenti se l'attività padre non riesce. Vengono infine illustrati gli [scenari delle relazione](#dependency-scenarios) supportate da Batch.

## <a name="enable-task-dependencies"></a>Abilitare le relazioni tra attività

Per usare le dipendenze delle attività nell'applicazione Batch, è innanzitutto necessario configurare il processo per l'uso delle dipendenze. In Batch .NET abilitare la funzionalità in [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) impostando la rispettiva proprietà [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) su `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Nel frammento di codice precedente "batchClient" è un'istanza della classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="create-dependent-tasks"></a>Creare attività dipendenti

Per creare un'attività che dipende dal completamento di una o più attività padre, è possibile specificare che l'attività "dipende" dalle altre attività. In batch .NET configurare la proprietà [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) con un'istanza della classe [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Questo frammento di codice crea un'attività dipendente con ID attività "Flowers". L'attività "Flowers" dipende dalle attività "Rain" e "Sun". L'esecuzione dell'attività "Flowers" in un nodo di calcolo verrà pianificata solo dopo il corretto completamento delle attività "Rain" e "Sun".

> [!NOTE]
> Per impostazione predefinita, un'attività viene considerata correttamente completata quando l'attività è in stato completato e il codice di uscita è `0`. In batch .NET questo significa che il valore della proprietà [CloudTask. state](/dotnet/api/microsoft.azure.batch.cloudtask.state) è `Completed` e il valore della proprietà [TaskExecutionInformation. ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) di CloudTask è `0` . Per informazioni su come modificare questa procedura, vedere la sezione [azioni di dipendenza](#dependency-actions) .

## <a name="dependency-scenarios"></a>scenari delle relazione

In Azure Batch è possibile usare tre scenari di relazioni tra attività di base, ovvero uno-a-uno, uno-a-molti e la relazione tra intervalli di ID. Questi tre scenari possono essere combinati per offrire un quarto scenario: molti-a-molti.

| Scenario&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Esempio | Illustrazione |
|:---:| --- | --- |
|  [Uno-a-uno](#one-to-one) |L'*attivitàB* dipende dall'*attivitàA* <p/> L'*attivitàB* sarà pianificata per l'esecuzione solo dopo il completamento corretto dell'*attivitàA* |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagramma che illustra lo scenario di dipendenza di attività uno-a-uno."::: |
|  [Uno-a-molti](#one-to-many) |L'*attivitàC* dipende sia dall'*attivitàA* che dall'*attivitàB*. <p/> L'*attivitàC* sarà pianificata per l'esecuzione solo dopo il completamento corretto dell'*attivitàA* e dell'*attivitàB* |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagramma che mostra lo scenario di dipendenza di attività uno-a-molti."::: |
|  [Intervallo di ID attività](#task-id-range) |L'*attivitàD* dipende da un intervallo di attività <p/> L'*attivitàD* sarà pianificata per l'esecuzione solo dopo il completamento corretto delle attività con ID compresi tra *1* e *10* |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagramma che mostra lo scenario di dipendenza dell'attività intervallo di ID attività."::: |

> [!TIP]
> È possibile creare relazioni **molti-a-molti**, ad esempio relazioni in cui le attività C, D, E e F dipendono dalle attività A e B. Questo tipo di relazione risulta utile, ad esempio, negli scenari di pre-elaborazione parallelizzata, in cui le attività downstream dipendono dall'output di più attività upstream.
> 
> Negli esempi di questa sezione un'attività dipendente viene eseguita solo dopo che le attività padre vengono completate correttamente. Questo comportamento è quello predefinito per un'attività dipendente. È possibile eseguire un'attività dipendente dopo che un'attività padre ha esito negativo specificando un' [azione di dipendenza](#dependency-actions)  per sostituire il comportamento predefinito.

### <a name="one-to-one"></a>Uno-a-uno

In una relazione uno-a-uno un'attività dipende dal corretto completamento di una sola attività padre. Per creare la dipendenza, fornire un singolo ID attività al metodo statico [TaskDependencies. OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) quando si popola la proprietà [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Uno-a-molti

In una relazione uno-a-molti un'attività dipende dal completamento di più attività padre. Per creare la dipendenza, fornire una raccolta di ID attività al metodo statico [TaskDependencies. onid](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) quando si popola la proprietà [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Intervallo di ID attività

In una dipendenza da un intervallo di attività padre un'attività dipende dal completamento delle attività il cui ID è compreso all'interno di un intervallo.
Per creare la dipendenza, fornire il primo e l'ultimo ID attività nell'intervallo al metodo statico [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) quando si popola la proprietà [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

> [!IMPORTANT]
> Quando si usano intervalli di ID attività per le dipendenze, verranno selezionate solo le attività con ID che rappresentano valori interi. Ad esempio, l'intervallo `1..10` selezionerà le attività `3` e `7` , ma non `5flamingoes` .
>
> Gli zeri iniziali non sono rilevanti per la valutazione delle dipendenze nell'intervallo, per cui le attività con gli identificatori di stringa `4`, `04` e `004` saranno tutte *all'interno* dell'intervallo e verranno tutte considerate attività `4`, in modo che la prima a essere completata soddisferà la dipendenza.
>
> Ogni attività nell'intervallo deve soddisfare la dipendenza, completando correttamente o completando un errore di cui è stato eseguito il mapping a un set di [azioni di dipendenza](#dependency-actions) da **soddisfare**.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Azioni di dipendenza

Per impostazione predefinita, un'attività o un set di attività dipendenti vengono eseguite solo dopo il corretto completamento di un'attività padre. In alcuni scenari potrebbe essere necessario eseguire attività dipendenti anche se l'attività padre non riesce. È possibile sostituire il comportamento predefinito specificando un'azione di dipendenza.

Un'azione di dipendenza specifica se un'attività dipendente è idonea per l'esecuzione in base alla riuscita o meno dell'attività padre. Si supponga, ad esempio, un'attività dipendente in attesa di dati dal completamento dell'attività upstream. Se l'attività upstream non riesce, l'attività dipendente potrebbe comunque essere eseguita usando dati meno recenti. In questo caso, un'azione di dipendenza può specificare che l'attività dipendente è idonea per l'esecuzione nonostante l'errore dell'attività padre.

Un'azione di dipendenza è basata su una condizione di uscita per l'attività padre. È possibile specificare un'azione di dipendenza per una delle seguenti condizioni di uscita:

- Quando si verifica un errore di pre-elaborazione.
- Quando si verifica un errore di caricamento dei file. Se l'attività si chiude con un codice di uscita specificato tramite **exitCodes** o **exitCodeRanges** e quindi si verifica un errore di caricamento dei file, l'azione specificata dal codice di uscita ha la precedenza.
- Quando l'attività termina con un codice di uscita definito dalla proprietà **ExitCodes**.
- Quando l'attività termina con un codice di uscita compreso in un intervallo specificato dalla proprietà **ExitCodeRanges**.
- Il caso predefinito, ovvero se l'attività si chiude con un codice di uscita non definito da **ExitCodes** o **ExitCodeRanges** oppure se l'attività si chiude con un errore di pre-elaborazione e la proprietà **PreProcessingError** non è stata configurata o se l'attività ha esito negativo con un errore di caricamento dei file e la proprietà **FileUploadError** non è stata configurata. 

Per .NET, vedere la classe [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) per altre informazioni su queste condizioni.

Per specificare un'azione di dipendenza in .NET, impostare la proprietà [ExitOptions. DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) per la condizione di uscita su uno dei seguenti elementi:

- **Soddisfa**: indica che le attività dipendenti sono idonee per l'esecuzione se l'attività padre termina con un errore specificato.
- **Block**: indica che le attività dipendenti non sono idonee per l'esecuzione.

L'impostazione predefinita per la proprietà **DependencyAction** è **Satisfy** per il codice di uscita 0 e **Block** per tutte le altre condizioni di uscita.

Il frammento di codice seguente imposta la proprietà **DependencyAction** per un'attività padre. Se l'attività padre termina con un errore di pre-elaborazione o con i codici di errore specificati, l'attività dipendente viene bloccata. Se l'attività padre termina con qualsiasi altro errore diverso da zero, l'attività dipendente è idonea per l'esecuzione.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Esempio di codice

Il progetto di esempio [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) su GitHub illustra:

- Come abilitare la dipendenza delle attività in un processo.
- Come creare attività che dipendono da altre attività.
- Come eseguire queste attività in un pool di nodi di calcolo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla funzionalità dei [pacchetti dell'applicazione](batch-application-packages.md) di batch, che fornisce un modo semplice per distribuire e rilasciare le versioni delle applicazioni eseguite dalle attività nei nodi di calcolo.
- Informazioni sul [controllo degli errori per i processi e le attività](batch-job-task-error-checking.md).
