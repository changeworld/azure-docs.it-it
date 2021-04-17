---
title: Risolvere gli errori di installazione della libreria
description: Questa esercitazione offre una panoramica su come risolvere gli errori di installazione della libreria.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 006abf62c605c2ca34fd1adeadee8e29ae0fb8fb
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588310"
---
# <a name="troubleshoot-library-installation-errors"></a>Risolvere gli errori di installazione della libreria 
Per rendere disponibile codice di terze parti o compilato in locale per le applicazioni, è possibile installare una libreria in uno dei pool Apache Spark serverless. I pacchetti elencati nel file requirements.txt vengono scaricati da PyPi al momento dell'avvio del pool. Questo file di requisiti viene usato ogni volta che viene creata un'istanza di Spark da tale pool di Spark. Una volta installata una libreria per un pool di Spark, è disponibile per tutte le sessioni che usano lo stesso pool. 

In alcuni casi, è possibile che una libreria non venga visualizzata nel pool Apache Spark. Questo caso si verifica spesso quando si verifica un errore nelle librerie requirements.txt o specificate. Quando si verifica un errore nel processo di installazione della libreria, il pool Apache Spark ripristina le librerie specificate nel runtime di base di Synapse.

L'obiettivo di questo documento è fornire problemi comuni e facilitare il debug degli errori di installazione della libreria.

## <a name="force-update-your-apache-spark-pool"></a>Forzare l'aggiornamento Apache Spark pool
Quando si aggiornano le librerie nel pool Apache Spark, queste modifiche verranno prelevate dopo il riavvio del pool. Se sono disponibili processi attivi, questi processi continueranno a essere eseguiti nella versione originale del pool di Spark.

È possibile forzare l'applicazione delle modifiche selezionando l'opzione **Forza nuove impostazioni.** Questa impostazione terminerà tutte le sessioni correnti per il pool di Spark selezionato. Al termine delle sessioni, è necessario attendere il riavvio del pool. 

![Aggiungere librerie Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Aggiungere librerie Python")

## <a name="track-installation-progress"></a>Tenere traccia dello stato dell'installazione
Un processo Spark riservato di sistema viene avviato ogni volta che un pool viene aggiornato con un nuovo set di librerie. Questo processo Spark consente di monitorare lo stato dell'installazione della libreria. Se l'installazione non riesce a causa di conflitti di libreria o di altri problemi, il pool Spark ripristina lo stato precedente o predefinito. 

Inoltre, gli utenti possono anche esaminare i log di installazione per identificare i conflitti di dipendenza o vedere quali librerie sono state installate durante l'aggiornamento del pool.

Per visualizzare questi log:
1. Passare all'elenco applicazioni Spark nella **scheda** Monitoraggio. 
2. Selezionare il processo dell'applicazione Spark di sistema corrispondente all'aggiornamento del pool. Questi processi di sistema vengono eseguiti con il *titolo SystemReservedJob-LibraryManagement.*
   ![Screenshot che evidenzia il processo della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visualizzare il processo della libreria di sistema")
3. Passare a per visualizzare i **log di driver** e **stdout.** 
4. Nei risultati verranno visualizzati i log correlati all'installazione dei pacchetti.
    ![Screenshot che evidenzia i risultati del processo della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visualizzare lo stato del processo della libreria di sistema")

## <a name="validate-your-permissions"></a>Convalidare le autorizzazioni
Per installare e aggiornare le  librerie, è  necessario disporre delle autorizzazioni Collaboratore dati BLOB di archiviazione o Proprietario dati BLOB di archiviazione nell'account di archiviazione Azure Data Lake Storage Gen2 primario collegato all'area di lavoro Azure Synapse Analytics archiviazione.

Per verificare di disporre di queste autorizzazioni, è possibile eseguire il codice seguente:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Se viene visualizzato un errore, probabilmente mancano le autorizzazioni necessarie. Per informazioni su come ottenere le autorizzazioni necessarie, vedere questo documento: [Assegnare](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)le autorizzazioni collaboratore ai dati del BLOB di archiviazione o Proprietario dei dati del BLOB di archiviazione .

Inoltre, se si esegue una pipeline, l'msi dell'area di lavoro deve avere anche le autorizzazioni Proprietario dati BLOB di archiviazione o Collaboratore dati BLOB di archiviazione. Per informazioni su come concedere questa autorizzazione all'identità dell'area di lavoro, vedere: Concedere [le autorizzazioni all'identità gestita dell'area di lavoro.](../security/how-to-grant-workspace-managed-identity-permissions.md)

## <a name="check-the-environment-configuration-file"></a>Controllare il file di configurazione dell'ambiente
Un file di configurazione dell'ambiente può essere usato per aggiornare l'ambiente Conda. Questi formati di file accettabili per la gestione del pool Python sono elencati [qui.](./apache-spark-manage-python-packages.md)

È importante notare le restrizioni seguenti:
   -  Il contenuto del file dei requisiti non deve includere righe o caratteri vuoti aggiuntivi. 
   -  [Synapse Runtime](apache-spark-version-support.md) include un set di librerie preinstallati in ogni pool Apache Spark serverless. I pacchetti preinstallati nel runtime di base non possono essere rimossi o disinstallati.
   -  La modifica della versione di PySpark, Python, Scala/Java, .NET o Spark non è supportata.
   -  Le librerie con ambito sessione Python accettano solo file con estensione YML.

## <a name="validate-wheel-files"></a>Convalidare i file wheel
I pool di Apache Spark serverless synapse sono basati sulla distribuzione Linux. Quando si scaricano e si installano i file Wheel direttamente da PyPI, assicurarsi di selezionare la versione compilata in Linux ed eseguita nella stessa versione di Python del pool di Spark.

>[!IMPORTANT]
>I pacchetti personalizzati possono essere aggiunti o modificati tra le sessioni. Tuttavia, è necessario attendere il riavvio del pool e della sessione per visualizzare il pacchetto aggiornato.

## <a name="check-for-dependency-conflicts"></a>Verificare la presenza di conflitti di dipendenza
 In generale, la risoluzione delle dipendenze Python può essere difficile da gestire. Per facilitare il debug dei conflitti di dipendenza in locale, è possibile creare un ambiente virtuale basato su Synapse Runtime e convalidare le modifiche.

Per ricreare l'ambiente e convalidare gli aggiornamenti:
 1. [Scaricare](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) il modello per ricreare localmente il runtime di Synapse. Potrebbero esserci lievi differenze tra il modello e l'ambiente Synapse effettivo.
   
 2. Creare un ambiente virtuale usando le [istruzioni seguenti.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment) Questo ambiente consente di creare un'installazione di Python isolata con l'elenco specificato di librerie. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Usare ``pip install -r <provide your req.txt file>`` per aggiornare l'ambiente virtuale con i pacchetti specificati. Se l'installazione comporta un errore, è possibile che si sia verificato un conflitto tra ciò che è preinstallato nel runtime di base di Synapse e quello specificato nel file dei requisiti fornito. Questi conflitti di dipendenza devono essere risolti per ottenere le librerie aggiornate nel pool Apache Spark serverless.

>[!IMPORTANT]
>I problemi possono verificarsi quando si usano insieme pip e conda. Quando si combinano pip e conda, è consigliabile seguire queste [procedure consigliate.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: supporto [Apache Spark versione](apache-spark-version-support.md)
