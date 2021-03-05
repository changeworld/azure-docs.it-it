---
title: Passare le informazioni sul trigger alla pipeline
description: Informazioni su come fare riferimento ai metadati di trigger nella pipeline
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 57024d6cb23ab273aa69bf59f4ec436a6f873a05
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193496"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Metadati del trigger di riferimento nelle esecuzioni di pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive come è possibile usare i metadati del trigger, ad esempio l'ora di inizio del trigger, nell'esecuzione della pipeline.

Pipeline a volte deve comprendere e leggere i metadati dal trigger che li richiama. Ad esempio, con l'esecuzione del trigger di finestra a cascata, in base all'ora di inizio e di fine della finestra, la pipeline elaborerà diverse sezioni di dati o cartelle. In Azure Data Factory viene usata la parametrizzazione e la [variabile di sistema](control-flow-system-variables.md) per passare i metadati dal trigger alla pipeline.

Questo modello è particolarmente utile per il [trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md), dove trigger fornisce l'ora di inizio e di fine della finestra.

> [!NOTE]
> Il tipo di trigger diverso fornisce informazioni sui metadati diversi. Per ulteriori informazioni, vedere [variabile di sistema](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

Questa sezione illustra come passare le informazioni dei metadati dal trigger alla pipeline, all'interno dell'interfaccia utente di Azure Data Factory.

1. Passare all' **area di disegno creazione** e modifica di una pipeline

1. Fare clic sull'area di disegno vuota per visualizzare le impostazioni della pipeline. Non selezionare alcuna attività. Potrebbe essere necessario estrarre il pannello delle impostazioni dalla parte inferiore dell'area di disegno, perché potrebbe essere stato compresso

1. Selezionare la sezione **parametri** e selezionare **+ nuovo** per aggiungere i parametri

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Screenshot dell'impostazione della pipeline che Mostra come definire i parametri nella pipeline.":::

1. Per aggiungere trigger alla pipeline, fare clic su **+ trigger**.

1. Crea o alleghi un trigger alla pipeline e fai clic su **OK**

1. Nella pagina seguente inserire i metadati del trigger per ogni parametro. Usare il formato definito nella [variabile di sistema](control-flow-system-variables.md) per recuperare le informazioni sul trigger. Non è necessario inserire le informazioni per tutti i parametri, ma solo quelle che presuppongono i valori dei metadati del trigger. Ad esempio, qui viene assegnata l'ora di inizio dell'esecuzione del trigger al *parameter_1*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Screenshot della pagina di definizione del trigger che Mostra come passare le informazioni sui trigger ai parametri della pipeline.":::

1. Per utilizzare i valori nella pipeline, utilizzare i parametri _@pipeline (). Parameters. ParameterName_, __non__ la variabile di sistema, nelle definizioni della pipeline. Ad esempio, in questo caso, per leggere l'ora di inizio del trigger, si farà riferimento a @pipeline () .Parameters.parameter_1.

## <a name="json-schema"></a>Schema JSON

Per passare le informazioni sul trigger alle esecuzioni della pipeline, è necessario aggiornare sia il trigger che il file JSON della pipeline con la sezione _Parameters_ .

### <a name="pipeline-definition"></a>Definizione della pipeline

Nella sezione **Proprietà** aggiungere le definizioni dei parametri alla sezione **parametri**

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Definizione del trigger

Nella sezione **pipeline** assegnare i valori dei parametri nella sezione **parametri** . Non è necessario inserire le informazioni per tutti i parametri, ma solo quelle che presuppongono i valori dei metadati del trigger.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Usare le informazioni sui trigger nella pipeline

Per utilizzare i valori nella pipeline, utilizzare i parametri _@pipeline (). Parameters. ParameterName_, __non__ la variabile di sistema, nelle definizioni della pipeline.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
