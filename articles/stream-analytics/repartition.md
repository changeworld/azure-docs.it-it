---
title: Usare il partizionamento per ottimizzare i processi di analisi di flusso di Azure
description: Questo articolo descrive come usare il ripartizionamento per ottimizzare i processi di analisi di flusso di Azure che non possono essere paralleli.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182537"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Usare il partizionamento per ottimizzare l'elaborazione con analisi di flusso di Azure

Questo articolo illustra come usare il partizionamento per ridimensionare la query di analisi di flusso di Azure per scenari che non possono essere completamente [paralleli](stream-analytics-scale-jobs.md).

Potrebbe non essere possibile usare la [parallelizzazione](stream-analytics-parallelization.md) se:

* Non si controlla la chiave di partizione per il flusso di input.
* L'origine "spruzza" input tra più partizioni che successivamente devono essere unite.

Il partizionamento o il rimescolamento è necessario quando si elaborano i dati in un flusso non partizionato in base a uno schema di input naturale, ad esempio **PartitionID** per hub eventi. Quando si esegue la ripartizione, ogni partizione può essere elaborata in modo indipendente, che consente di scalare in modo lineare la pipeline di streaming. 

## <a name="how-to-repartition"></a>Come ripartizionare
È possibile ripartizionare l'input in due modi:
1. Usare un processo di analisi di flusso separato che esegue il partizionamento
2. Usare un singolo processo ma eseguire il ripartizionamento prima della logica di analisi personalizzata

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>Creazione di un processo di analisi di flusso separato per la ripartizione dell'input
È possibile creare un processo che legge input e scritture in un output di hub eventi usando una chiave di partizione. Questo hub eventi può quindi fungere da input per un altro processo di analisi di flusso in cui si implementa la logica di analisi. Quando si configura questo output di hub eventi nel processo, è necessario specificare la chiave di partizione in base alla quale analisi di flusso ripartirà i dati. 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>Ripartizionare l'input all'interno di un singolo processo di analisi di flusso
È anche possibile introdurre un passaggio nella query che prima Ripartiziona l'input e che può quindi essere usato da altri passaggi della query. Se ad esempio si vuole ripartizionare l'input in base a **DeviceID**, la query sarà:
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

La query di esempio seguente unisce due flussi di dati ripartizionati. Quando si uniscono due flussi di dati ripartizionati, i flussi devono avere la stessa chiave di partizione e il numero. Il risultato è un flusso con lo stesso schema di partizione.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Lo schema di output deve corrispondere alla chiave dello schema del flusso e al conteggio in modo che ogni sottoflusso possa essere svuotato in modo indipendente. Il flusso può anche essere Unito e ripartizionato di nuovo in base a uno schema diverso prima dello scaricamento, ma è consigliabile evitare tale metodo perché aggiunge alla latenza generale dell'elaborazione e aumenta l'utilizzo delle risorse.

## <a name="streaming-units-for-repartitions"></a>Unità di streaming per le ripartizionamenti

Sperimentare e osservare l'utilizzo delle risorse del processo per determinare il numero esatto di partizioni necessarie. Il numero di [unità di streaming (su)](stream-analytics-streaming-unit-consumption.md) deve essere regolato in base alle risorse fisiche necessarie per ogni partizione. In generale, sono necessari sei SUs per ogni partizione. Se al processo sono assegnate risorse insufficienti, il sistema applicherà la ripartizione solo se il processo è vantaggioso.

## <a name="repartitions-for-sql-output"></a>Ripartizionamenti per l'output SQL

Quando il processo utilizza il database SQL per l'output, utilizzare il partizionamento esplicito in modo che corrisponda al numero di partizioni ottimale per ottimizzare la velocità effettiva. Poiché SQL funziona al meglio con otto writer, il partizionamento del flusso a otto prima dello scaricamento o in un altro upstream può trarre vantaggio dalle prestazioni dei processi. 

Quando sono presenti più di 8 partizioni di input, ereditare l'input dello schema di partizione potrebbe non essere una scelta appropriata. Si consiglia di usare [into](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) nella query per specificare in modo esplicito il numero di writer di output. 

Nell'esempio seguente viene letta dall'input, indipendentemente dal fatto che sia partizionata in modo naturale e il flusso viene ripartizionato di dieci volte in base alla dimensione DeviceID e vengono scaricati i dati nell'output. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Per altre informazioni, consultare [Output di Analisi di flusso di Azure in Database SQL di Azure](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'uso di Analisi di flusso di Azure](stream-analytics-introduction.md)
* [Sfruttare i vantaggi della parallelizzazione delle query in Analisi di flusso di Azure](stream-analytics-parallelization.md)
