---
title: Trasformazione Ordinamento nel flusso di dati di mapping
description: Azure Data Factory mapping dell'ordinamento dei dati
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 4a6567f8576e2507704956233bc593b203b48239
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588735"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Trasformazione Ordinamento nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione ordinamento consente di ordinare le righe in ingresso nel flusso di dati corrente. È possibile scegliere singole colonne e ordinarle in ordine crescente o decrescente.

> [!NOTE]
> I flussi di dati di mapping vengono eseguiti in cluster Spark che distribuiscono i dati tra più nodi e partizioni. Se si sceglie di ripartizionare i dati in una trasformazione successiva, l'ordinamento potrebbe andare perso a causa del rimpasto dei dati. Il modo migliore per mantenere l'ordinamento nel flusso di dati è impostare una singola partizione nella scheda Ottimizza della trasformazione e mantenere la trasformazione Ordinamento il più vicino possibile al sink.

## <a name="configuration"></a>Configurazione

![Impostazioni di ordinamento](media/data-flow/sort.png "Ordina")

**Senza distinzione tra maiuscole e minuscole:** Se si desidera ignorare o meno la distinzione tra maiuscole e minuscole durante l'ordinamento di campi stringa o di testo

**Ordina solo all'interno delle partizioni:** Quando i flussi di dati vengono eseguiti in Spark, ogni flusso di dati viene suddiviso in partizioni. Questa impostazione ordina i dati solo all'interno delle partizioni in ingresso invece di ordinare l'intero flusso di dati. 

**Condizioni di ordinamento:** Scegliere le colonne in base alle quali eseguire l'ordinamento e in quale ordine viene eseguita l'ordinamento. L'ordine determina la priorità di ordinamento. Scegliere se i valori Null verranno visualizzati all'inizio o alla fine del flusso di dati.

### <a name="computed-columns"></a>Colonne calcolate

Per modificare o estrarre un valore di colonna prima di applicare l'ordinamento, passare il mouse sulla colonna e selezionare "colonna calcolata". Verrà aperto il generatore di espressioni per creare un'espressione per l'operazione di ordinamento anziché usare un valore di colonna.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Esempio

![Impostazioni di ordinamento](media/data-flow/sort.png "Ordina")

Lo script del flusso di dati per la configurazione di ordinamento precedente si trova nel frammento di codice seguente.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Passaggi successivi

Dopo l'ordinamento, è possibile usare la trasformazione [Aggregazione](data-flow-aggregate.md)
