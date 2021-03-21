---
title: Gestire le librerie Python per Apache Spark
description: Informazioni su come aggiungere e gestire le librerie Python usate da Apache Spark in Azure sinapsi Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 2d6ac02402414f096a46fec0340c3074d8e1784a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586642"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Gestire le librerie Python per Apache Spark in Azure sinapsi Analytics

Le librerie forniscono codice riutilizzabile che può essere utile includere nei programmi o nei progetti. 

Potrebbe essere necessario aggiornare l'ambiente del pool di Apache Spark senza server per vari motivi. Ad esempio, è possibile trovare quanto segue:
- una delle dipendenze principali ha appena rilasciato una nuova versione.
- è necessario un pacchetto aggiuntivo per il training del modello di Machine Learning o la preparazione dei dati.
- è stato trovato un pacchetto migliore e non è più necessario il pacchetto meno recente.

Per rendere disponibile per le applicazioni di terze parti o codice compilato localmente, è possibile installare una libreria in uno dei pool di Apache Spark senza server o nella sessione del notebook. In questo articolo verrà illustrato come gestire le librerie di Python nel pool di Apache Spark senza server.

## <a name="default-installation"></a>Installazione predefinita
Apache Spark in Azure sinapsi Analytics include un set completo di librerie per le attività comuni di progettazione dei dati, preparazione dei dati, apprendimento automatico e visualizzazione dei dati. L'elenco completo delle librerie è disponibile nel [Apache Spark supporto della versione](apache-spark-version-support.md). 

Quando viene avviata un'istanza di Spark, queste librerie verranno incluse automaticamente. È possibile aggiungere altri pacchetti Python e personalizzati a livello di sessione e pool di Spark.

## <a name="pool-libraries"></a>Librerie di pool
Dopo aver identificato le librerie di Python che si vuole usare per l'applicazione Spark, è possibile installarle in un pool Spark. Le librerie a livello di pool sono disponibili per tutti i blocchi appunti e i processi in esecuzione nel pool.

Esistono due modi principali per installare una libreria in un cluster:
-  Installare una libreria dell'area di lavoro caricata come pacchetto dell'area di lavoro.
-  Fornire una specifica dell'ambiente *requirements.txt* o *conda Environment. yml* per installare i pacchetti da repository come PyPI, conda-Forge e altro ancora.

> [!IMPORTANT]
> - Se il pacchetto che si sta installando è di grandi dimensioni o richiede molto tempo per l'installazione, questo influirà sul tempo di avvio dell'istanza di Spark.
> - La modifica della versione di PySpark, Python, scala/Java, .NET o Spark non è supportata.
> - L'installazione di pacchetti da repository esterni come PyPI, conda-Forge o i canali conda predefiniti non è supportata nelle aree di lavoro abilitate per DEP.

### <a name="install-python-packages"></a>Installare i pacchetti Python
I pacchetti Python possono essere installati da repository come PyPI e Conda-Forge fornendo un file di specifica dell'ambiente. 

#### <a name="environment-specification-formats"></a>Formati di specifica dell'ambiente

##### <a name="pip-requirementstxt"></a>requirements.txt PIP
 `pip freeze` Per aggiornare l'ambiente, è possibile usare un file direquirements.txt(output del comando). Quando un pool viene aggiornato, i pacchetti elencati in questo file vengono scaricati da PyPI. Le dipendenze complete vengono quindi memorizzate nella cache e salvate per un successivo riutilizzo del pool. 

Il frammento di codice seguente mostra il formato per il file dei requisiti. Il nome del pacchetto PyPI è elencato insieme a una versione esatta. Questo file segue il formato descritto nella documentazione di riferimento per il [blocco PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . 

In questo esempio viene pin una versione specifica. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>Formato YML (anteprima)
Inoltre, è possibile fornire un file *Environment. yml* per aggiornare l'ambiente del pool. I pacchetti elencati in questo file vengono scaricati dai canali conda predefiniti, conda-Forge e PyPI. È possibile specificare altri canali o rimuovere i canali predefiniti usando le opzioni di configurazione.

Questo esempio specifica i canali e le dipendenze conda/PyPI. 

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
Per informazioni dettagliate sulla creazione di un ambiente da questo file Environment. yml, vedere [creazione di un ambiente da un file Environment. yml](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

#### <a name="update-python-packages"></a>Aggiornare i pacchetti Python
Dopo aver identificato il file di specifica dell'ambiente o il set di librerie che si vuole installare nel pool Spark, è possibile aggiornare le librerie del pool di Spark passando ad Azure sinapsi Studio o portale di Azure. Qui è possibile specificare la specifica dell'ambiente e selezionare le librerie dell'area di lavoro da installare. 

Una volta salvate le modifiche, un processo Spark eseguirà l'installazione e memorizza nella cache l'ambiente risultante per riutilizzarlo in un secondo momento. Al termine del processo, i nuovi processi Spark o le sessioni notebook utilizzeranno le librerie di pool aggiornate. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Gestire i pacchetti da Azure sinapsi Studio o portale di Azure
Le librerie di pool Spark possono essere gestite da Azure sinapsi Studio o portale di Azure. 

Per aggiornare o aggiungere librerie a un pool Spark:
1. Passare all'area di lavoro di Azure sinapsi Analytics dal portale di Azure.

    Se si esegue l'aggiornamento dal **portale di Azure**:

    - Nella sezione **risorse sinapsi** selezionare la scheda **pool di Apache Spark** e selezionare un pool Spark nell'elenco.
     
    - Selezionare i **pacchetti** dalla sezione **Impostazioni** del pool Spark.
  
    ![Schermata che evidenzia il pulsante Carica file di configurazione dell'ambiente.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Aggiungere librerie Python")
   
    Se si esegue l'aggiornamento da **sinapsi Studio**:
    - Selezionare **Gestisci** dal pannello di navigazione principale, quindi selezionare **Apache Spark pool**.

    - Selezionare la sezione **pacchetti** per un pool Spark specifico.
    ![Screenshot che evidenzia l'opzione Carica configurazione ambiente da studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Aggiungere librerie Python da studio")
   
2. Caricare il file di configurazione dell'ambiente utilizzando il selettore file nella sezione  **pacchetti** della pagina.
3. È anche possibile selezionare altri **pacchetti dell'area di lavoro** da aggiungere al pool. 
4. Una volta salvate le modifiche, verrà attivato un processo di sistema per installare e memorizzare nella cache le librerie specificate. Questo processo consente di ridurre i tempi di avvio della sessione complessivi. 
5. Una volta completato il processo, tutte le nuove sessioni preleveranno le librerie di pool aggiornate.

> [!IMPORTANT]
> Selezionando l'opzione per **forzare nuove impostazioni**, si terminano tutte le sessioni correnti per il pool Spark selezionato. Una volta terminate le sessioni, sarà necessario attendere il riavvio del pool. 
>
> Se questa impostazione è deselezionata, sarà necessario attendere la fine della sessione Spark corrente o arrestarla manualmente. Al termine della sessione, sarà necessario consentire il riavvio del pool.


##### <a name="track-installation-progress-preview"></a>Tenere traccia dello stato dell'installazione (anteprima)
Un processo Spark riservato del sistema viene avviato ogni volta che un pool viene aggiornato con un nuovo set di librerie. Questo processo Spark consente di monitorare lo stato dell'installazione della libreria. Se l'installazione non riesce a causa di conflitti di libreria o di altri problemi, il pool Spark verrà ripristinato sullo stato precedente o predefinito. 

Inoltre, gli utenti possono esaminare i log di installazione per identificare i conflitti di dipendenza o vedere quali librerie sono state installate durante l'aggiornamento del pool.

Per visualizzare i log seguenti:
1. Passare all'elenco di applicazioni Spark nella scheda **monitoraggio** . 
2. Selezionare il processo dell'applicazione Spark di sistema che corrisponde all'aggiornamento del pool. Questi processi di sistema vengono eseguiti con il titolo *SystemReservedJob-LibraryManagement* .
   ![Schermata che evidenzia il processo della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Visualizza processo libreria di sistema")
3. Passare alla visualizzazione del **driver** e dei log **stdout** . 
4. Nei risultati vengono visualizzati i log correlati all'installazione delle dipendenze.
    ![Screenshot che evidenzia i risultati dei processi della libreria riservata di sistema.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Visualizza stato processo libreria di sistema")

## <a name="install-wheel-files"></a>Installare i file della rotellina
I file della rotellina Python sono un modo comune per la creazione di pacchetti di librerie Python. All'interno di Azure sinapsi Analytics, gli utenti possono caricare i file delle ruote in un percorso noto l'account Azure Data Lake Storage o caricare usando l'interfaccia dei pacchetti dell'area di lavoro di Azure sinapsi.

### <a name="workspace-packages-preview"></a>Pacchetti dell'area di lavoro (anteprima)
I pacchetti dell'area di lavoro possono essere file di ruote personalizzati o privati. È possibile caricare i pacchetti nell'area di lavoro e successivamente assegnarli a un pool Spark specifico.

Per aggiungere i pacchetti dell'area di lavoro:
1. Passare alla scheda **Gestisci**  >  **pacchetti dell'area di lavoro** .
2. Caricare i file della rotellina usando il selettore file.
3. Una volta caricati i file nell'area di lavoro di sinapsi di Azure, è possibile aggiungere questi pacchetti a un pool di Apache Spark specificato.

![Screenshot che evidenzia i pacchetti dell'area di lavoro.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Visualizzare i pacchetti dell'area di lavoro")

>[!WARNING]
>- All'interno di una sinapsi di Azure, un pool di Apache Spark può sfruttare le librerie personalizzate caricate come pacchetti dell'area di lavoro o caricate in un percorso di Azure Data Lake Storage noto. Tuttavia, entrambe le opzioni non possono essere usate contemporaneamente nello stesso pool di Apache Spark. Se i pacchetti vengono forniti utilizzando entrambi i metodi, verranno installati solo i file della rotellina specificati nell'elenco dei pacchetti dell'area di lavoro. 
>
>- Una volta che i pacchetti dell'area di lavoro (anteprima) vengono usati per installare i pacchetti in un pool di Apache Spark specificato, esiste una limitazione che non è più possibile specificare i pacchetti usando il percorso dell'account di archiviazione nello stesso pool.  

### <a name="storage-account"></a>Account di archiviazione
I pacchetti Wheel personalizzati possono essere installati nel pool di Apache Spark caricando tutti i file della rotellina nell'account Azure Data Lake Storage (Gen2) collegato con l'area di lavoro sinapsi. 

I file devono essere caricati nel percorso seguente nel contenitore predefinito dell'account di archiviazione: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> In alcuni casi, potrebbe essere necessario creare il percorso del file in base alla struttura precedente, se non esiste già. Ad esempio, potrebbe essere necessario aggiungere la ```python``` cartella all'interno della ```libraries``` cartella, se non esiste già.

> [!IMPORTANT]
> Per installare librerie personalizzate usando il metodo di archiviazione di Azure datalake, è necessario disporre delle autorizzazioni di **collaboratore dati BLOB di archiviazione** o di **proprietario dei dati BLOB di archiviazione** per l'account di archiviazione Gen2 primario collegato all'area di lavoro di Azure sinapsi Analytics.


## <a name="session-scoped-packages-preview"></a>Pacchetti con ambito sessione (anteprima)
Oltre ai pacchetti a livello di pool, è anche possibile specificare librerie con ambito sessione all'inizio di una sessione del notebook.  Le librerie con ambito sessione consentono di specificare e usare ambienti Python personalizzati all'interno di una sessione del notebook. 

Quando si usano le librerie con ambito sessione, è importante tenere presente quanto segue:
   - Quando si installano le librerie con ambito sessione, solo il notebook corrente può accedere alle librerie specificate. 
   - Queste librerie non influiscano su altre sessioni o processi che usano lo stesso pool Spark. 
   - Queste librerie vengono installate nelle librerie di runtime di base e a livello di pool. 
   - Le librerie di notebook avranno la precedenza più alta.

Per specificare i pacchetti con ambito sessione:
1.  Passare al pool Spark selezionato e assicurarsi di avere abilitato le librerie a livello di sessione.  È possibile abilitare questa impostazione passando alla scheda **Gestisci**  >  pacchetti del **pool di Apache Spark**  >   . ![abilitare i pacchetti della sessione.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Abilita pacchetti di sessione")
2.  Una volta applicata l'impostazione, è possibile aprire un notebook e selezionare **Configura** >  **pacchetti** di sessione.
  ![Specificare i pacchetti della sessione.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Aggiornare la configurazione di sessione")
3.  Qui è possibile caricare un file conda *Environment. yml* per installare o aggiornare i pacchetti in una sessione. Una volta avviata la sessione, verranno installate le librerie specificate. Una volta terminata la sessione, queste librerie non saranno più disponibili perché sono specifiche della sessione.

## <a name="verify-installed-libraries"></a>Verificare le librerie installate
Per verificare se le versioni corrette delle librerie corrette sono installate da PyPI, eseguire il codice seguente:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
In alcuni casi, per visualizzare le versioni del pacchetto da conda, potrebbe essere necessario controllare la versione del pacchetto singolarmente.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: [supporto della versione Apache Spark](apache-spark-version-support.md)
- Risolvere gli errori di installazione della libreria: [risoluzione degli errori di libreria](apache-spark-troubleshoot-library-errors.md)
- Creare un canale conda privato utilizzando l'account di Azure Data Lake Storage: [canali privati conda](./spark/../apache-spark-custom-conda-channel.md)
