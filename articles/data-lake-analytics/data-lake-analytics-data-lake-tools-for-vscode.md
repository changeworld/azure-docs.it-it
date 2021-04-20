---
title: Usare gli Strumenti Azure Data Lake per Visual Studio Code
description: Informazioni su come usare gli Strumenti Azure Data Lake per Visual Studio Code per creare, testare ed eseguire gli script U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 5050e7014d9f1f804f7103938ff3dcb43915c549
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738937"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usare gli Strumenti Azure Data Lake per Visual Studio Code

In questo articolo si apprenderà come usare gli Strumenti Azure Data Lake per Visual Studio Code (VS Code) per creare, testare ed eseguire gli script U-SQL. Le informazioni sono disponibili anche nel video seguente:

[![Lettore video: strumenti di Azure Data Lake per VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Prerequisiti

Gli strumenti Azure Data Lake per VS Code supportano Windows, Linux e MacOS. L'esecuzione e il debug locali di U-SQL funzionano solo in Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

Per MacOS e Linux:

- [.NET Core SDK 5.0](https://www.microsoft.com/net/download/core)
- [Mono 6.12.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installare Azure Data Lake Tools

Dopo avere installato i prerequisiti, è possibile installare Strumenti Azure Data Lake per VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Per installare Strumenti Azure Data Lake

1. Aprire Visual Studio Code.
2. Selezionare **Estensioni** nel riquadro sinistro. Immettere **Strumenti Azure Data Lake** nella casella di ricerca.
3. Selezionare **Installa** accanto a **Strumenti Azure Data Lake**.

   ![Selezioni per l'installazione degli Strumenti Data Lake](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Dopo alcuni secondi il pulsante **Installa** diventa **Ricarica**.
4. Scegliere **Ricarica** per attivare l'estensione **Strumenti Azure Data Lake**.
5. Selezionare **Ricarica finestra** per confermare. È possibile visualizzare **Strumenti Azure Data Lake** nel **riquadro** Estensioni.

## <a name="activate-azure-data-lake-tools"></a>Attivare Strumenti Azure Data Lake

Creare un file con estensione usql o aprirne uno esistente per attivare l'estensione.

## <a name="work-with-u-sql"></a>Uso di U-SQL

Per usare U-SQL, è necessario aprire una cartella o un file U-SQL.

### <a name="to-open-the-sample-script"></a>Per aprire lo script di esempio

Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **ADL: Open Sample Script** (ADL: Apri script di esempio). Viene aperta un'altra istanza di questo esempio. In questa istanza è anche possibile modificare, configurare e inviare uno script.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Aprire una cartella per il progetto U-SQL

1. Da Visual Studio Code selezionare il menu **File** e quindi selezionare **Apri cartella**.
2. Specificare una cartella e quindi scegliere **Seleziona cartella**.
3. Selezionare il menu **File** e quindi scegliere **Nuovo**. Un file Untilted-1 viene aggiunto al progetto.
4. Immettere il codice seguente nel file Untitled-1:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   OUTPUT @departments     TO "/Output/departments.csv" USING Outputters.Csv();

    Lo script crea un file departments.csv con alcuni dati inclusi nella cartella /output.

5. Salvare il file come **myUSQL.usql** nella cartella aperta.

### <a name="to-compile-a-u-sql-script"></a>Compilare uno script U-SQL

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: Compile Script**. I risultati di compilazione vengono visualizzati nella finestra **Output**. È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Compile Script** (ADL: Compila script) per eseguire la compilazione di un processo U-SQL. Il risultato della compilazione viene visualizzato nel riquadro **Output**.

### <a name="to-submit-a-u-sql-script"></a>Inviare uno script U-SQL

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: Submit Job**. È anche possibile fare clic con il pulsante destro del mouse su un file di script e selezionare **ADL: Invia processo**.

Dopo aver inviato un processo U-SQL, i log di invio vengono visualizzati nella finestra **Output** in VS Code. La visualizzazione del processo appare nel riquadro di destra. Se l'invio ha esito positivo, viene visualizzato anche l'URL del processo. È possibile aprire l'URL del processo in un Web browser per monitorare lo stato del processo in tempo reale.

Nella scheda **RIEPILOGO** della visualizzazione del processo è possibile vedere i dettagli del processo. Le funzioni principali includono il reinvio e la duplicazione di uno script e l'apertura nel portale. Nella scheda **DATI** della visualizzazione del processo è possibile fare riferimento a file di input, di output e di risorse. I file possono essere scaricati nel computer locale.

![Scheda Riepilogo nella visualizzazione del processo](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Scheda Dati nella visualizzazione del processo](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Per impostare il contesto predefinito

È possibile impostare il contesto predefinito per applicare questa impostazione a tutti i file di script, se non sono stati impostati parametri specifici per ogni file.

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: Set Default Context** (ADL: Imposta contesto predefinito). Oppure fare clic con il pulsante destro del mouse sull'editor di script e scegliere **ADL: Set Default Context** (ADL: Imposta contesto predefinito).
3. Scegliere l'account, il database e lo schema desiderato. L'impostazione viene salvata nel file di configurazione xxx_settings.json.

   ![Account, database e schema impostati come contesto predefinito](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Per impostare i parametri di script

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: Set Script Parameters** (ADL: Imposta parametri di script).
3. Si apre il file xxx_settings.json con le proprietà seguenti:

   - **account**: un account di Azure Data Lake Analytics nella sottoscrizione di Azure necessario per compilare ed eseguire i processi U-SQL. È necessario configurare l'account del computer prima di compilare ed eseguire i processi di U-SQL.
   - **database:** un database nell'account. Il valore predefinito è **master.**
   - **schema:** uno schema nel database. Il valore predefinito è **dbo**.
   - **optionalSettings**:
        - **priority**: l'intervallo di priorità va da 1 a 1000 dove la priorità più alta corrisponde al valore 1. Il valore predefinito è **1000**.
        - **degreeOfParallelism**: l'intervallo di parallelismo va da 1 a 150. Il valore predefinito è il parallelismo massimo consentito nell'account Azure Data Lake Analytics.

   ![Contenuto del file JSON](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> Dopo il salvataggio della configurazione, se non è stato configurato un contesto predefinito, le informazioni relative ad account, database e schema vengono visualizzate sulla barra di stato nell'angolo inferiore sinistro del file con estensione usql corrispondente.

### <a name="to-set-git-ignore"></a>Per impostare Git Ignore

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: Set Git Ignore (ADL: Imposta Git Ignore).**

   - Se nella cartella di lavoro di VS Code non è presente un file con estensione **gitIgnore**, nella cartella viene creato un file denominato **.gitIgnore**. Per impostazione predefinita, nel file vengono aggiunti quattro elementi: **usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** e **obj**. Se necessario, è possibile effettuare altri aggiornamenti.
   - Se nella cartella di lavoro di VS Code è già presente un file con estensione **gitIgnore** e nel file non sono presenti gli elementi **usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache** e **obj**, lo strumento li aggiunge al file con estensione **gitIgnore**.

   ![Elementi del file con estensione gitIgnore](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Usare file code-behind: C Sharp, Python e R

Azure Data Lake Tools supporta più codici personalizzati. Per istruzioni, vedere [Eseguire lo sviluppo U-SQL con Python, R e C# per Azure Data Lake Analytics in Visual Studio Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Usare gli assembly

Per informazioni sullo sviluppo di assembly, vedere [Develop U-SQL assemblies for Azure Data Lake Analytics jobs]() (Sviluppare assembly U-SQL per i processi di Azure Data Lake Analytics).

È possibile usare Strumenti Data Lake per registrare gli assembly di codice personalizzato nel catalogo di Data Lake Analytics.

### <a name="to-register-an-assembly"></a>Registrare un assembly

È possibile registrare l'assembly tramite il comando **ADL: Register Assembly** (ADL: Registra assembly) o **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (Opzioni avanzate)).

### <a name="to-register-through-the-adl-register-assembly-command"></a>Per eseguire la registrazione mediante il comando ADL: Register Assembly (ADL: Registra assembly)

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: Register Assembly** (ADL: Registra assembly).
3. Specificare il percorso dell'assembly locale.
4. Selezionare un account di Data Lake Analytics.
5. Selezionare un database.

Il portale si apre in un browser e visualizza il processo di registrazione dell'assembly.  

Un modo più pratico per attivare il comando **ADL: Register Assembly** (ADL: Registra assembly) consiste nel fare clic con il pulsante destro del mouse sul file con estensione dll in Esplora file.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Per eseguire la registrazione con il comando ADL: Register Assembly (ADL: Registra assembly (Opzioni avanzate))

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.
2. Immettere **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)).
3. Specificare il percorso dell'assembly locale.
4. Verrà visualizzato il file JSON. Esaminare e modificare le dipendenze dell'assembly e i parametri delle risorse, se necessario. Le istruzioni vengono visualizzate nella **finestra Output.** Salvare (CTRL+S) il file JSON per procedere con la registrazione dell'assembly.

   ![File JSON con dipendenze dell'assembly e i parametri delle risorse](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Strumenti Azure Data Lake rileva automaticamente se la DLL include dipendenze dell'assembly. Le dipendenze vengono visualizzate nel file JSON dopo che sono state rilevate.
>- È possibile caricare le risorse DLL (ad esempio file con estensione txt, png e csv) nel corso della registrazione dell'assembly.

Un altro modo per attivare il comando **ADL: Register Assembly (Advanced)** (ADL: Registra assembly (avanzato)) consiste nel fare clic con il pulsante destro del mouse sul file con estensione dll in Esplora file.

Il codice U-SQL seguente illustra come chiamare un assembly. Nell'esempio il nome dell'assembly è *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Usare l'esecuzione e il debug locali di U-SQL per utenti Windows

L'esecuzione locale di U-SQL verifica i dati locali e convalida lo script localmente prima che il codice venga pubblicato in Data Lake Analytics. È possibile usare la funzionalità di debug locale per completare le attività seguenti prima che il codice venga inviato a Data Lake Analytics:

- Eseguire il debug del code-behind di C#.
- Eseguire il codice passo per passo.
- Convalidare lo script in locale.

La funzionalità di esecuzione locale e debug locale funziona solo in ambienti Windows e non è supportata nei sistemi operativi basati su Linux e macOS.

Per istruzioni sull'esecuzione e il debug locali, vedere [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Connettersi ad Azure

Prima di compilare ed eseguire gli script U-SQL in Data Lake Analytics, è necessario connettersi al proprio account di Azure.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Per connettersi ad Azure tramite un comando

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi.

2. Immettere **ADL: Login** (ADL: Accedi). Le informazioni di accesso vengono visualizzate in basso a destra.

   ![Immissione del comando di accesso](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Notifica di accesso e di autenticazione](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Fare clic su **Copy & Open** (Copia e apri) per aprire la [pagina Web di accesso](https://aka.ms/devicelogin). Incollare il codice nella casella e quindi selezionare **Continua**.

    ![Pagina Web di accesso](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Seguire le istruzioni per accedere dalla pagina Web. Quando si è connessi viene visualizzato il nome di account di Azure sulla barra di stato nell'angolo inferiore sinistro della finestra VS Code.

> [!NOTE]
>
> - Se non si effettua la disconnessione, Data Lake Tools effettuerà automaticamente l'accesso la volta successiva.
> - Se l'account ha due fattori abilitati, è consigliabile usare l'autenticazione telefonica anziché un PIN.

Per disconnettersi immettere il comando **ADL: Logout** (ADL: Disconnetti).

### <a name="to-connect-to-azure-from-the-explorer"></a>Per connettersi ad Azure da Explorer

Espandere **AZURE DATALAKE**, selezionare **Sign in to Azure** (Accedi ad Azure), quindi seguire i passaggi 3 e 4 della sezione [Per connettersi ad Azure tramite un comando](#sign-in-by-command).

![Selezione di "Accedi ad Azure" in Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Non è possibile disconnettersi da Explorer. Per disconnettersi, vedere [Per connettersi ad Azure tramite un comando](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Creare uno script di estrazione

È possibile creare un script di estrazione per file con estensione csv, tsv e txt usando il comando **ADL: Crea script EXTRACT** o da Azure Data Lake Explorer.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Per creare uno script di estrazione tramite un comando

1. Premere CTRL+MAIUSC+P per aprire il riquadro comandi e immettere **ADL: Crea script EXTRACT**.
2. Specificare il percorso completo di un file di Archiviazione di Azure e premere INVIO.
3. Selezionare un account.
4. Per il file con estensione txt, selezionare un delimitatore per estrarre il file.

![Processo per la creazione di uno script di estrazione](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Lo script di estrazione viene generato in base ai valori immessi. Per uno script che non è in grado di rilevare le colonne, scegliere una tra le due opzioni. In caso contrario, verrà generato un solo script.

![Risultato della creazione di uno script di estrazione](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Per creare uno script di estrazione da Explorer

È possibile creare lo script di estrazione anche tramite il menu di scelta rapida che è possibile visualizzare facendo clic con il pulsante destro del mouse sul file con estensione csv, tsv o txt in Azure Data Lake Store o nell'archiviazione BLOB di Azure.

![Comando "Crea script EXTRACT" dal menu di scelta rapida](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire lo sviluppo U-SQL con Python, R e CSharp per Azure Data Lake Analytics in VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Esercitazione: Introduzione ad Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Esercitazione: Sviluppare script U-SQL tramite Strumenti Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
