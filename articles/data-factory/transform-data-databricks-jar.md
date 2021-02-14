---
title: Trasformare i dati con jar di databricks
description: Informazioni su come elaborare o trasformare i dati eseguendo un file jar di databricks in una pipeline Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374015"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Trasformare i dati eseguendo un'attività JAR in Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L'attività JAR di Azure Databricks in una [pipeline di Data Factory](concepts-pipelines-activities.md) esegue un'attività JAR Spark nel cluster di Azure Databricks. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate. Azure Databricks è una piattaforma gestita per l'esecuzione di Apache Spark.

Per un'introduzione di undici minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definizione di attività JAR di Databricks

Ecco la definizione JSON di esempio di un'attività jar di databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Proprietà dell'attività JAR di Databricks

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

|Proprietà|Descrizione|Obbligatoria|
|:--|---|:-:|
|name|Nome dell'attività nella pipeline.|Sì|
|description|Testo che descrive l'attività.|No|
|type|Per l'attività JAR di Databricks il tipo di attività è DatabricksSparkJar.|Sì|
|linkedServiceName|Nome del servizio collegato Databricks su cui è in esecuzione l'attività JAR. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).|Sì|
|mainClassName|Il nome completo della classe che contiene il metodo Main deve essere eseguito. Questa classe deve essere contenuta in un file JAR fornito come libreria. Un file JAR può contenere più classi. Ognuna delle classi può contenere un metodo Main.|Sì|
|parametri|Parametri che verranno passati al metodo Main. Questa proprietà è una matrice di stringhe.|No|
|libraries|Un elenco di librerie da installare nel cluster che eseguirà il processo. Può essere una matrice di <stringa, oggetto>|Sì (almeno una che contiene il metodo mainClassName)|

> [!NOTE]
> **Problema noto** : quando si usa lo stesso [cluster interattivo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) per l'esecuzione di attività jar di databricks simultanee (senza riavvio del cluster), si verifica un problema noto in databricks, in cui i parametri della prima attività verranno usati anche dalle attività seguenti. Di conseguenza, i parametri non corretti vengono passati ai processi successivi. Per ovviare a questo problema, usare invece un [cluster di processi](compute-linked-services.md#example---using-new-job-cluster-in-databricks) .

## <a name="supported-libraries-for-databricks-activities"></a>Librerie supportate per le attività di databricks

Nella definizione precedente dell'attività databricks sono stati specificati i tipi di libreria seguenti: `jar` ,, `egg` `maven` , `pypi` , `cran` .

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

Per ulteriori informazioni, vedere la [documentazione di databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) per i tipi di libreria.

## <a name="how-to-upload-a-library-in-databricks"></a>Come caricare una libreria in Databricks

### <a name="you-can-use-the-workspace-ui"></a>È possibile usare l'interfaccia utente dell'area di lavoro:

1. [Usare l'interfaccia utente dell'area di lavoro di databricks](/azure/databricks/libraries/#create-a-library)

2. Per ottenere il percorso dBFS della libreria aggiunta usando l'interfaccia utente, è possibile usare l'interfaccia della riga di comando di [databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   In genere le librerie Jar sono archiviate in dbfs:/FileStore/jars quando si usa l'interfaccia utente. È possibile elencarle tutte tramite l'interfaccia della riga di comando: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>In alternativa, è possibile usare l'interfaccia della riga di comando di databricks:

1. Seguire [la copia della libreria usando l'interfaccia della riga di comando di databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Usare l'interfaccia della riga di comando di databricks [(passaggi di installazione)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Ad esempio, per copiare un file JAR in dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione e una dimostrazione di questa funzionalità per undici minuti, Guarda il [video](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
