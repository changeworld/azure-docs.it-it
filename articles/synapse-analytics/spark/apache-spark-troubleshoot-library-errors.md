---
title: Risolvere gli errori di installazione della libreria
description: In questa esercitazione viene fornita una panoramica su come risolvere gli errori di installazione della libreria.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 60ea97ea2df271f867febec3fa0f0826a18dbbbf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417458"
---
# <a name="troubleshoot-library-installation-errors"></a>Risolvere gli errori di installazione della libreria 
Per rendere disponibile per le applicazioni di terze parti o codice compilato localmente, è possibile installare una libreria in uno dei pool di Apache Spark senza server. I pacchetti elencati nel file di requirements.txt vengono scaricati da PyPi al momento dell'avvio del pool. Questo file dei requisiti viene usato ogni volta che viene creata un'istanza Spark dal pool Spark. Quando una libreria è installata per un pool Spark, è disponibile per tutte le sessioni che usano lo stesso pool. 

In alcuni casi, è possibile che la libreria che si sta tentando di installare non venga visualizzata nel pool di Apache Spark. Questo caso spesso si verifica quando si verifica un errore nel requirements.txt fornito o nelle librerie specificate. Quando si verifica un errore nel processo di installazione della libreria, il pool di Apache Spark ripristinerà le librerie specificate nel runtime di base della sinapsi.

L'obiettivo di questo documento è fornire problemi comuni e consentire il debug degli errori di installazione della libreria.

## <a name="force-update-your-apache-spark-pool"></a>Forzare l'aggiornamento del pool di Apache Spark
Quando si aggiornano le librerie nel pool di Apache Spark, queste modifiche verranno selezionate una volta riavviato il pool. Se sono presenti processi attivi, questi processi continueranno a essere eseguiti nella versione originale del pool Spark.

È possibile forzare l'applicazione delle modifiche selezionando l'opzione per **forzare nuove impostazioni**. Questa impostazione terminerà tutte le sessioni correnti per il pool Spark selezionato. Una volta terminate le sessioni, sarà necessario attendere il riavvio del pool. 

![Aggiungere librerie Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Aggiungere librerie Python")

## <a name="validate-your-permissions"></a>Convalidare le autorizzazioni
Per installare e aggiornare le librerie, è necessario disporre delle autorizzazioni di **collaboratore dati BLOB di archiviazione** o del **proprietario dei dati BLOB di archiviazione** per l'account di archiviazione Azure Data Lake storage Gen2 primario collegato all'area di lavoro di Azure sinapsi Analytics.

Per convalidare la presenza di queste autorizzazioni, è possibile eseguire il codice seguente:

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
Se viene visualizzato un errore, probabilmente mancano le autorizzazioni necessarie. Per informazioni su come ottenere le autorizzazioni necessarie, vedere questo documento: [assegnare le autorizzazioni di collaboratore dati BLOB di archiviazione o di proprietario di dati BLOB di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-an-azure-built-in-role).

Inoltre, se si esegue una pipeline, l'identità del servizio gestito dell'area di lavoro deve disporre anche di autorizzazioni per il proprietario dei dati BLOB di archiviazione o per i dati BLOB di archiviazione. Per informazioni su come concedere all'identità dell'area di lavoro questa autorizzazione, vedere: [concedere le autorizzazioni all'identità gestita dell'area di lavoro](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-requirements-file"></a>Verificare il file dei requisiti
È possibile utilizzare un file di ***requirements.txt*** (output del comando PIP Freeze) per aggiornare l'ambiente virtuale. Questo file segue il formato descritto nella documentazione di riferimento per il [blocco PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) .

È importante tenere presenti le restrizioni seguenti:
   -  Il nome del pacchetto PyPI deve essere elencato insieme a una versione esatta. 
   -  Il contenuto del file dei requisiti non deve includere righe o caratteri vuoti aggiuntivi. 
   -  Il [runtime di sinapsi](apache-spark-version-support.md) include un set di librerie pre-installate in ogni pool di Apache Spark senza server. Non è possibile effettuare il downgrade dei pacchetti preinstallati nel runtime di base. I pacchetti possono essere aggiunti o aggiornati.
   -  La modifica della versione di PySpark, Python, scala/Java, .NET o Spark non è supportata.

Il frammento di codice seguente mostra il formato richiesto per il file dei requisiti.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

## <a name="validate-wheel-files"></a>Convalidare i file della rotellina
I pool di Apache Spark server sinapsi non sono basati sulla distribuzione Linux. Quando si scaricano e si installano i file della rotellina direttamente da PyPI, assicurarsi di selezionare la versione compilata in Linux e in esecuzione nella stessa versione di Python del pool Spark.

>[!IMPORTANT]
>È possibile aggiungere o modificare pacchetti personalizzati tra le sessioni. Tuttavia, sarà necessario attendere il riavvio del pool e della sessione per visualizzare il pacchetto aggiornato.

## <a name="check-for-dependency-conflicts"></a>Verifica i conflitti di dipendenza
 In generale, la risoluzione delle dipendenze Python può essere difficile da gestire. Per semplificare il debug dei conflitti di dipendenza localmente, è possibile creare un ambiente virtuale basato sul runtime di sinapsi e convalidare le modifiche.

Per ricreare l'ambiente e convalidare gli aggiornamenti:
 1. [Scaricare](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) il modello per ricreare localmente il runtime di sinapsi. Potrebbero esserci lievi differenze tra il modello e l'ambiente sinapsi effettivo.
   
 2. Creare un ambiente virtuale usando le [istruzioni seguenti](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). Questo ambiente consente di creare un'installazione di Python isolata con l'elenco di librerie specificato. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Utilizzare ``pip install -r <provide your req.txt file>`` per aggiornare l'ambiente virtuale con i pacchetti specificati. Se l'installazione genera un errore, potrebbe verificarsi un conflitto tra gli elementi preinstallati nel runtime di base della sinapsi e quelli specificati nel file dei requisiti fornito. È necessario risolvere questi conflitti di dipendenza per ottenere le librerie aggiornate nel pool di Apache Spark senza server.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: [supporto della versione Apache Spark](apache-spark-version-support.md)

