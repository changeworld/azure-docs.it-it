---
title: Trasformazione EXISTS nel flusso di dati di mapping
description: Verificare la presenza di righe esistenti utilizzando la trasformazione EXISTS nel flusso di dati di mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "82982633"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Trasformazione EXISTS nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione EXISTS è una trasformazione filtro di riga che controlla se i dati esistono in un'altra origine o in un altro flusso. Il flusso di output include tutte le righe nel flusso a sinistra che esistono o non esistono nel flusso di destra. La trasformazione EXISTS è simile a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Configurazione

1. Scegliere il flusso di dati che si sta controllando di esistere nell'elenco a discesa **flusso destro** .
1. Specificare se si desidera che i dati esistano o non esistano nell'impostazione del **tipo exist** .
1. Specificare se si desidera un' **espressione personalizzata**.
1. Consente di scegliere le colonne chiave da confrontare con le condizioni esistenti. Per impostazione predefinita, il flusso di dati cerca l'uguaglianza tra una colonna per ogni flusso. Per eseguire il confronto tramite un valore calcolato, passare il puntatore del mouse sull'elenco a discesa della colonna e selezionare **Colonna calcolata**.

![Impostazioni exists](media/data-flow/exists.png "esistente 1")

### <a name="multiple-exists-conditions"></a>Più condizioni exists

Per confrontare più colonne da ogni flusso, aggiungere una nuova condizione exists facendo clic sull'icona a forma di segno più accanto a una riga esistente. Ogni condizione aggiuntiva viene unita in join da un'istruzione "and". Il confronto di due colonne equivale all'espressione seguente:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Espressione personalizzata

Per creare un'espressione in formato libero che contenga operatori diversi da "e" e "uguale a", selezionare il campo **espressione personalizzata** . Immettere un'espressione personalizzata tramite il generatore di espressioni del flusso di dati facendo clic sulla casella blu.

![Impostazioni personalizzate exists](media/data-flow/exists1.png "esistente personalizzato")

## <a name="broadcast-optimization"></a>Ottimizzazione della trasmissione

![Join di trasmissione](media/data-flow/broadcast.png "Join di trasmissione")

Nella trasformazione di join, ricerche ed exists, se uno o entrambi i flussi di dati rientrano nella memoria del nodo di lavoro, è possibile ottimizzare le prestazioni abilitando la **trasmissione**. Per impostazione predefinita, il motore Spark deciderà automaticamente se trasmettere o meno un lato. Per scegliere manualmente il lato da trasmettere, selezionare **Fisso**.

Non è consigliabile disabilitare la trasmissione tramite l'opzione **Off** a meno che i join non siano in errore di timeout.

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Esempio

L'esempio seguente è una trasformazione exists denominata `checkForChanges` che accetta il flusso sinistro `NameNorm2` e il flusso destro `TypeConversions` .  La condizione EXISTS è l'espressione `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` che restituisce true se entrambe `EMPID` le `Region` colonne e in ogni flusso corrispondono. Mentre viene verificata l'esistenza, `negate` è false. Non è abilitata alcuna trasmissione nella scheda Ottimizza, quindi il `broadcast` valore è `'none'` .

In Data Factory UX questa trasformazione è simile all'immagine seguente:

![Esempio exists](media/data-flow/exists-script.png "Esempio exists")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Passaggi successivi

Le trasformazioni simili sono [ricerca](data-flow-lookup.md) e [join](data-flow-join.md).
