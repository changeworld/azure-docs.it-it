---
title: Synapse Studio notebook
description: Questo articolo illustra come creare e sviluppare notebook di Azure Synapse Studio per la preparazione e la visualizzazione dei dati.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 4230ced172de52e5acf45e071fa2a49a332eb696
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719224"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Creare, sviluppare e gestire Synapse Studio notebook in Azure Synapse Analytics

Un Synapse Studio notebook è un'interfaccia Web che consente di creare file contenenti codice live, visualizzazioni e testo narrativo. I notebook possono essere usati per convalidare idee ed eseguire esperimenti rapidi per ottenere informazioni cognitive dettagliate dai dati. I notebook sono anche ampiamente usati per la preparazione e la visualizzazione dei dati, l'apprendimento automatico e altri scenari di Big Data.

Con un notebook di Azure Synapse Studio è possibile:

* Iniziare senza attività di configurazione.
* Mantenere i dati protetti con le funzionalità di sicurezza aziendali predefinite.
* Analizzare i dati in formati non elaborati (CSV, txt, JSON e così via), formati di file elaborati (parquet, Delta Lake, ORC e così via) e file di dati tabulari SQL in Spark e SQL.
* Ottenere produttività con funzionalità avanzate di creazione e visualizzazione dei dati predefinite.

Questo articolo descrive come usare i notebook in Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Anteprima della nuova esperienza del notebook
Il team di Synapse ha inserito il nuovo componente notebook in Synapse Studio per offrire un'esperienza di notebook coerente per i clienti Microsoft e ottimizzare l'individuabilità, la produttività, la condivisione e la collaborazione. La nuova esperienza del notebook è pronta per l'anteprima. Selezionare il **pulsante Funzionalità di anteprima** nella barra degli strumenti del notebook per attivarlo. La tabella seguente illustra il confronto delle funzionalità tra il notebook esistente (denominato "notebook classico") e il nuovo notebook di anteprima.  

|Funzionalità|Notebook classico|Notebook di anteprima|
|--|--|--|
|%run| Non supportato | &#9745;|
|%history| Non supportato |&#9745;
|%load| Non supportato |&#9745;|
|%%html| Non supportato |&#9745;|
|Trascinare e rilasciare per spostare una cella| Non supportato |&#9745;|
|Formattare la cella di testo con i pulsanti della barra degli strumenti|&#9745;| Non disponibile |
|Annullare l'operazione di cella| &#9745;| Non disponibile |


## <a name="create-a-notebook"></a>Creare un notebook

È possibile creare un notebook in due modi. È possibile creare un nuovo notebook o importarne uno esistente in un'area di lavoro Azure Synapse da **Esplora oggetti**. I notebook di Azure Synapse Studio sono in grado di riconoscere i file con estensione ipynb standard di Jupyter Notebook.

![creare un notebook di importazione](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Sviluppare i notebook

I notebook sono costituiti da celle, ovvero singoli blocchi di codice o testo che possono essere eseguiti in modo indipendente o come gruppo.

### <a name="add-a-cell"></a>Aggiungere una cella

Esistono diversi modi per aggiungere una nuova cella al notebook.

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

1. Espandere il pulsante in alto a sinistra **+ Cella**, quindi selezionare **Aggiungi cella di codice** o **Aggiungi cella di testo**.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Passare con il mouse sullo spazio tra due celle e selezionare **Aggiungi codice** o **Aggiungi testo**.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **A** per inserire una cella al di sopra della cella corrente. Premere **B** per inserire una cella sotto la cella corrente.


# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

1. Espandere il pulsante superiore sinistro **+** Cella e selezionare la **cella di codice** o la cella **Markdown.**
    ![add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Selezionare il segno più all'inizio di una cella e selezionare **Cella di codice** o Cella **Markdown**.

    ![add-azure-notebook-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Usare [i tasti di scelta rapida aznb in modalità comando](#shortcut-keys-under-command-mode). Premere **A** per inserire una cella al di sopra della cella corrente. Premere **B** per inserire una cella sotto la cella corrente.

---

### <a name="set-a-primary-language"></a>Impostare il linguaggio primario

I notebook di Azure Synapse Studio supportano quattro linguaggi Apache Spark:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET per Apache Spark (C#)

È possibile impostare il linguaggio primario per le nuove celle aggiunte dall'elenco a discesa nella barra dei comandi superiore.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Usare più linguaggi

È possibile usare più linguaggi in un notebook specificando il comando magic corretto per il linguaggio all'inizio di una cella. Nella tabella seguente sono elencati i comandi magic per passare da un linguaggio all'altro nelle celle.

|Comando magic |Linguaggio | Descrizione |  
|---|------|-----|
|%%pyspark| Python | Eseguire una query **Python** sul contesto Spark.  |
|%%spark| Scala | Eseguire una query **Scala** sul contesto Spark.  |  
|%%sql| SparkSQL | Eseguire una query **SparkSQL** sul contesto Spark.  |
|%%csharp | .NET per Spark C# | Eseguire una query **.NET per Spark C#** sul contesto Spark. |

L'immagine seguente è un esempio di come è possibile scrivere una query PySpark usando il comando magic **%% PySpark** o una query SparkSQL con il comando magic **%% SQL** in un notebook **Spark(Scala)** . Si noti che il linguaggio primario per il notebook è impostato su pySpark.

   ![Comandi magic di Synapse Spark](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Usare tabelle temporanee per fare riferimento ai dati tra linguaggi

Non è possibile fare riferimento a dati o variabili direttamente tra linguaggi diversi in un notebook di Synapse Studio. In Spark è possibile fare riferimento a una tabella temporanea tra i vari linguaggi. Di seguito è riportato un esempio di come leggere un DataFrame `Scala` in `PySpark` e `SparkSQL` usando una tabella temporanea di Spark come soluzione alternativa.

1. Nella cella 1 leggere un dataframe da un connettore del pool SQL usando Scala e creare una tabella temporanea.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. Nella cella 2 eseguire una query sui dati usando Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Nella cella 3 usare i dati in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense di tipo IDE

I notebook di Azure Synapse Studio sono integrati nell'editor Monaco e consentono di aggiungere la funzionalità IntelliSense di tipo IDE all'editor di celle. L'evidenziazione della sintassi, il marcatore di errore e i completamenti automatici del codice consentono di scrivere codice e identificare i problemi più rapidamente.

Le funzionalità di IntelliSense hanno livelli di maturità diversi per i diversi linguaggi. Usare la tabella seguente per vedere cosa è supportato.

|Languages| Evidenziazione della sintassi | Generatore di errori della sintassi  | Completamento del codice della sintassi | Completamento del codice della variabile| Completamento del codice della funzione di sistema| Completamento del codice della funzione utente| Rientro automatico | Riduzione del codice|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sì|Sì|Sì|Sì|Sì|Sì|Sì|Sì|
|Spark (Scala)|Sì|Sì|Sì|Sì|-|-|-|Sì|
|SparkSQL|Sì|Sì|-|-|-|-|-|-|
|.NET per Spark (C#)|Sì|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Formattare la cella di testo con i pulsanti della barra degli strumenti

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

È possibile usare i pulsanti di formato nella barra degli strumenti delle celle di testo per eseguire azioni di markdown comuni, tra cui l'applicazione di grassetto o corsivo al testo, l'inserimento di frammenti di codice, l'inserimento di elenchi non ordinati, l'inserimento di elenchi ordinati e l'inserimento di immagini dall'URL.

  ![Barra degli strumenti della cella di testo synapse](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

La barra degli strumenti del pulsante Formato non è ancora disponibile per l'esperienza del notebook di anteprima. 

---

### <a name="undo-cell-operations"></a>Annullare operazioni sulle celle

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Selezionare il **pulsante Annulla** o premere **CTRL+Z** per revocare l'operazione più recente sulla cella. A questo punto è possibile annullare le ultime 20 azioni cronologiche sulle celle. 

   ![Celle di annullamento synapse](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

L'operazione di annullamento della cella non è ancora disponibile per l'esperienza di anteprima del notebook. 

---

### <a name="move-a-cell"></a>Spostare una cella

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Selezionare i puntini di sospensione (...) per accedere al menu aggiuntivo delle azioni sulle celle all'estrema destra. Selezionare quindi **Sposta la cella in alto** o **Sposta la cella in basso** per spostare la cella corrente. 

È anche possibile usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **CTRL + ALT + ↑** per spostare verso l'alto la cella corrente. Premere **CTRL + ALT + ↓** per spostare verso il basso la cella corrente.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Fare clic sul lato sinistro di una cella e trascinarlo nella posizione desiderata. 
    ![Spostare le celle di Synapse](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Eliminare una cella

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Per eliminare una cella, selezionare i puntini di sospensione (...) per accedere al menu aggiuntivo delle azioni sulle celle all'estrema destra e selezionare **Elimina cella**. 

È anche possibile usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **D,D** per eliminare la cella corrente.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Per eliminare una cella, selezionare il pulsante Elimina a destra della cella. 

È anche possibile usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **MAIUSC+D** per eliminare la cella corrente. 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Comprimere l'input di una cella

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Selezionare il pulsante freccia nella parte inferiore della cella corrente per comprimerlo. Per espanderlo, selezionare il pulsante freccia mentre la cella è compressa.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Selezionare i **puntini di sospensione** (...) di Altri comandi sulla barra degli strumenti della cella e **immettere per** comprimere l'input della cella corrente. Per espanderlo, selezionare **l'input nascosto** mentre la cella è compressa.

   ![azure-notebook-collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Comprimere l'output di una cella

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Selezionare il **pulsante comprimi output** in alto a sinistra dell'output della cella corrente per comprimerlo. Per espanderla, selezionare Mostra **output cella** mentre l'output della cella è compresso.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Selezionare i **puntini di sospensione** Altri comandi (...) sulla barra degli strumenti della cella e **l'output** per comprimere l'output della cella corrente. Per espanderlo, selezionare lo stesso pulsante mentre l'output della cella è nascosto.

   ![azure-notebook-collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Eseguire i notebook

È possibile eseguire le celle di codice nel notebook singolarmente o tutte insieme. Lo stato e l'avanzamento di ogni cella sono rappresentati nel notebook.

### <a name="run-a-cell"></a>Eseguire una cella

Esistono diversi modi per eseguire il codice in una cella.

1. Passare con il puntatore del mouse sulla cella che si desidera eseguire e selezionare il pulsante **Esegui cella** oppure premere **CTRL + INVIO**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Usare la [combinazione di tasti in modalità comando](#shortcut-keys-under-command-mode). Premere **MAIUSC + INVIO** per eseguire la cella corrente e selezionare la cella al di sotto. Premere **ALT + INVIO** per eseguire la cella corrente e inserire una nuova cella al di sotto.

---

### <a name="run-all-cells"></a>Eseguire tutte le celle
Selezionare il **pulsante Esegui tutto** per eseguire tutte le celle del notebook corrente in sequenza.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Eseguire tutte le celle al di sopra o al di sotto

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Selezionare i puntini di sospensione **(...)** all'estrema destra per accedere al menu aggiuntivo delle azioni sulle celle. Selezionare quindi **Esegui celle sopra** per eseguire tutte le celle sopra quella corrente in sequenza. Selezionare **Esegui celle sotto** per eseguire tutte le celle sotto quella corrente in sequenza.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Espandere l'elenco a discesa **dal** pulsante Esegui tutto e quindi selezionare **Esegui celle sopra** per eseguire tutte le celle sopra la corrente in sequenza. Selezionare **Esegui celle sotto** per eseguire tutte le celle sotto quella corrente in sequenza.

   ![azure-notebook-run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Annullare tutte le celle in esecuzione

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)
Selezionare il **pulsante Annulla tutto** per annullare le celle in esecuzione o le celle in attesa nella coda. 
   ![cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Selezionare il **pulsante Annulla tutto** per annullare le celle in esecuzione o le celle in attesa nella coda. 
   ![azure-notebook-cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Informazioni di riferimento sui notebook

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Non supportata.

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

È possibile usare il ```%run <notebook path>``` comando magic per fare riferimento a un altro notebook all'interno del contesto del notebook corrente. Tutte le variabili definite nel notebook di riferimento sono disponibili nel notebook corrente. ```%run``` Il comando magic supporta le chiamate annidate, ma non le chiamate ricorsive. Si riceverà un'eccezione se la profondità dell'istruzione è maggiore di cinque. ```%run``` il comando supporta attualmente solo il passaggio di un percorso del notebook come parametro. 

Esempio: ``` %run /path/notebookA ```.

> [!NOTE]
> Le informazioni di riferimento sui notebook non sono supportate nella pipeline synapse.
>
>

---


### <a name="cell-status-indicator"></a>Indicatore di stato delle celle

Sotto la cella viene visualizzato il relativo stato di esecuzione dettagliato, che indica lo stato di avanzamento corrente. Al termine dell'esecuzione della cella, viene visualizzato un riepilogo dell'esecuzione con la durata totale e l'ora di fine, informazioni che verranno mantenute per riferimento futuro.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicatore di avanzamento Spark

Il notebook di Azure Synapse Studio è basato esclusivamente su Spark. Le celle di codice vengono eseguite nel pool Apache Spark serverless in modalità remota. Viene fornito un indicatore di stato del processo Spark con una barra di avanzamento in tempo reale che consente di comprendere lo stato di esecuzione del processo.
Il numero di attività per ogni processo o fase consente di identificare il livello parallelo del processo Spark. È anche possibile eseguire il drill-down nell'interfaccia utente di Spark di un processo specifico (o fase) selezionando il collegamento nel nome del processo (o della fase).


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configurazione della sessione Spark

È possibile specificare la durata del timeout, il numero e le dimensioni degli executor da assegnare alla sessione Spark corrente in **Configura sessione**. Riavviare la sessione di Spark per rendere effettive le modifiche alla configurazione. Tutte le variabili del notebook memorizzate nella cache vengono cancellate.

[![gestione delle sessioni](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Comando magic di configurazione della sessione Spark
È anche possibile specificare le impostazioni della sessione Spark tramite un comando **magic %%configure.** La sessione spark deve essere riavviata per rendere effettive le impostazioni. È consigliabile eseguire **%%configure** all'inizio del notebook. Di seguito è riportato un esempio, vedere https://github.com/cloudera/livy#request-body per l'elenco completo dei parametri validi 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> Il comando magic di configurazione della sessione Spark non è supportato nella pipeline synapse.
>
>

## <a name="bring-data-to-a-notebook"></a>Importare i dati in un notebook

È possibile caricare i dati da Archiviazione BLOB di Azure, Azure Data Lake Store Gen 2 e dal pool SQL, come illustrato negli esempi di codice riportati di seguito.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Leggere un CSV da Azure Data Lake Store Gen2 come un DataFrame di Spark

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leggere un CSV da Archiviazione BLOB di Azure come un DataFrame di Spark

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Leggere i dati dall'account di archiviazione primario

È possibile accedere direttamente ai dati nell'account di archiviazione primario. Non è necessario fornire le chiavi private. In Esplora dati fare clic con il pulsante destro del mouse su un file e selezionare **Nuovo notebook** per visualizzare un nuovo notebook con l'estrazione dati generata automaticamente.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Salvare i notebook

È possibile salvare un singolo notebook o tutti i notebook nell'area di lavoro.

1. Per salvare le modifiche apportate a un singolo notebook, selezionare il pulsante **Pubblica** sulla barra dei comandi del notebook.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Per salvare tutti i notebook nell'area di lavoro, selezionare il pulsante **Pubblica tutti i** sulla barra dei comandi dell'area di lavoro. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nelle proprietà del notebook è possibile specificare se includere l'output della cella durante il salvataggio.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandi magic
È possibile usare i comandi magic di Jupyter familiari nei notebook Azure Synapse Studio. Esaminare l'elenco seguente come comandi magic attualmente disponibili. Indicare i [casi d'uso in GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) in modo da poter continuare a creare altri comandi magic per soddisfare le proprie esigenze.

> [!NOTE]
> Nella pipeline synapse sono supportati solo i comandi magic seguenti: %%pyspark, %%spark, %%csharp, %%sql. 
>
>

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Comandi magic disponibili per le righe: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Magics di cella disponibili: [%%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%%timeit,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%capture,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [%%writefile,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) [%%sql,](#use-multiple-languages) [%%pyspark,](#use-multiple-languages) [%%spark,](#use-multiple-languages) [%%csharp,](#use-multiple-languages)[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Maghi di riga disponibili: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%timeit,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%history,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history) [%run,](#notebook-reference) [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Magics di cella disponibili: [%%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%%timeit,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%capture,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [%%writefile,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) [%%sql,](#use-multiple-languages) [%%pyspark,](#use-multiple-languages) [%%spark,](#use-multiple-languages) [%%csharp,](#use-multiple-languages) [%%html,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html) [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrare un notebook

### <a name="add-a-notebook-to-a-pipeline"></a>Aggiungere un notebook a una pipeline

Selezionare il **pulsante Aggiungi alla pipeline** nell'angolo superiore destro per aggiungere un notebook a una pipeline esistente o creare una nuova pipeline.

![Aggiungere un notebook alla pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Designare una cella di parametri

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Per impostare parametri per il notebook, selezionare i puntini di sospensione (...) per accedere al menu azioni cella aggiuntive all'estrema destra. Selezionare quindi **Attiva/Disattiva cella parametro** per designare la cella come cella dei parametri.

![toggle-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

Per impostare parametri per il notebook, selezionare i puntini di sospensione (...) per accedere a **più comandi sulla** barra degli strumenti della cella. Selezionare quindi **Attiva/Disattiva cella parametro** per designare la cella come cella dei parametri.

![azure-notebook-toggle-parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory cerca la cella dei parametri e considera questa cella come valori predefiniti per i parametri passati in fase di esecuzione. Il motore di esecuzione aggiungerà una nuova cella sotto la cella dei parametri con parametri di input per sovrascrivere i valori predefiniti. Quando una cella di parametri non è designata, la cella inserita verrà inserita nella parte superiore del notebook.


### <a name="assign-parameters-values-from-a-pipeline"></a>Assegnare i valori dei parametri da una pipeline

Dopo aver creato un notebook con parametri, è possibile eseguirlo da una pipeline con l'attività Azure Synapse Notebook. Dopo aver aggiunto l'attività al canvas della pipeline, sarà possibile impostare i valori dei parametri nella sezione **Parametri** di base della **scheda** Impostazioni. 

![Assegnare un parametro](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Quando si assegnano valori di parametro, è possibile usare il linguaggio delle [espressioni della pipeline](../../data-factory/control-flow-expression-language-functions.md) o le variabili di [sistema](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Combinazioni di tasti

Analogamente a Jupyter Notebook, i notebook di Azure Synapse Studio hanno un'interfaccia utente modale. La tastiera esegue diverse operazioni a seconda della modalità in cui si trova la cella del notebook. I notebook di Synapse Studio supportano le due modalità seguenti per una cella di codice specificata, ovvero la modalità di comando e la modalità di modifica.

1. Una cella è in modalità di comando quando non è presente un cursore di testo che richiede di digitare. Quando una cella è in modalità di comando, è possibile modificare il notebook nel suo complesso, ma non digitare in singole celle. Per accedere alla modalità comando, `ESC` premere o usare il mouse per selezionare all'esterno dell'area dell'editor di una cella.

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. La modalità di modifica è indicata da un cursore di testo che richiede di digitare nell'area dell'editor. Quando una cella è in modalità di modifica, è possibile digitare nella cella. Per accedere alla modalità di `Enter` modifica, premere o usare il mouse per selezionare l'area dell'editor di una cella.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Combinazione di tasti in modalità comando

# <a name="classical-notebook"></a>[Notebook classico](#tab/classical)

Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice nei notebook di Azure Synapse.

| Azione |Scelte rapide del notebook di Synapse Studio  |
|--|--|
|Eseguire la cella corrente e selezionare in basso | MAIUSC+INVIO |
|Eseguire la cella corrente e inserire in basso | ALT+INVIO |
|Selezionare la cella in alto| Su |
|Selezionare la cella in basso| Giù |
|Inserire la cella in alto| Una |
|Inserire la cella in basso| b |
|Estendere le celle selezionate in alto| MAIUSC+freccia SU |
|Estendere le celle selezionate in basso| Shift+Down|
|Spostare la cella in alto| CTRL + ALT + ↑ |
|Spostare la cella in basso| CTRL+Alt+↓ |
|Eliminare le celle selezionate| D, D |
|Passare alla modalità di modifica| Immettere |

# <a name="preview-notebook"></a>[Notebook di anteprima](#tab/preview)

| Azione |Scelte rapide del notebook di Synapse Studio  |
|--|--|
|Eseguire la cella corrente e selezionare in basso | MAIUSC+INVIO |
|Eseguire la cella corrente e inserire in basso | ALT+INVIO |
|Esegui cella corrente| CTRL+INVIO |
|Selezionare la cella in alto| Su |
|Selezionare la cella in basso| Giù |
|Selezionare la cella precedente| K |
|Selezionare la cella successiva| J |
|Inserire la cella in alto| Una |
|Inserire la cella in basso| b |
|Eliminare le celle selezionate| MAIUSC+D |
|Passare alla modalità di modifica| Immettere |

---

### <a name="shortcut-keys-under-edit-mode"></a>Tasti di scelta rapida in modalità di modifica


Usando i tasti di scelta rapida seguenti, è possibile esplorare ed eseguire più facilmente il codice nei notebook di Azure Synapse in modalità di modifica.

| Azione |Scelte rapide del notebook di Synapse Studio  |
|--|--|
|Spostare il cursore in alto | Su |
|Spostare in cursore in basso|Giù|
|Annullamento|CTRL + Z|
|Ripristinare|CTRL + Y|
|Inserimento/Rimozione di commenti|CTRL + /|
|Eliminare la parola prima|CTRL + BACKSPACE|
|Eliminare la parola dopo|CTRL + CANC|
|Andare all'inizio della cella|CTRL + Home|
|Andare alla fine della cella |CTRL + Fine|
|Andare a sinistra di una parola|CTRL + freccia sinistra|
|Andare a destra di una parola|CTRL + freccia destra|
|Selezionare tutto|CTRL + A|
|Impostare un rientro| CTRL+]|
|Annullare l'impostazione di un rientro|CTRL + [|
|Passare alla modalità comandi| ESC |

---

## <a name="next-steps"></a>Passaggi successivi
- [Vedere Notebook di esempio di Synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Avvio rapido: Creare un pool di Apache Spark in Azure Synapse Analytics con gli strumenti Web](../quickstart-apache-spark-notebook.md)
- [Che cos'è Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)
- [Usare .NET per Apache Spark con Azure Synapse Analytics](spark-dotnet.md)
- [Documentazione di .NET per Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)