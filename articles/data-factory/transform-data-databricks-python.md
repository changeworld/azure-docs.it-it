---
title: Trasformare i dati con Python di databricks
description: Informazioni su come elaborare o trasformare i dati eseguendo un'attività Python di databricks in una pipeline Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373947"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Trasformare i dati eseguendo un'attività Python in Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'attività Python di Azure Databricks in una [pipeline di Data Factory](concepts-pipelines-activities.md) esegue un file Python nel cluster di Azure Databricks. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate. Azure Databricks è una piattaforma gestita per l'esecuzione di Apache Spark.

Per un'introduzione di undici minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definizione dell'attività Python di Databricks

Ecco la definizione JSON di esempio di un'attività Python di Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Proprietà dell'attività Python di Databricks

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

|Proprietà|Descrizione|Obbligatoria|
|---|---|---|
|name|Nome dell'attività nella pipeline.|Sì|
|description|Testo che descrive l'attività.|No|
|type|Per l'attività Python di Databricks il tipo di attività è DatabricksSparkPython.|Sì|
|linkedServiceName|Nome del servizio collegato Databricks su cui è in esecuzione l'attività Python. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).|Sì|
|pythonFile|L'URI del file Python da eseguire. Sono supportati solo i percorsi DBFS.|Sì|
|parametri|Parametri della riga di comando che verranno passati al file Python. È una matrice di stringhe.|No|
|libraries|Un elenco di librerie da installare nel cluster che eseguirà il processo. Può essere una matrice di <stringa, oggetto>|No|

## <a name="supported-libraries-for-databricks-activities"></a>Librerie supportate per le attività di databricks

Nella definizione dell'attività di Databricks precedente si specificano questi tipi di libreria: *jar*, *egg*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Per altre informazioni, consultare la [documentazione di Databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) per i tipi di libreria.

## <a name="how-to-upload-a-library-in-databricks"></a>Come caricare una libreria in Databricks

### <a name="you-can-use-the-workspace-ui"></a>È possibile usare l'interfaccia utente dell'area di lavoro:

1. [Usare l'interfaccia utente dell'area di lavoro di databricks](/azure/databricks/libraries/#create-a-library)

2. Per ottenere il percorso dBFS della libreria aggiunta usando l'interfaccia utente, è possibile usare l'interfaccia della riga di comando di [databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   In genere le librerie Jar sono archiviate in dbfs:/FileStore/jars quando si usa l'interfaccia utente. È possibile elencarle tutte tramite l'interfaccia della riga di comando: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>In alternativa, è possibile usare l'interfaccia della riga di comando di databricks:

1. Seguire [la copia della libreria usando l'interfaccia della riga di comando di databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Usare l'interfaccia della riga di comando di databricks [(passaggi di installazione)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Ad esempio, per copiare un file JAR in dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
