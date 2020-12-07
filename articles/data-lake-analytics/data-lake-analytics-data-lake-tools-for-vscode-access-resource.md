---
title: Accesso alle risorse con strumenti di Data Lake
description: Informazioni su come usare gli strumenti di Azure Data Lake per accedere alle risorse Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754659"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Accesso alle risorse con strumenti di Azure Data Lake

È possibile accedere alle risorse Azure Data Lake Analytics con i comandi o le azioni di Azure Data Tools in VS Code con facilità.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrazione con Azure Data Lake Analytics tramite un comando

È possibile accedere alle risorse di Azure Data Lake Analytics per elencare gli account, accedere ai metadati e visualizzare i processi di analisi.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Per elencare gli account Azure Data Lake Analytics della sottoscrizione di Azure

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: List Accounts** (ADL: Elenca gli account). L'account viene visualizzato nel riquadro **Output**.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Per accedere ai metadati di Azure Data Lake Analytics

1. Premere CTRL+MAIUSC+P e digitare **ADL: List Tables** (ADL: Elenca tabelle).
2. Selezionare uno degli account di Data Lake Analytics.
3. Selezionare uno dei database di Data Lake Analytics.
4. Selezionare uno degli schemi. È possibile visualizzare l'elenco delle tabelle.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Per visualizzare i processi di Azure Data Lake Analytics

1. Aprire il riquadro comandi (CTRL+MAIUSC+P) e selezionare **ADL: Show Jobs** (ADL: Mostra processi).
2. Selezionare un account di Data Lake Analytics o locale.
3. Attendere che venga visualizzato l'elenco dei processi per l'account.
4. Selezionare un processo nell'elenco dei processi. Strumenti Data Lake aprirà la visualizzazione del processo nel riquadro di destra e visualizzerà alcune informazioni nell'output VS Code.

    ![Elenco processi](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrazione con Azure Data Lake Store tramite un comando

È possibile usare i comandi correlati ad Azure Data Lake Store per:

- [Esplorare le risorse di Azure Data Lake Store](#list-the-storage-path)
- [Visualizzare in anteprima il file di Azure Data Lake Store](#preview-the-storage-file)
- Caricare il file direttamente in Azure Data Lake Store in VS Code
- Scaricare il file direttamente da Azure Data Lake Store in VS Code

### <a name="list-the-storage-path"></a>Elencare il percorso di archiviazione

### <a name="to-list-the-storage-path-through-the-command-palette"></a>Per elencare il percorso di archiviazione tramite il riquadro comandi

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: List Path** (ADL: Elenca percorso).
2. Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti un percorso) o su **Browse from Root** (Sfoglia da radice). Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio.
3. Selezionare l'account di Data Lake Analytics.
4. Andare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/.  

Il riquadro comandi elenca le informazioni sul percorso in base all'input.

![Risultati del percorso di archiviazione](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Un modo più pratico per elencare il percorso relativo consiste nell'usare il menu di scelta rapida.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>Per elencare il percorso di archiviazione tramite il menu di scelta rapida

Fare clic con il pulsante destro del mouse sulla stringa del percorso e scegliere **List Path** (Elenca percorso).

!["List Path" (Elenca percorso) nel menu di scelta rapida](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>Visualizzare in anteprima il file di archiviazione

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: Preview File** (ADL: Anteprima file).
2. Selezionare l'account di Data Lake Analytics.
3. Immettere un percorso di file di Archiviazione di Azure, ad esempio /output/SearchLog.txt.

Il file si apre in VS Code.

![Passaggi e risultati per visualizzare in anteprima il file di archiviazione](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Un altro modo per visualizzare l'anteprima del file è attraverso il menu di scelta rapida sul percorso completo o sul percorso relativo del file nell'editor di script.

### <a name="upload-a-file-or-folder"></a>Caricare un file o una cartella

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Upload File** (Carica file) o **Upload Folder** (Carica cartella).
2. Scegliere uno o più file se è stata selezionata l'opzione **Carica file** oppure scegliere l'intera cartella se è stata selezionata l'opzione **Upload Folder** (Carica cartella). Selezionare quindi **Upload** (Carica).
3. Scegliere la cartella di archiviazione nell'elenco o selezionare **Enter Path** (Immetti un percorso) o su **Browse from Root** (Sfoglia da radice). Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio.
4. Selezionare l'account di Data Lake Analytics.
5. Andare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/.
6. Scegliere **Choose Current Folder** (Scegli cartella corrente) per specificare la destinazione di caricamento.

![Passaggi e risultati per il caricamento di un file o di una cartella](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

Un altro modo per caricare file da archiviare è attraverso il menu di scelta rapida sul percorso completo o sul percorso relativo del file nell'editor di script.

È possibile [monitorare lo stato di caricamento](#check-storage-tasks-status).

### <a name="download-a-file"></a>Scaricare un file

È possibile scaricare un file usando i comandi **ADL: Download File** (ADL: Scarica file) o **ADL: Download File (Advanced)** (ADL: Scarica file - (Opzioni avanzate)).

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Per scaricare un file tramite il comando ADL: Download file (Advanced) (ADL: Scarica file - (Opzioni avanzate))

1. Fare clic con il pulsante destro del mouse sull'editor di script e scegliere **Download file (Advanced)** (Scarica file - Opzioni avanzate).
2. VS Code visualizza un file JSON. È possibile immettere i percorsi di file e scaricare più file contemporaneamente. Le istruzioni vengono visualizzate nella finestra **output** . Salvare (CTRL+S) il file JSON per procedere con il download dei file.

    ![File JSON con i percorsi per il download dei file](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

La finestra **Output** mostra lo stato del download dei file.

![Finestra di output con lo stato del download](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

È possibile [monitorare lo stato del download](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Per scaricare un file tramite il comando ADL: Download file (ADL: Scarica file)

1. Fare clic con il pulsante destro del mouse sull'editor di script, scegliere **Download File** (Scarica file) e quindi selezionare la cartella di destinazione nella finestra di dialogo **Select Folder** (Seleziona cartella).

1. Scegliere la cartella nell'elenco o fare clic su **Enter Path** (Immetti un percorso) o su **Browse from Root** (Sfoglia da radice). Questo passaggio usa **Enter a path** (Immetti un percorso) come esempio.

1. Selezionare l'account di Data Lake Analytics.

1. Andare al percorso della cartella di archiviazione o immetterlo manualmente, ad esempio /output/, e scegliere un file da scaricare.

![Passaggi e risultati per il download di un file](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

Un altro modo per scaricare file di archiviazione è attraverso il menu di scelta rapida sul percorso completo o sul percorso relativo del file nell'editor di script.

È possibile [monitorare lo stato del download](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Controllare lo stato delle attività di archiviazione

Sulla barra di stato viene visualizzato lo stato del caricamento e del download. Selezionare la barra di stato: viene visualizzato lo stato nella scheda **OUTPUT**.

![Dettagli della barra di stato e di output](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrazione con Azure Data Lake Analytics da Explorer

Dopo l'accesso, tutte le sottoscrizioni dell'account Azure sono elencate nel riquadro sinistro di **AZURE DATALAKE**.

![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Navigazione nei metadati di Data Lake Analytics

Espandere la sottoscrizione di Azure. Sotto il nodo **Database U-SQL**, è possibile esplorare il database U-SQL e visualizzare cartelle come **Schemi**, **Credenziali**, **Assembly**, **Tabelle** e **Indice**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Gestione delle entità dei metadati di Azure Data Lake Analytics

Espandere i **database U-SQL**. È possibile creare un database, uno schema, una tabella, un tipo di tabella, un indice o una statistica facendo clic sul nodo corrispondente e quindi selezionando **Script to Create** (Script per la creazione) nel menu di scelta rapida. Nella pagina dello script aperta modificare lo script in base alle esigenze. Quindi inviare il processo facendo clic su di esso e selezionando **ADL: Submit Job** (ADL: Invia processo).

Al termine della creazione dell'elemento, fare clic con il pulsante destro del mouse sul nodo e quindi selezionare **Aggiorna** per visualizzare l'elemento. È anche possibile eliminare l'elemento facendo clic con il pulsante destro del mouse su di esso e scegliendo **Elimina**.

![Comando "Script to Create" (Script per la creazione) nel menu di scelta rapida in Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Pagina dello script per il nuovo elemento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Registrazione dell'assembly Data Lake Analytics

È possibile registrare un assembly nel database corrispondente facendo clic con il pulsante destro del mouse sul nodo **Assembly** e selezionando **Registra assembly**.

![Comando "Registra assembly" nel menu di scelta rapida del nodo di Assembly](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrazione con Azure Data Lake Store da Explorer

Passare a **Data Lake Store**:

- Nel nodo della cartella è possibile fare clic con il pulsante destro del mouse e usare i comandi **Aggiorna**, **Elimina**, **Carica**, **Upload Folder** (Carica cartella), **Copia percorso relativo** e **Copia percorso completo** dal menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo di cartella in Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- È possibile fare clic con il pulsante destro del mouse sul nodo dei file e quindi usare i comandi **Anteprima**, **Scarica**, **Elimina**, **Crea script EXTRACT** (disponibile solo per file con estensione csv, tsv e txt), **Copia percorso relativo** e **Copia percorso completo** del menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo di file in Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integrazione con archiviazione BLOB di Azure da Explorer

Andare all'archiviazione BLOB:

- È possibile fare doppio clic con il pulsante destro del mouse sul nodo contenitore BLOB e quindi usare i comandi **Aggiorna**, **Elimina contenitore BLOB** e **Carica BLOB** nel menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo contenitore BLOB nell'archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- È possibile fare clic con il pulsante destro del mouse sul nodo della cartella e quindi usare i comandi **Aggiorna** e **Carica BLOB** nel menu di scelta rapida.

   ![Comandi di menu di scelta rapida per un nodo della cartella nell'archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- È possibile fare clic con il pulsante destro del mouse sul nodo dei file e quindi usare i comandi **Preview/Edit** (Anteprima/Modifica), **Scarica**, **Elimina**, **Crea script EXTRACT** (disponibile solo per file con estensione csv, tsv e txt), **Copia percorso relativo** e **Copia percorso completo** del menu di scelta rapida.

    ![Comandi di menu di scelta rapida per un nodo di file nell'archiviazione BLOB](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>Aprire Esplora risorse di Data Lake Explorer

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer) o fare clic con il pulsante destro del mouse su un percorso relativo o un percorso completo nell'editor di script e quindi scegliere **Open Web Azure Storage Explorer** (Apri Web Azure Storage Explorer).
3. Selezionare un account di Data Lake Analytics.

Strumenti Data Lake apre il percorso di Archiviazione di Azure nel portale di Azure. È possibile trovare il percorso e visualizzare in anteprima il file dal Web.

## <a name="additional-features"></a>Funzionalità aggiuntive

Strumenti Data Lake per VS Code supporta le funzionalità seguenti:

- **Completamento automatico IntelliSense**: vengono visualizzati suggerimenti in finestre popup intorno agli elementi, ad esempio parole chiave, metodi e variabili. Icone diverse rappresentano vari tipi di oggetti:

  - Tipo di dati Scala
  - Tipo di dati complesso
  - UDT integrati
  - Raccolta e classi .NET
  - Espressioni C#
  - UDF, UDO e UDAAG di C# integrati
  - Funzioni di U-SQL
  - Funzioni di windowing di U-SQL

    ![Tipi di oggetti IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Completamento automatico IntelliSense per i metadati di Data Lake Analytics**: Strumenti Data Lake scarica le informazioni di metadati di Data Lake Analytics localmente. La funzionalità IntelliSense consente di popolare automaticamente gli oggetti dai metadati di Data Lake Analytics. Questi oggetti includono il database, lo schema, la tabella, la vista, la funzione con valori di tabella, le procedure e gli assembly C#.

  ![Metadati di IntelliSense](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **Indicatore di errore di IntelliSense**: Strumenti Data Lake sottolinea gli errori di modifica per U-SQL e C#.
- **Evidenziazione della sintassi**: Strumenti Data Lake usa colori diversi per distinguere gli elementi, ad esempio variabili, parole chiave, tipi di dati e funzioni.

    ![Sintassi con diversi colori](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Si consiglia di eseguire l'aggiornamento a Strumenti Azure Data Lake per Visual Studio versione 2.3.3000.4 o successiva. Le versioni precedenti non sono più disponibili per il download e sono deprecate.  

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire lo sviluppo U-SQL con Python, R e CSharp per Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Esercitazione: Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Esercitazione: Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)