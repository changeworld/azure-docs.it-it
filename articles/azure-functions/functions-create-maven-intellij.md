---
title: Creare una funzione Java in funzioni di Azure usando IntelliJ
description: Informazioni su come usare IntelliJ per creare una semplice funzione Java attivata da HTTP, che viene quindi pubblicata per l'esecuzione in un ambiente senza server in Azure.
author: yucwan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: yucwan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 45fb62b446e6b589dc0cb9287a8aebe7f4e699b1
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543644"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>Creare la prima funzione Java in Azure con IntelliJ

Questo articolo illustra:
- Come creare una funzione Java attivata da HTTP in un progetto IntelliJ IDEA.
- Passaggi per il test e il debug del progetto nel Integrated Development Environment (IDE) nel computer.
- Come distribuire il progetto di funzione in Funzioni di Azure

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Per creare e pubblicare funzioni Java in Azure usando IntelliJ, installare il software seguente:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ [Java Development Kit (JDK) supportato da Azure](/azure/developer/java/fundamentals/java-jdk-long-term-support) per Java 8
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition o Community Edition installato
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Ultima versione di [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installazione e accesso

1. Nella finestra di dialogo Settings/Preferences (Impostazioni/Preferenze) (CTRL+ALT+S) di IntelliJ IDEA selezionare **Plugins**. Quindi individuare **Azure Toolkit for IntelliJ** nel **Marketplace** e fare clic su **Installa**. Dopo l'installazione, fare clic su **Riavvia** per attivare il plug-in. 

    ![Plug-in Azure Toolkit for IntelliJ in Marketplace][marketplace]

2. Per accedere all'account Azure, aprire **Azure Explorer** sulla barra laterale e quindi fare clic su **Azure Sign In** (Accesso ad Azure) sulla barra superiore. Oppure scegliere **Tools/Azure/Azure Sign in** (Strumenti/Azure/Accesso ad Azure) dal menu IDEA.
    ![Comando di accesso ad Azure in IntelliJ][intellij-azure-login]

3. Nella finestra **Azure Sign In** (Accesso ad Azure) selezionare **Device Login** (Accesso dispositivo) e quindi fare clic su **Sign in** (Accedi) ([altre opzioni di accesso](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Finestra di accesso ad Azure con l'accesso dispositivo selezionato][intellij-azure-popup]

4. Nella finestra di dialogo **Azure Device Login** (Accesso dispositivo Azure) fare clic su **Copy&Open** (Copia e apri).

   ![Finestra di dialogo di accesso ad Azure][intellij-azure-copycode]

5. Nel browser incollare il codice dispositivo (copiato facendo clic su **Copy&Open** nell'ultimo passaggio) e quindi fare clic su **Avanti**.

   ![Accesso al dispositivo nel browser][intellij-azure-link-ms-account]

6. Nella finestra di dialogo **Seleziona sottoscrizioni** selezionare le sottoscrizioni che si desidera utilizzare e quindi fare clic su **Seleziona**.

   ![Finestra di dialogo Seleziona sottoscrizioni][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Creare il progetto locale

In questa sezione si userà Azure Toolkit for IntelliJ per creare un progetto locale di Funzioni di Azure. Più avanti in questo articolo verrà pubblicato il codice della funzione in Azure. 

1. Aprire la finestra di dialogo iniziale di IntelliJ, selezionare *Create New Project* (Crea nuovo progetto) per aprire la creazione guidata di un nuovo progetto, quindi selezionare *Funzioni di Azure*.

    ![Crea progetto di funzione](media/functions-create-first-java-intellij/create-functions-project.png)

1. Selezionare *Http Trigger* (Trigger HTTP), quindi fare clic su *Avanti* e seguire la procedura guidata per esaminare tutte le configurazioni nelle pagine seguenti. Confermare il percorso del progetto, quindi fare clic su *Fine*. Intellj IDEA apre quindi il nuovo progetto.

    ![Fine del progetto di creazione funzione](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>Eseguire il progetto in locale

1. Passare a `src/main/java/org/example/functions/HttpTriggerFunction.java` per visualizzare il codice generato. Accanto alla riga *17* si noterà un pulsante verde *Run* (Esegui). Fare clic e quindi selezionare *Run 'azure-function-exam'* . Si vedrà l'app per le funzioni in esecuzione in locale con alcuni log.

    ![Progetto di esecuzione locale](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Output del progetto di esecuzione locale](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. È possibile provare la funzione accedendo all'endpoint stampato dal browser, ad esempio `http://localhost:7071/api/HttpTrigger-Java?name=Azure`.

    ![Risultato del test della funzione di esecuzione locale](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Il log viene anche stampato nella propria IDEA, ora arrestare l'app per le funzioni facendo clic sul pulsante *Arresta* .

    ![Log di test della funzione di esecuzione locale](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>Eseguire il debug del progetto in locale

1. Per eseguire il debug del codice della funzione nel progetto in locale, selezionare il pulsante *debug* nella barra degli strumenti. Se la barra degli strumenti non viene visualizzata, abilitarla scegliendo **Visualizza**  >  **aspetto**  >  **barra degli strumenti**.

    ![Pulsante app per le funzioni di debug locale](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Fare clic su riga *20* del file `src/main/java/org/example/functions/HttpTriggerFunction.java` per aggiungere un punto di interruzione, accedere nuovamente all'endpoint `http://localhost:7071/api/HttpTrigger-Java?name=Azure`. Si noterà che il punto di interruzione viene raggiunto. È possibile provare altre funzionalità di debug, ad esempio *step*, *watch*, *evaluation*. Arrestare la sessione di debug facendo clic sul pulsante stop.

    ![Interruzioni app per le funzioni di debug locale](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Distribuire il progetto in Azure

1. Fare clic con il pulsante destro del mouse sul progetto nella finestra di esplorazione progetti IntelliJ, quindi scegliere *Azure -> Deploy to Azure Functions* (Azure -> Distribuisci in Funzioni di Azure)

    ![Distribuire il progetto in Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Se non si dispone ancora di app per le funzioni, fare clic sulla *+* riga della *funzione* . Digitare il nome dell'app per le funzioni e scegliere la piattaforma appropriata. qui è possibile accettare semplicemente il valore predefinito. Fare clic su *OK* e la nuova app per le funzioni appena creata verrà selezionata automaticamente. Fare clic su *Run* (Esegui) per distribuire le funzioni.

    ![Crea app per le funzioni in Azure](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

    ![Distribuisci app per le funzioni in log di Azure](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>Gestisci le app per le funzioni da IDEA

1. È possibile gestire le app per le funzioni con *Azure Explorer* nella propria idea, fare clic su *app per le funzioni* per visualizzare tutte le app per le funzioni.

    ![Visualizzare le app per le funzioni in Esplora risorse](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Fare clic per selezionare una delle app per le funzioni e fare clic con il pulsante destro del mouse e scegliere *Mostra proprietà* per aprire la pagina dei dettagli. 

    ![Mostra proprietà app per le funzioni](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Fare clic con il pulsante destro del mouse sull'app per le funzioni *HttpTrigger-Java* e selezionare *funzione trigger*. si noterà che il browser viene aperto con l'URL del trigger.

    ![Screenshot mostra un browser con U R L.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Aggiungere altre funzioni al progetto

1. Fare clic con il pulsante destro del mouse sul pacchetto *org.example.functions* e Scegliere *New -> Azure Function Class* (Nuovo -> Classe di funzioni di Azure). 

    ![Aggiungere funzioni al progetto](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Immettere il nome della classe *HttpTest* e selezionare *HttpTrigger* nella creazione guidata classe di funzione, fare clic su *OK* per avviare la creazione. In questo modo è possibile creare le nuove funzioni come si preferisce.

    ![Screenshot mostra la finestra di dialogo Crea classe di funzioni.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Output dell'aggiunta di funzioni al progetto](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Pulizia delle funzioni

1. Eliminazione di funzioni in Azure Explorer
      
      ![Screenshot mostra l'opzione Elimina selezionata da un menu di scelta rapida.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Passaggi successivi

È stato creato un progetto Java con una funzione attivata tramite HTTP, viene eseguito nel computer locale e distribuito in Azure. A questo punto, estendere la funzione effettuando l'operazione seguente.

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
