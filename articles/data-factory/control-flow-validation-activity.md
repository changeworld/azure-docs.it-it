---
title: Attività di convalida in Azure Data Factory
description: L'attività di convalida non continua l'esecuzione della pipeline fino a quando non convalida il set di dati collegato con determinati criteri specificati dall'utente.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e00090fc31c129f35632d30b9a5ed6c39c13a414
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385252"
---
# <a name="validation-activity-in-azure-data-factory"></a>Attività di convalida in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

È possibile usare una convalida in una pipeline per garantire che la pipeline continui a essere eseguita solo dopo aver convalidato il riferimento al set di dati allegato, che soddisfa i criteri specificati oppure che sia stato raggiunto il timeout.


## <a name="syntax"></a>Sintassi

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
name | Nome dell'attività' Validation ' | string | Sì |
type | Deve essere impostato su  **Validation**. | string | Sì |
dataset | L'attività bloccherà l'esecuzione fino a quando non viene convalidato il riferimento al set di dati esistente e soddisfa i criteri specificati oppure è stato raggiunto il timeout. Il set di dati fornito deve supportare la proprietà "MinimumSize" o "ChildItems". | Riferimento a DataSet | Sì |
timeout | Specifica il timeout per l'attività da eseguire. Se non viene specificato alcun valore, il valore predefinito è 7 giorni ("7.00:00:00"). Il formato è d. hh: mm: SS | string | No |
sleep | Ritardo in secondi tra i tentativi di convalida. Se non viene specificato alcun valore, il valore predefinito è 10 secondi. | Integer | No |
childItems | Controlla se la cartella contiene elementi figlio. Può essere impostato su-true: verificare che la cartella esista e che includa elementi. Si blocca fino a quando almeno un elemento non è presente nella cartella o viene raggiunto il valore di timeout.-false: verificare che la cartella esista e che sia vuota. Blocca fino a quando la cartella non è vuota o fino al raggiungimento del valore di timeout. Se non viene specificato alcun valore, l'attività si bloccherà fino a quando la cartella non esiste o fino al raggiungimento del timeout. | Boolean | No |
minimumSize | Dimensioni minime di un file in byte. Se non viene specificato alcun valore, il valore predefinito è 0 byte | Integer | No |


## <a name="next-steps"></a>Passaggi successivi
Vedere altre attività del flusso di controllo supportate da Data Factory:

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività Esegui pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)
