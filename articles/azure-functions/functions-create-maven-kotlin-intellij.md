---
title: Creare una funzione Kotlin in Funzioni di Azure usando IntelliJ
description: Informazioni su come usare IntelliJ per creare una semplice funzione Kotlin attivata da HTTP, che verrà quindi pubblicata per l'esecuzione in un ambiente serverless in Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 0207e4af9f845343866714ec207ca306cb327b36
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035173"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Creare la prima funzione Kotlin in Azure usando IntelliJ

Questo articolo illustra come creare una funzione Java attivata da HTTP in un progetto IntelliJ IDEA, eseguire e sottoporre a debug il progetto nell'IDE (ambiente di sviluppo integrato) e infine distribuire il progetto di funzione in un'app per le funzioni in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Per creare e pubblicare funzioni Kotlin in Azure usando IntelliJ, installare il software seguente:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), versione 8
- [Apache Maven](https://maven.apache.org), versione 3.0 o successiva
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), versione Community o Ultimate con Maven
- [Interfaccia della riga di comando di Azure](/cli/azure)
- [Versione 2.x](functions-run-local.md#v2) di Azure Functions Core Tools, che offre un ambiente di sviluppo locale per la scrittura, l'esecuzione e il debug di Funzioni di Azure.

> [!IMPORTANT]
> Per completare la procedura descritta in questo articolo, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

## <a name="create-a-function-project"></a>Creare un progetto per le funzioni

1. In IntelliJ IDEA selezionare **Create New Project** (Crea nuovo progetto).  
1. Nella finestra **New Project** (Nuovo progetto) selezionare **Maven** nel riquadro sinistro.
1. Selezionare la casella di controllo **Create from archetype** (Crea da archetipo) e quindi selezionare **Add Archetype** (Aggiungi archetipo) per [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Nella finestra **Add Archetype** (Aggiungi archetipo) completare i campi come indicato di seguito:
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _Version_: usare la versione più recente disponibile nel [repository centrale](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Creare un progetto Maven dall'archetipo in IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selezionare **OK**, quindi **Avanti**.
1. Immettere i dettagli relativi al progetto corrente e selezionare **Finish** (Fine).

Maven crea i file di progetto in una nuova cartella a cui viene assegnato lo stesso nome del valore _artifactId_. Il codice generato del progetto è una semplice funzione [attivata tramite HTTP](./functions-bindings-http-webhook.md) che restituisce il corpo della richiesta di attivazione dell'HTTP.

## <a name="run-project-locally-in-the-ide"></a>Eseguire il progetto in locale nell'IDE

> [!NOTE]
> Per eseguire e sottoporre a debug il progetto in locale, assicurarsi di aver installato [Azure Functions Core Tools, versione 2](functions-run-local.md#v2).

1. Importare le modifiche manualmente oppure abilitare l'[importazione automatica](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Aprire la barra degli strumenti **Maven Projects** (Progetti Maven).
1. Espandere **Lifecycle** (Ciclo di vita) e quindi aprire **package** (pacchetto). La soluzione viene compilata e inserita in un pacchetto all'interno di una directory di destinazione appena creata.
1. Espandere **Plugins** (Plugin)  > **azure-functions** e aprire **azure-functions:run** per avviare il runtime locale di Funzioni di Azure.  
  ![Barra degli strumenti Maven per Funzioni di Azure](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Dopo avere testato la funzione, chiudere la finestra di dialogo di esecuzione. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

## <a name="debug-the-project-in-intellij"></a>Eseguire il debug del progetto in IntelliJ

1. Per avviare l'host di funzioni in modalità di debug, aggiungere **- DenableDebug** come argomento quando si esegue la funzione. È possibile modificare la configurazione negli [obiettivi di Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) o eseguire il comando seguente in una finestra del terminale:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Con questo comando l'host di funzioni apre una porta di debug 5005.

1. Nel menu **Run** (Esegui) selezionare **Edit Configurations** (Modifica configurazioni).
1. Selezionare **(+)** per aggiungere una **configurazione remota**.
1. Compilare i campi _Name_ (Nome) e _Settings_ (Impostazioni) e quindi selezionare **OK** per salvare la configurazione.
1. Al termine dell'installazione, selezionare **Debug <nome configurazione remota>** oppure premere MAIUSC+F9 sulla tastiera per avviare il debug.

   ![Eseguire il debug del progetto in IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Al termine, arrestare il debugger e il processo in esecuzione. Può essere attivo e in esecuzione in locale un solo host di funzioni alla volta.

## <a name="deploy-the-project-to-azure"></a>Distribuire il progetto in Azure

1. Prima di distribuire il progetto in un'app per le funzioni in Azure, è necessario [accedere usando l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Distribuire il codice in una nuova app per le funzioni usando la destinazione Maven `azure-functions:deploy`. È anche possibile selezionare l'opzione **azure-functions:deploy** nella finestra dei progetti Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Al termine della distribuzione dell'app per le funzioni, individuare l'URL della funzione attivata da HTTP nell'output dell'interfaccia della riga di comando di Azure.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito la prima app per le funzioni Kotlin in Azure, vedere [Guida per sviluppatori Java per Funzioni di Azure](functions-reference-java.md) per altre informazioni sullo sviluppo di funzioni Java e Kotlin.
- Aggiungere al progetto altre app per le funzioni con trigger diversi usando la destinazione Maven `azure-functions:add`.
