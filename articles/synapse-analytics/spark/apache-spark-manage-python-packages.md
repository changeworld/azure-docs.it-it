---
title: Gestire librerie Python per Apache Spark
description: Informazioni su come aggiungere e gestire librerie Python usate Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 1d64233fc477ec25f91bb73c744b10210571df41
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588344"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gestire librerie Python per Apache Spark in Azure Synapse Analytics

Le librerie forniscono codice riutilizzabile che può essere necessario includere nei programmi o nei progetti. 

Potrebbe essere necessario aggiornare l'ambiente del pool Apache Spark serverless per vari motivi. Ad esempio, è possibile che:
- una delle dipendenze principali ha appena rilasciato una nuova versione.
- È necessario un pacchetto aggiuntivo per il training del modello di Machine Learning o la preparazione dei dati.
- È stato trovato un pacchetto migliore e non è più necessario il pacchetto precedente.

Per rendere disponibile il codice di terze parti o compilato in locale per le applicazioni, è possibile installare una libreria in uno dei pool di Apache Spark serverless o nella sessione del notebook. Questo articolo illustra come gestire le librerie Python nel pool di Apache Spark serverless.

## <a name="default-installation"></a>Installazione predefinita
Apache Spark in Azure Synapse Analytics un set completo di librerie per attività comuni di progettazione dei dati, preparazione dei dati, Machine Learning e visualizzazione dei dati. L'elenco completo delle librerie è disponibile in [Apache Spark versione.](apache-spark-version-support.md) 

All'avvio di un'istanza di Spark, queste librerie verranno incluse automaticamente. È possibile aggiungere altri pacchetti Python e personalizzati a livello di pool e di sessione spark.

## <a name="pool-libraries"></a>Librerie di pool
Dopo aver identificato le librerie Python da usare per l'applicazione Spark, è possibile installarle in un pool di Spark. Le librerie a livello di pool sono disponibili per tutti i notebook e i processi in esecuzione nel pool.

Esistono due modi principali per installare una libreria in un cluster:
-  Installare una libreria dell'area di lavoro caricata come pacchetto dell'area di lavoro.
-  Specificare un *requirements.txt* o *conda environment.yml* per installare pacchetti da repository come PyPI, Conda-Forge e altro ancora.

> [!IMPORTANT]
> - Se il pacchetto che si sta installando è di grandi dimensioni o richiede molto tempo per l'installazione, questo influisce sul tempo di avvio dell'istanza di Spark.
> - La modifica della versione di PySpark, Python, Scala/Java, .NET o Spark non è supportata.
> - L'installazione di pacchetti da repository esterni come PyPI, Conda-Forge o i canali Conda predefiniti non è supportata nelle aree di lavoro abilitate per DEP.

### <a name="install-python-packages"></a>Installare i pacchetti Python
I pacchetti Python possono essere installati da repository come PyPI e Conda-Forge fornendo un file di specifica dell'ambiente. 

#### <a name="environment-specification-formats"></a>Formati di specifica dell'ambiente

##### <a name="pip-requirementstxt"></a>Pip requirements.txt
Un *requirements.txt* file (output del comando) può essere usato `pip freeze` per aggiornare l'ambiente. Quando un pool viene aggiornato, i pacchetti elencati in questo file vengono scaricati da PyPI. Le dipendenze complete vengono quindi memorizzate nella cache e salvate per un successivo riutilizzo del pool. 

Il frammento di codice seguente illustra il formato del file dei requisiti. Il nome del pacchetto PyPI è elencato insieme a una versione esatta. Questo file segue il formato descritto nella documentazione di riferimento [pip freeze.](https://pip.pypa.io/en/stable/reference/pip_freeze/) 

In questo esempio viene aggiunta una versione specifica. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>Formato YML (anteprima)
È anche possibile specificare un file *environment.yml* per aggiornare l'ambiente del pool. I pacchetti elencati in questo file vengono scaricati dai canali Conda predefiniti, Conda-Forge e PyPI. È possibile specificare altri canali o rimuovere i canali predefiniti usando le opzioni di configurazione.

Questo esempio specifica i canali e le dipendenze Conda/PyPI. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Per informazioni dettagliate sulla creazione di un ambiente da questo file environment.yml, vedere Creazione di un [ambiente da un file environment.yml.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
)

#### <a name="update-python-packages"></a>Aggiornare i pacchetti Python
Dopo aver identificato il file di specifica dell'ambiente o il set di librerie da installare nel pool di Spark, è possibile aggiornare le librerie del pool di Spark passando a Azure Synapse Studio o portale di Azure. Qui è possibile specificare la specifica dell'ambiente e selezionare le librerie dell'area di lavoro da installare. 

Dopo aver salvato le modifiche, un processo Spark eseguirà l'installazione e memorizza nella cache l'ambiente risultante per un riutilizzo successivo. Al termine del processo, i nuovi processi Spark o le sessioni del notebook useranno le librerie del pool aggiornate. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gestire i pacchetti da Azure Synapse Studio o portale di Azure
Le librerie del pool di Spark possono essere gestite da Azure Synapse Studio o portale di Azure. 

Per aggiornare o aggiungere librerie a un pool di Spark:
1. Passare all'area Azure Synapse Analytics lavoro dal portale di Azure.

    Se si sta aggiornando dalla **portale di Azure**:

    - Nella sezione **Risorse di Synapse** selezionare la scheda **Apache Spark pool** e selezionare un pool di Spark dall'elenco.
     
    - Selezionare **Pacchetti** nella sezione **Impostazioni** del pool di Spark.
  
    ![Screenshot che evidenzia il pulsante Carica file di configurazione dell'ambiente.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Aggiungere librerie Python")
   
    Se si sta aggiornando dalla **Synapse Studio**:
    - Selezionare **Gestisci** nel pannello di spostamento principale e quindi selezionare Apache Spark **pool.**

    - Selezionare la **sezione** Pacchetti per un pool di Spark specifico.
    ![Screenshot che evidenzia l'opzione di configurazione per il caricamento dell'ambiente da Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Aggiungere librerie Python da Studio")
   
2. Caricare il file di configurazione dell'ambiente usando il selettore di file nella  **sezione** Pacchetti della pagina.
3. È anche possibile selezionare altri pacchetti **dell'area di** lavoro da aggiungere al pool. 
4. Dopo aver salvato le modifiche, verrà attivato un processo di sistema per installare e memorizzare nella cache le librerie specificate. Questo processo consente di ridurre il tempo complessivo di avvio della sessione. 
5. Al termine del processo, tutte le nuove sessioni preleveranno le librerie del pool aggiornate.

> [!IMPORTANT]
> Selezionando l'opzione Forza **nuove impostazioni**, si termineranno tutte le sessioni correnti per il pool Spark selezionato. Al termine delle sessioni, sarà necessario attendere il riavvio del pool. 
>
> Se questa impostazione è deselezionata, sarà necessario attendere la fine della sessione di Spark corrente o arrestarla manualmente. Al termine della sessione, è necessario consentire il riavvio del pool.


##### <a name="track-installation-progress-preview"></a>Tenere traccia dello stato di avanzamento dell'installazione (anteprima)
Un processo Spark riservato di sistema viene avviato ogni volta che un pool viene aggiornato con un nuovo set di librerie. Questo processo Spark consente di monitorare lo stato dell'installazione della libreria. Se l'installazione non riesce a causa di conflitti di libreria o di altri problemi, il pool Spark ripristina lo stato precedente o predefinito. 

Inoltre, gli utenti possono anche esaminare i log di installazione per identificare i conflitti di dipendenza o vedere quali librerie sono state installate durante l'aggiornamento del pool.

Per visualizzare questi log:
1. Passare all'elenco applicazioni Spark nella **scheda** Monitoraggio. 
2. Selezionare il processo dell'applicazione Spark di sistema corrispondente all'aggiornamento del pool. Questi processi di sistema vengono eseguiti con il *titolo SystemReservedJob-LibraryManagement.*
   ![Screenshot che evidenzia il processo della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visualizzare il processo della libreria di sistema")
3. Passare a per visualizzare i **log di driver** e **stdout.** 
4. All'interno dei risultati verranno visualizzati i log correlati all'installazione delle dipendenze.
    ![Screenshot che evidenzia i risultati del processo della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visualizzare lo stato del processo della libreria di sistema")

## <a name="install-wheel-files"></a>Installare i file wheel
I file wheel python sono un modo comune per creare pacchetti di librerie Python. All'Azure Synapse Analytics, gli utenti possono caricare i file wheel in una posizione nota dell'account Azure Data Lake Storage o caricare usando l'interfaccia dei pacchetti dell'Azure Synapse lavoro.

### <a name="workspace-packages-preview"></a>Pacchetti dell'area di lavoro (anteprima)
I pacchetti dell'area di lavoro possono essere file wheel personalizzati o privati. È possibile caricare questi pacchetti nell'area di lavoro e assegnarli successivamente a un pool di Spark specifico.

Per aggiungere pacchetti dell'area di lavoro:
1. Passare alla scheda **Gestisci pacchetti**  >  **dell'area di** lavoro.
2. Caricare i file wheel usando il selettore di file.
3. Dopo aver caricato i file nell'area di lavoro Azure Synapse, è possibile aggiungere questi pacchetti a un pool Apache Spark specificato.

![Screenshot che evidenzia i pacchetti dell'area di lavoro.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Visualizzare i pacchetti dell'area di lavoro")

>[!WARNING]
>- All Azure Synapse, un pool Apache Spark può sfruttare librerie personalizzate caricate come pacchetti dell'area di lavoro o caricate all'interno di un percorso Azure Data Lake Storage noto. Tuttavia, entrambe queste opzioni non possono essere usate contemporaneamente all'interno dello stesso pool Apache Spark. Se i pacchetti vengono forniti usando entrambi i metodi, verranno installati solo i file wheel specificati nell'elenco Pacchetti dell'area di lavoro. 
>
>- Quando i pacchetti dell'area di lavoro (anteprima) vengono usati per installare i pacchetti in un determinato pool di Apache Spark, esiste una limitazione che non è più possibile specificare i pacchetti usando il percorso dell'account di archiviazione nello stesso pool.  

### <a name="storage-account"></a>Account di archiviazione
I pacchetti wheel personalizzati possono essere installati nel pool di Apache Spark caricando tutti i file wheel nell'account di Azure Data Lake Storage (Gen2) collegato all'area di lavoro synapse. 

I file devono essere caricati nel percorso seguente nel contenitore predefinito dell'account di archiviazione: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> In alcuni casi, potrebbe essere necessario creare il percorso del file in base alla struttura precedente, se non esiste già. Ad esempio, potrebbe essere necessario aggiungere la ```python``` cartella all'interno della ```libraries``` cartella se non esiste già.

> [!IMPORTANT]
> Per installare librerie personalizzate usando il metodo di  archiviazione Di Azure  DataLake, è necessario disporre delle autorizzazioni Collaboratore dati BLOB di archiviazione o Proprietario dati BLOB di archiviazione nell'account di archiviazione gen2 primario collegato all'area di lavoro Azure Synapse Analytics archiviazione.


## <a name="session-scoped-packages-preview"></a>Pacchetti con ambito sessione (anteprima)
Oltre ai pacchetti a livello di pool, è anche possibile specificare librerie con ambito sessione all'inizio di una sessione del notebook.  Le librerie con ambito sessione consentono di specificare e usare ambienti Python personalizzati all'interno di una sessione di notebook. 

Quando si usano librerie con ambito sessione, è importante tenere presenti i punti seguenti:
   - Quando si installano librerie con ambito sessione, solo il notebook corrente ha accesso alle librerie specificate. 
   - Queste librerie non incideranno su altre sessioni o processi che usano lo stesso pool spark. 
   - Queste librerie vengono installate nelle librerie a livello di pool e di runtime di base. 
   - Le librerie notebook avranno la precedenza più elevata.

Per specificare pacchetti con ambito sessione:
1.  Passare al pool Spark selezionato e assicurarsi di aver abilitato le librerie a livello di sessione.  È possibile abilitare questa impostazione passando alla scheda Gestisci Apache Spark  >  **pool**  >  **di** applicazioni. ![Abilitare i pacchetti di sessione.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Abilitare i pacchetti di sessione")
2.  Dopo aver applicato l'impostazione, è possibile aprire un notebook e selezionare **Configura pacchetti di** >  **sessione**.
  ![Specificare i pacchetti di sessione.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Aggiornare la configurazione della sessione")
3.  In questo caso, è possibile caricare un file conda *environment.yml* per installare o aggiornare i pacchetti all'interno di una sessione. Dopo aver avviato la sessione, verranno installate le librerie specificate. Al termine della sessione, queste librerie non saranno più disponibili perché sono specifiche della sessione.

## <a name="verify-installed-libraries"></a>Verificare le librerie installate
Per verificare se da PyPI sono installate le versioni corrette delle librerie corrette, eseguire il codice seguente:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
In alcuni casi, per visualizzare le versioni del pacchetto da Conda, potrebbe essere necessario esaminare singolarmente la versione del pacchetto.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: supporto [Apache Spark versione](apache-spark-version-support.md)
- Risolvere gli errori di installazione della libreria: [Risolvere gli errori della libreria](apache-spark-troubleshoot-library-errors.md)
- Creare un canale Conda privato usando l'account Azure Data Lake Storage: [canali privati Conda](./spark/../apache-spark-custom-conda-channel.md)
