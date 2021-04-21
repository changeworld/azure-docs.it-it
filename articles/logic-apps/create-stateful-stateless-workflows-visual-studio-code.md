---
title: Creare flussi di lavoro di anteprima di App per la logica in Visual Studio Code
description: Compilare ed eseguire flussi di lavoro per scenari di automazione e integrazione in Visual Studio Code con l'estensione App per la logica di Azure (anteprima).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 4010f7e2d0d20216107a45109056478694c940ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772506"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Creare flussi di lavoro con e senza stato in Visual Studio Code con l'estensione App per la logica di Azure (anteprima)

> [!IMPORTANT]
> Questa funzionalità è in anteprima pubblica, viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Con [App per la logica di Azure Preview](logic-apps-overview-preview.md)è possibile creare soluzioni di automazione e integrazione in app, dati, servizi cloud e sistemi creando ed eseguendo app per la logica che includono flussi di lavoro con e senza stato in Visual Studio Code usando l'estensione App per la logica di Azure (anteprima). [   ](logic-apps-overview-preview.md#stateful-stateless) Usando questo nuovo tipo di app per la logica, è possibile creare più flussi di lavoro basati sul runtime di App per la logica di Azure Preview riprogettato, che offre portabilità, prestazioni migliori e flessibilità per la distribuzione e l'esecuzione in vari ambienti di hosting, non solo in Azure, ma anche nei contenitori Docker. Per altre informazioni sul nuovo tipo di app per la logica, vedere [Panoramica di App per la logica di Azure Anteprima.](logic-apps-overview-preview.md)

![Screenshot che mostra il Visual Studio Code, il progetto di app per la logica e il flusso di lavoro.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

In Visual Studio Code è possibile iniziare creando un progetto  in cui è possibile compilare ed eseguire in locale i flussi di lavoro dell'app per la logica nell'ambiente di sviluppo usando l'estensione App per la logica di Azure (anteprima). Anche se è anche possibile iniziare creando una nuova risorsa app per la logica [ **(anteprima)** nel portale di Azure ,](create-stateful-stateless-workflows-azure-portal.md)entrambi gli approcci offrono la possibilità di distribuire ed eseguire l'app per la logica negli stessi tipi di ambienti di hosting.

Nel frattempo, è comunque possibile creare il tipo di app per la logica originale. Anche se le esperienze di sviluppo in Visual Studio Code sono diverse tra i tipi di app per la logica originale e nuovo, la sottoscrizione di Azure può includere entrambi i tipi. È possibile visualizzare e accedere a tutte le app per la logica distribuite nella sottoscrizione di Azure, ma le app sono organizzate in categorie e sezioni specifiche.

Questo articolo illustra come creare l'app per la logica e un flusso di lavoro in Visual Studio Code usando l'estensione App per la logica di Azure (anteprima) ed eseguendo queste attività di alto livello:

* Creare un progetto per l'app per la logica e il flusso di lavoro.

* Aggiungere un trigger e un'azione.

* Eseguire, testare, eseguire il debug ed esaminare la cronologia di esecuzione in locale.

* Trovare i dettagli del nome di dominio per l'accesso al firewall.

* Distribuire in Azure, che include facoltativamente l'abilitazione Application Insights.

* Gestire l'app per la logica distribuita Visual Studio Code e il portale di Azure.

* Abilitare la cronologia di esecuzione per i flussi di lavoro senza stato.

* Abilitare o aprire il Application Insights dopo la distribuzione.

* Distribuire in un contenitore Docker che è possibile eseguire ovunque.

> [!NOTE]
> Per informazioni sui problemi noti correnti, vedere la pagina Problemi noti dell'anteprima pubblica di App per la [logica in GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

## <a name="prerequisites"></a>Prerequisiti

### <a name="access-and-connectivity"></a>Accesso e connettività

* Accesso a Internet per poter scaricare i requisiti, connettersi da Visual Studio Code all'account Azure e pubblicare da Visual Studio Code ad Azure, un contenitore Docker o un altro ambiente.

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Per creare la stessa app per la logica di esempio in questo articolo, è necessario un account di posta elettronica di Office 365 Outlook che usa un account aziendale o dell'istituto di istruzione Microsoft per accedere.

  Se si sceglie di usare un connettore di posta elettronica diverso supportato da App per la logica di Azure, ad esempio Outlook.com o [Gmail,](../connectors/connectors-google-data-security-privacy-policy.md)è comunque possibile seguire l'esempio e i passaggi generali sono gli stessi, ma [l'interfaccia](/connectors/)utente e le opzioni potrebbero essere diverse per alcuni aspetti. Ad esempio, se si usa il connettore Outlook.com, usare il proprio account Microsoft per accedere.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Requisiti di archiviazione

#### <a name="windows"></a>Windows

Per compilare ed eseguire il progetto di app per la logica in locale Visual Studio Code quando si usa Windows, seguire questa procedura per configurare l'emulatore Archiviazione di Azure:

1. Scaricare e installare [Archiviazione di Azure Emulator 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Se non è già disponibile, è necessario avere un'installazione del database SQL locale, ad esempio la versione [gratuita SQL Server 2019 Express Edition,](https://go.microsoft.com/fwlink/p/?linkid=866658)in modo che l'emulatore possa essere eseguito.

   Per altre informazioni, vedere [Usare l'emulatore Archiviazione di Azure per lo sviluppo e il test.](../storage/common/storage-use-emulator.md)

1. Prima di poter eseguire il progetto, assicurarsi di avviare l'emulatore.

   ![Screenshot che mostra l'emulatore Archiviazione di Azure in esecuzione.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS e Linux

Per compilare ed eseguire in locale il progetto di app per la logica in Visual Studio Code quando si usa macOS o Linux, seguire questa procedura per creare e configurare un account Archiviazione di Azure locale.

> [!NOTE]
> Attualmente, la finestra di progettazione in Visual Studio Code non funziona nel sistema operativo Linux, ma è comunque possibile eseguire le app per la logica di compilazione, esecuzione e distribuzione che usano il runtime di anteprima di App per la logica in macchine virtuali basate su Linux. Per il momento, è possibile compilare le app per la logica in Visual Studio Code in Windows o macOS e quindi distribuirvi in una macchina virtuale basata su Linux.

1. Accedere al portale di Azure [e](https://portal.azure.com)creare un account [Archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal), che è un [prerequisito per Funzioni di Azure](../azure-functions/storage-considerations.md).

1. Nel menu dell'account di archiviazione, in **Impostazioni** selezionare **Chiavi di accesso.**

1. Nel riquadro **Chiavi di accesso** trovare e copiare la stringa di connessione dell'account di archiviazione, simile all'esempio seguente:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Screenshot che mostra l'portale di Azure con le chiavi di accesso dell'account di archiviazione e la stringa di connessione copiate.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Per altre informazioni, vedere Gestire [le chiavi dell'account di archiviazione.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Salvare la stringa di connessione in un luogo sicuro. Dopo aver creato il progetto di app per la logica in Visual Studio Code, è necessario aggiungere la stringa al **local.settings.js** nel file nella cartella del livello radice del progetto.

   > [!IMPORTANT]
   > Se si prevede di eseguire la distribuzione in un contenitore Docker, è necessario usare anche questa stringa di connessione con il file Docker da usare per la distribuzione. Per gli scenari di produzione, assicurarsi di proteggere e proteggere tali segreti e informazioni riservate, ad esempio usando un insieme di credenziali delle chiavi.
  
### <a name="tools"></a>Strumenti

* [Visual Studio Code 1.30.1 (gennaio 2019)](https://code.visualstudio.com/)o versione successiva, è gratuito. Scaricare e installare anche questi strumenti per Visual Studio Code, se non sono già disponibili:

  * [Estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), che offre un'unica esperienza comune di filtro per l'accesso e la sottoscrizione di Azure per tutte le altre estensioni di Azure Visual Studio Code.

  * [C# per Visual Studio Code,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)che consente alla funzionalità F5 di eseguire l'app per la logica.

  * [Azure Functions Core Tools 3.0.3245](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) o versione successiva usando la versione di Microsoft Installer (MSI), ovvero `func-cli-3.0.3245-x*.msi` .

    Questi strumenti includono una versione dello stesso runtime che alimenta il runtime Funzioni di Azure, che l'estensione di anteprima usa in Visual Studio Code.

    > [!IMPORTANT]
    > Se si dispone di un'installazione precedente a queste versioni, disinstallare prima tale versione o assicurarsi che la variabile di ambiente PATH punti alla versione scaricata e installata.

  * [App per la logica di Azure (anteprima) per Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Questa estensione offre la possibilità di creare app per la logica in cui è possibile creare flussi di lavoro con stato e senza stato eseguiti in locale in Visual Studio Code e quindi distribuire tali app per la logica direttamente in Azure o nei contenitori Docker.

    Attualmente, è possibile avere sia l'estensione App per la logica di Azure originale che l'estensione anteprima pubblica installate in Visual Studio Code. Anche se le esperienze di sviluppo differiscono per alcuni aspetti tra le estensioni, la sottoscrizione di Azure può includere entrambi i tipi di app per la logica creati con le estensioni. Visual Studio Code mostra tutte le app per la logica distribuite nella sottoscrizione di Azure, ma le organizza in sezioni diverse in base ai nomi delle **estensioni,** alle app per la logica e App per la logica di Azure **(anteprima).**

    > [!IMPORTANT]
    > Se sono stati creati progetti di app per la logica con l'estensione di anteprima privata precedente, questi progetti non funzionano con l'estensione Anteprima pubblica. È tuttavia possibile eseguire la migrazione di questi progetti dopo aver disinstallato l'estensione di anteprima privata, eliminato i file associati e installato l'estensione di anteprima pubblica. Creare quindi un nuovo progetto in Visual Studio Code e copiare il file **workflow.definition** dell'app per la logica creata in precedenza nel nuovo progetto. Per altre informazioni, vedere Eseguire la [migrazione dall'estensione di anteprima privata](#migrate-private-preview).
    > 
    > Se sono stati creati progetti di app per la logica con l'estensione di anteprima pubblica precedente, è possibile continuare a usare tali progetti senza passaggi di migrazione.

    **Per installare **l'App per la logica di Azure (anteprima),** seguire questa procedura:**

    1. Nella Visual Studio Code a sinistra selezionare **Estensioni.**

    1. Nella casella di ricerca delle estensioni immettere `azure logic apps preview` . Nell'elenco dei risultati selezionare **App per la logica di Azure (anteprima)** **>** **Installa**.

       Al termine dell'installazione, l'estensione Anteprima viene visualizzata **nell'elenco Estensioni:** Installati .

       ![Screenshot che mostra Visual Studio Code'elenco delle estensioni installate con l'estensione "App per la logica di Azure (anteprima)" sottolineata.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Se l'estensione non viene visualizzata nell'elenco installato, provare a riavviare Visual Studio Code.

* Per usare [l'azione Operazioni](../logic-apps/logic-apps-add-run-inline-code.md) codice inline che esegue JavaScript, installare [Node.js versioni 10.x.x, 11.x.x o 12.x.x.](https://nodejs.org/en/download/releases/)

  > [!TIP] 
  > Per Windows, scaricare la versione MSI. Se invece si usa la versione ZIP, è necessario rendere Node.js manualmente usando una variabile di ambiente PATH per il sistema operativo.

* Per eseguire in locale trigger e azioni basati su [webhook,](../connectors/connectors-native-webhook.md)ad esempio il trigger webhook HTTP predefinito, in Visual Studio Code, è necessario configurare l'inoltro per l'URL [di callback](#webhook-setup).

* Per testare l'app per la logica di esempio creata in questo articolo, è necessario uno strumento in grado di inviare chiamate al trigger di richiesta, che è il primo passaggio dell'app per la logica di esempio. Se non si ha uno strumento di questo tipo, è possibile scaricare, installare e usare [Postman](https://www.postman.com/downloads/).

* Se si crea l'app per la logica e si distribuisce con impostazioni [che supportano l'uso](../azure-monitor/app/app-insights-overview.md)di Application Insights , è possibile abilitare facoltativamente la registrazione diagnostica e la traccia per l'app per la logica. È possibile farlo quando si distribuisce l'app per la logica da Visual Studio Code o dopo la distribuzione. È necessario avere un'istanza Application Insights, ma è possibile creare questa risorsa [in](../azure-monitor/app/create-workspace-resource.md)anticipo, quando si distribuisce l'app per la logica o dopo la distribuzione.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Eseguire la migrazione dall'estensione di anteprima privata

Tutti i progetti di app per la logica creati con **l'estensione App per la logica di Azure (anteprima privata)** non funzionano con l'estensione Anteprima pubblica. È tuttavia possibile eseguire la migrazione di questi progetti a nuovi progetti seguendo questa procedura:

1. Disinstallare l'estensione di anteprima privata.

1. Eliminare qualsiasi bundle di estensioni associato e le cartelle dei pacchetti NuGet nei percorsi seguenti:

   * La **cartella Microsoft.Azure.Functions.ExtensionBundle.Workflows,** che contiene i bundle di estensioni precedenti e si trova in uno dei percorsi seguenti:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * Cartella **microsoft.azure.workflows.webjobs.extension,** che è la cache [NuGet](/nuget/what-is-nuget) per l'estensione di anteprima privata e si trova in questo percorso:

     `C:\Users\{userName}\.nuget\packages`

1. Installare **l'App per la logica di Azure (anteprima).**

1. Creare un nuovo progetto in Visual Studio Code.

1. Copiare il file **workflow.definition** dell'app per la logica creata in precedenza nel nuovo progetto.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurare Visual Studio Code

1. Per assicurarsi che tutte le estensioni siano installate correttamente, ricaricare o riavviare Visual Studio Code.

1. Verificare che Visual Studio Code trova e installi automaticamente gli aggiornamenti dell'estensione in modo che l'estensione di anteprima osezioni gli aggiornamenti più recenti. In caso contrario, è necessario disinstallare manualmente la versione obsoleta e installare la versione più recente.

   1. Nel menu **File** passare a **Impostazioni** **>** **preferenze**.

   1. Nella scheda **Utente** passare a **Estensioni** **>** **funzionalità**.

   1. Verificare che **siano selezionate le opzioni Aggiornamenti controllo** automatico e **Aggiornamento** automatico.

Inoltre, per impostazione predefinita, le impostazioni seguenti sono abilitate e impostate per l'estensione di anteprima app per la logica:

* **App per la logica di Azure V2: Project Runtime,** che è impostato sulla versione **~3**

  > [!NOTE]
  > Questa versione è necessaria per usare le [azioni operazioni codice inline](../logic-apps/logic-apps-add-run-inline-code.md).

* **App per la logica di Azure V2: Experimental View Manager,** che abilita la finestra di progettazione più recente Visual Studio Code. Se si verificano problemi nella finestra di progettazione, ad esempio il trascinamento e il rilascio di elementi, disattivare questa impostazione.

Per trovare e confermare queste impostazioni, seguire questa procedura:

1. Nel menu **File** passare a Preferences Settings **(Impostazioni** **>** **preferenze).**

1. Nella scheda **Utente** passare a **>** **Estensioni App per la logica di Azure** **>** **(anteprima).**

   Ad esempio, è possibile trovare **l'impostazione App per la logica di Azure V2: Project Runtime** qui o usare la casella di ricerca per trovare altre impostazioni:

   ![Screenshot che mostra le Visual Studio Code per l'estensione "App per la logica di Azure (anteprima)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

1. Nella barra Visual Studio Code attività selezionare l'icona Di Azure.

   ![Screenshot che mostra la Visual Studio Code attività e l'icona di Azure selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Nel riquadro Azure, in **Azure: App per la logica (anteprima)** selezionare **Accedi ad Azure.** Quando viene visualizzata Visual Studio Code di autenticazione, accedere con il proprio account Azure.

   ![Screenshot che mostra il riquadro di Azure e il collegamento selezionato per l'accesso ad Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Dopo l'accesso, nel riquadro di Azure vengono visualizzate le sottoscrizioni nell'account Azure. Se è disponibile anche l'estensione rilasciata pubblicamente, è possibile  trovare tutte le app per la logica create con tale estensione nella sezione App per la logica, non nella sezione App per la logica **(anteprima).**
   
   Se le sottoscrizioni previste non vengono visualizzate o si vuole che nel riquadro vengano visualizzate solo sottoscrizioni specifiche, seguire questa procedura:

   1. Nell'elenco delle sottoscrizioni spostare il puntatore accanto alla prima sottoscrizione fino a quando non viene visualizzato il pulsante **Seleziona** sottoscrizioni (icona del filtro). Selezionare l'icona del filtro.

      ![Screenshot che mostra il riquadro di Azure e l'icona del filtro selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      In caso contrario, nella Visual Studio Code di stato selezionare l'account Azure. 

   1. Quando viene visualizzato un altro elenco di sottoscrizioni, selezionare le sottoscrizioni desiderate e quindi assicurarsi di selezionare **OK.**

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Creare un progetto locale

Prima di poter creare l'app per la logica, creare un progetto locale in modo da poter gestire, eseguire e distribuire l'app per la logica da Visual Studio Code. Il progetto sottostante è simile a un progetto Funzioni di Azure, noto anche come progetto di app per le funzioni. Tuttavia, questi tipi di progetto sono separati l'uno dall'altro, quindi le app per la logica e le app per le funzioni non possono esistere nello stesso progetto.

1. Nel computer creare una cartella *locale* vuota da usare per il progetto che verrà successivamente creato in Visual Studio Code.

1. In Visual Studio Code chiudere tutte le cartelle aperte.

1. Nel riquadro Azure accanto ad Azure: App per la logica **(anteprima)** selezionare **Crea** nuovo progetto (icona che mostra una cartella e un fulmine).

   ![Screenshot che mostra la barra degli strumenti del riquadro di Azure con l'opzione "Crea nuovo progetto" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Se Windows Defender Firewall richiede di concedere l'accesso alla rete per , ovvero Visual Studio Code, e per , che è il Azure Functions Core Tools, selezionare Reti private, ad esempio rete domestica o aziendale Consenti `Code.exe` `func.exe`  **>** **l'accesso**.

1. Passare al percorso in cui è stata creata la cartella del progetto, selezionare la cartella e continuare.

   ![Screenshot che mostra la finestra di dialogo "Seleziona cartella" con una cartella di progetto appena creata e il pulsante "Seleziona" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Nell'elenco dei modelli visualizzato selezionare Flusso di lavoro **con stato** o Flusso di lavoro **senza stato.** In questo esempio viene selezionato **il flusso di lavoro con stato**.

   ![Screenshot che mostra l'elenco dei modelli di flusso di lavoro con l'opzione "Flusso di lavoro con stato" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Specificare un nome per il flusso di lavoro e premere INVIO. In questo esempio `Fabrikam-Stateful-Workflow` viene utilizzato come nome.

   ![Screenshot che mostra la casella "Crea nuovo flusso di lavoro con stato (3/4)" e "Fabrikam-Stateful-Workflow" come nome del flusso di lavoro.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code completa la creazione del progetto e apre ilworkflow.js **nel** file per il flusso di lavoro nell'editor di codice.

   > [!NOTE]
   > Se viene richiesto di selezionare la modalità di  apertura del progetto, selezionare Apri nella finestra corrente se si vuole aprire il progetto nella finestra Visual Studio Code corrente. Per aprire una nuova istanza per Visual Studio Code, **selezionare Apri in una nuova finestra.**

1. Dalla barra Visual Studio, aprire il riquadro Esplora risorse, se non è già aperto.

   Il riquadro Esplora risorse mostra il progetto, che ora include i file di progetto generati automaticamente. Ad esempio, il progetto ha una cartella che mostra il nome del flusso di lavoro. All'interno di questa **cartella,workflow.jsfile** contiene la definizione JSON sottostante del flusso di lavoro.

   ![Screenshot che mostra il riquadro Esplora risorse con la cartella del progetto, la cartella del flusso di lavoro e il file "workflow.jssu".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Se si usa macOS o Linux, configurare l'accesso all'account di archiviazione seguendo questa procedura, che sono necessarie per l'esecuzione locale del progetto:

   1. Nella cartella radice del progetto aprire illocal.settings.js **file.**

      ![Screenshot che mostra il riquadro Esplora risorse e il file "local.settings.js" nel progetto.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Sostituire il valore della proprietà con la stringa di connessione dell'account di archiviazione salvata in `AzureWebJobsStorage` precedenza, ad esempio:

      Prima di:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Dopo:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

      > [!IMPORTANT]
      > Per gli scenari di produzione, assicurarsi di proteggere e proteggere tali segreti e informazioni riservate, ad esempio usando un insieme di credenziali delle chiavi.

   1. Al termine, assicurarsi di salvare le modifiche.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Abilitare la creazione di connettori predefiniti

È possibile creare connettori predefiniti per qualsiasi servizio necessario usando il framework di [estendibilità della versione di anteprima.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) Analogamente ai connettori predefiniti, ad esempio bus di servizio di Azure e SQL Server, questi connettori offrono velocità effettiva superiore, bassa latenza, connettività locale ed eseguiti in modo nativo nello stesso processo del runtime di anteprima.

La funzionalità di creazione è attualmente disponibile solo in Visual Studio Code, ma non è abilitata per impostazione predefinita. Per creare questi connettori, è prima necessario convertire il progetto da un'estensione basata su bundle (Node.js) a nuGet basata su pacchetti (.NET).

> [!IMPORTANT]
> Questa azione è un'operazione unidirezione che non è possibile annullare.

1. Nel riquadro Esplora risorse, nella radice del progetto, spostare il puntatore del mouse su qualsiasi area vuota sotto tutti gli altri file e cartelle, aprire il menu di scelta rapida e selezionare Converti in progetto app per la logica basato su **Nuget.**

   ![Screenshot che mostra il riquadro Esplora risorse con il menu di scelta rapida del progetto aperto da un'area vuota nella finestra del progetto.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Quando viene visualizzata la richiesta, confermare la conversione del progetto.

1. Per continuare, esaminare e seguire i passaggi descritti nell'articolo Esecuzione di App per la logica di Azure via Internet - Estendibilità del connettore [incorporato](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Aprire il file di definizione del flusso di lavoro nella finestra di progettazione

1. Controllare le versioni installate nel computer eseguendo questo comando:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Se è disponibile .NET Core SDK 5.x, questa versione potrebbe impedire l'apertura della definizione del flusso di lavoro sottostante dell'app per la logica nella finestra di progettazione. Anziché disinstallare questa versione, nella cartella radice del progetto creare un **global.js** nel file che fa riferimento alla versione 3.x del runtime di .NET Core successiva alla 3.1.201, ad esempio:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Assicurarsi di aggiungere in modo esplicito **ilglobal.jsfile** nella cartella radice del progetto dall'interno Visual Studio Code. In caso contrario, la finestra di progettazione non verrà aperta.

1. Espandere la cartella del progetto per il flusso di lavoro. Aprire il **workflow.jsnel** menu di scelta rapida del file e selezionare Apri nella finestra **di progettazione**.

   ![Screenshot che mostra il riquadro Esplora risorse e la finestra di scelta rapida workflow.jsfile con l'opzione "Apri nella finestra di progettazione" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Nell'elenco Abilita connettori **in Azure** selezionare Usa connettori di **Azure**, che si applica a tutti i connettori gestiti disponibili e distribuiti in Azure, non solo ai connettori per i servizi di Azure.

   ![Screenshot che mostra il riquadro Esplora risorse con l'elenco "Abilita connettori in Azure" aperto e l'opzione "Usa connettori da Azure" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > I flussi di lavoro senza stato supportano *attualmente* solo [le azioni](../connectors/managed.md)per i connettori gestiti, distribuiti in Azure e non per i trigger. Anche se è possibile abilitare i connettori in Azure per il flusso di lavoro senza stato, la finestra di progettazione non mostra alcun trigger del connettore gestito da selezionare.

1. **Nell'elenco Seleziona sottoscrizione** selezionare la sottoscrizione di Azure da usare per il progetto di app per la logica.

   ![Screenshot che mostra il riquadro Explorer con la casella "Seleziona sottoscrizione" e la sottoscrizione selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Nell'elenco dei gruppi di risorse selezionare **Crea nuovo gruppo di risorse.**

   ![Screenshot che mostra il riquadro Esplora risorse con l'elenco dei gruppi di risorse e l'opzione "Crea nuovo gruppo di risorse" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Specificare un nome per il gruppo di risorse e premere INVIO. In questo esempio viene utilizzato `Fabrikam-Workflows-RG`.

   ![Screenshot che mostra il riquadro Esplora risorse e la casella del nome del gruppo di risorse.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Nell'elenco delle località trovare e selezionare l'area di Azure da usare durante la creazione del gruppo di risorse e delle risorse. Questo esempio usa **Stati Uniti centro-occidentali.**

   ![Screenshot che mostra il riquadro Explorer con l'elenco delle località e l'opzione "Stati Uniti centro-occidentali" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Dopo aver eseguito questo passaggio, Visual Studio Code la finestra di progettazione del flusso di lavoro.

   > [!NOTE]
   > Quando Visual Studio Code l'API della fase di progettazione del flusso di lavoro, è possibile che venga visualizzato un messaggio che indica che l'avvio potrebbe richiedere alcuni secondi. È possibile ignorare questo messaggio o selezionare **OK.**
   >
   > Se la finestra di progettazione non si apre, esaminare la sezione relativa alla risoluzione dei problemi. [Progettazione non riesce ad aprire](#designer-fails-to-open).

   Quando viene visualizzata la finestra di progettazione, nella finestra di progettazione viene visualizzato il **prompt** Scegliere un'operazione ed è selezionato per impostazione predefinita, che mostra il riquadro **Aggiungi un'azione.**

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Aggiungere quindi [un trigger e le azioni al](#add-trigger-actions) flusso di lavoro.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Aggiungere un trigger e azioni

Dopo aver aperto la finestra di progettazione, il prompt **Scegliere un'operazione** viene visualizzato nella finestra di progettazione ed è selezionato per impostazione predefinita. È ora possibile iniziare a creare il flusso di lavoro aggiungendo un trigger e azioni.

Il flusso di lavoro in questo esempio usa questo trigger e le azioni seguenti:

* Il [trigger](../connectors/connectors-native-reqres.md)di richiesta predefinito , quando viene ricevuta una richiesta **HTTP,** che riceve le chiamate o le richieste in ingresso e crea un endpoint che può essere chiamato da altri servizi o app per la logica.

* [L'azione Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), Invia un messaggio di posta **elettronica**.

* Azione response [predefinita,](../connectors/connectors-native-reqres.md)che consente di inviare una risposta e restituire i dati al chiamante.

### <a name="add-the-request-trigger"></a>Aggiungere il trigger Request

1. Accanto alla finestra di progettazione, nel riquadro  Aggiungi **trigger,** nella casella  di ricerca Scegliere un'operazione, assicurarsi che l'opzione Predefinita sia selezionata in modo che sia possibile selezionare un trigger che viene eseguito in modo nativo.

1. Nella casella di ricerca Scegliere **un'operazione** immettere e selezionare il trigger di richiesta incorporato denominato Quando viene ricevuta `when a http request` una richiesta **HTTP.**

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e il riquadro **Aggiungi un trigger** con il trigger "Quando viene ricevuta una richiesta HTTP" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Quando il trigger viene visualizzato nella finestra di progettazione, viene aperto il riquadro dei dettagli del trigger per visualizzare le proprietà, le impostazioni e altre azioni del trigger.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro con il trigger "Quando viene ricevuta una richiesta HTTP" selezionato e il riquadro dei dettagli del trigger aperto.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se il riquadro dei dettagli non viene visualizzato, assicurarsi che il trigger sia selezionato nella finestra di progettazione.

1. Se è necessario eliminare un elemento dalla finestra di progettazione, seguire questa procedura per eliminare [elementi dalla finestra di progettazione.](#delete-from-designer)

### <a name="add-the-office-365-outlook-action"></a>Aggiungere l'azione Office 365 Outlook

1. Nella finestra di progettazione, sotto il trigger aggiunto, selezionare **Nuovo passaggio.**

   Nella **finestra di progettazione viene** visualizzato  il prompt Scegliere un'operazione e il riquadro Aggiungi un'azione viene riaperto in modo da poter selezionare l'azione successiva.

1. Nella casella **di** ricerca Scegliere  un'operazione del riquadro Aggiungi un'azione selezionare **Azure** per trovare e selezionare un'azione per un connettore gestito distribuito in Azure.

   Questo esempio seleziona e usa l'azione Office 365 **Outlook, Invia un messaggio di posta elettronica (V2).**

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e il riquadro **Aggiungi un'azione** con l'azione "Invia un messaggio di posta elettronica" di Office 365 Outlook selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Nel riquadro dei dettagli dell'azione selezionare **Accedi per** poter creare una connessione all'account di posta elettronica.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e il riquadro **Invia un messaggio di posta elettronica (V2)** con l'opzione "Accedi" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Quando Visual Studio Code viene richiesto il consenso per accedere all'account di posta elettronica, selezionare **Apri**.

   ![Screenshot che mostra la richiesta Visual Studio Code consentire l'accesso.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Per evitare richieste future, selezionare **Configura domini attendibili** in modo che sia possibile aggiungere la pagina di autenticazione come dominio trusted.

1. Seguire le istruzioni successive per accedere, consentire l'accesso e consentire il ritorno a Visual Studio Code.

   > [!NOTE]
   > Se è passato troppo tempo prima di completare le richieste, si verifica il timeout del processo di autenticazione e si verifica un errore. In questo caso, tornare alla finestra di progettazione e riprovare ad accedere per creare la connessione.

1. Quando l'estensione App per la logica di Azure (anteprima) richiede il consenso per accedere all'account di posta elettronica, selezionare **Apri.** Seguire la richiesta successiva per consentire l'accesso.

   ![Screenshot che mostra la richiesta di anteprima dell'estensione per consentire l'accesso.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Per evitare richieste future, selezionare **Non chiedere più questa estensione.**

   Dopo Visual Studio Code la connessione, alcuni connettori visualizzano il messaggio `The connection will be valid for {n} days only` . Questo limite di tempo si applica solo alla durata durante la creazione dell'app per la logica in Visual Studio Code. Dopo la distribuzione, questo limite non si applica più perché l'app per la logica può eseguire l'autenticazione in fase di esecuzione usando l'identità gestita assegnata dal sistema [abilitata automaticamente.](../logic-apps/create-managed-service-identity.md) Questa identità gestita è diversa dalle credenziali di autenticazione o dalla stringa di connessione usate quando si crea una connessione. Se si disabilita questa identità gestita assegnata dal sistema, le connessioni non funzioneranno in fase di esecuzione.

1. Nella finestra di progettazione, se **l'azione** Invia un messaggio di posta elettronica non viene visualizzata, selezionare l'azione.

1. Nella scheda Parametri del riquadro  dei dettagli dell'azione specificare le informazioni necessarie per l'azione, ad esempio:

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro con i dettagli per l'azione "Invia un messaggio di posta elettronica" di Office 365 Outlook.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Destinatario di posta elettronica, che può essere l'indirizzo di posta elettronica dell'utente a scopo di test. In questo esempio viene utilizzato il messaggio di posta elettronica fittizio, `sophiaowen@fabrikam.com` . |
   | **Oggetto** | Sì | `An email from your example workflow` | L'oggetto del messaggio di posta elettronica |
   | **Corpo** | Sì | `Hello from your example workflow!` | Contenuto del corpo del messaggio di posta elettronica |
   ||||

   > [!NOTE]
   > Se si desidera apportare modifiche nel riquadro dei dettagli  nella scheda Impostazioni **,** Risultato  statico o Esegui dopo , assicurarsi di selezionare Fine per eseguire il commit delle modifiche prima di spostare le schede o spostare lo stato attivo nella finestra di progettazione. In caso Visual Studio Code le modifiche non verranno conservate. Per altre informazioni, vedere la pagina Problemi noti dell'anteprima pubblica di App per la [logica in GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

1. Nella finestra di progettazione selezionare **Salva**.

> [!IMPORTANT]
> Per eseguire in locale un flusso di lavoro che usa un trigger o azioni basate su webhook, ad esempio il trigger o l'azione webhook HTTP predefinita, è necessario abilitare questa funzionalità configurando [l'inoltro per l'URL](#webhook-setup)di callback del [webhook.](../connectors/connectors-native-webhook.md)

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Abilitare webhook in esecuzione in locale

Quando si usa un trigger o un'azione basata su webhook, ad esempio **webhook HTTP,** con un'app per la logica in esecuzione in Azure, il runtime di App per la logica sottoscrive l'endpoint del servizio generando e registrando un URL di callback con tale endpoint. Il trigger o l'azione attende quindi che l'endpoint del servizio chiami l'URL. Tuttavia, quando si lavora in Visual Studio Code, l'URL di callback generato inizia con `http://localhost:7071/...` . Questo URL è per il server localhost, che è privato, quindi l'endpoint del servizio non può chiamare questo URL.

Per eseguire in locale trigger e azioni basati su webhook in Visual Studio Code, è necessario configurare un URL pubblico che espone il server localhost e inoltra in modo sicuro le chiamate dall'endpoint del servizio all'URL di callback del webhook. È possibile usare un servizio di inoltro e uno strumento come [**ngrok**](https://ngrok.com/), che apre un tunnel HTTP alla porta localhost oppure è possibile usare uno strumento personalizzato.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Configurare l'inoltro di chiamata con **ngrok**

1. [Se non si ha un account **ngrok,**](https://dashboard.ngrok.com/signup) iscriversi. In caso [contrario, accedere all'account](https://dashboard.ngrok.com/login).

1. Ottenere il token di autenticazione personale, necessario al client **ngrok** per connettersi e autenticare l'accesso all'account.

   1. Per trovare la pagina [del token di autenticazione,](https://dashboard.ngrok.com/auth/your-authtoken)nel menu del dashboard dell'account espandere **Autenticazione** e selezionare Token **di autenticazione.**

   1. Nella casella **Your Authtoken** copiare il token in una posizione sicura.

1. Dalla pagina di download [ **di ngrok**](https://ngrok.com/download) o dal [dashboard dell'account,](https://dashboard.ngrok.com/get-started/setup)scaricare la versione **di ngrok** desiderata ed estrarre il file zip. Per altre informazioni, vedere [Passaggio 1: Decomprimere per installare](https://ngrok.com/download).

1. Nel computer aprire lo strumento del prompt dei comandi. Passare al percorso in cui si trova il file **ngrok.exe** file.

1. Connettere il client **ngrok** all'account **ngrok** eseguendo il comando seguente. Per altre informazioni, vedere [Passaggio 2: Connettere l'account.](https://ngrok.com/download)

   `ngrok authtoken <your_auth_token>`

1. Aprire il tunnel HTTP alla porta localhost 7071 eseguendo il comando seguente. Per altre informazioni, vedere [Passaggio 3: Generarlo.](https://ngrok.com/download)

   `ngrok http 7071`

1. Nell'output trovare la riga seguente:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Copiare e salvare l'URL con questo formato: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Configurare l'URL di inoltro nelle impostazioni dell'app

1. Nella Visual Studio Code della finestra di progettazione aggiungere l'azione o il trigger **HTTP + Webhook.**

1. Quando viene visualizzata la richiesta per il percorso dell'endpoint host, immettere l'URL di inoltro (reindirizzamento) creato in precedenza.

   > [!NOTE]
   > Se si ignora la richiesta, viene visualizzato un avviso che indica che è necessario specificare l'URL di inoltro, quindi selezionare **Configura** e immettere l'URL. Al termine di questo passaggio, la richiesta non verrà visualizzata di nuovo per i trigger o le azioni del webhook successivi che è possibile aggiungere.
   >
   > Per visualizzare di nuovo la richiesta, a livello di radice del progetto, aprire illocal.settings.jsnel menu di scelta rapida del **file** e selezionare Configura endpoint di reindirizzamento **webhook**. Viene ora visualizzata la richiesta in modo da poter specificare l'URL di inoltro.

   Visual Studio Code l'URL di inoltro **allocal.settings.jsfile** nella cartella radice del progetto. `Values`Nell'oggetto viene visualizzata la proprietà denominata e viene `Workflows.WebhookRedirectHostUri` impostata sull'URL di inoltro, ad esempio:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

La prima volta che si avvia una sessione di debug locale o si esegue il flusso di lavoro senza eseguire il debug, il runtime di App per la logica registra il flusso di lavoro con l'endpoint del servizio e sottoscrive tale endpoint per notificare le operazioni del webhook. Alla successiva esecuzione del flusso di lavoro, il runtime non verrà registrato o rieseguito perché la registrazione della sottoscrizione esiste già nell'archiviazione locale.

Quando si arresta la sessione di debug per un'esecuzione del flusso di lavoro che usa azioni o trigger basati su webhook eseguiti localmente, le registrazioni delle sottoscrizioni esistenti non vengono eliminate. Per annullare la registrazione, è necessario rimuovere o eliminare manualmente le registrazioni della sottoscrizione.

> [!NOTE]
> Dopo l'avvio dell'esecuzione del flusso di lavoro, la finestra del terminale potrebbe visualizzare errori come nell'esempio seguente:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> In questo caso, aprire **local.settings.js** file nella cartella radice del progetto e assicurarsi che la proprietà sia impostata su `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Gestire i punti di interruzione per il debug

Prima di eseguire e testare il flusso di lavoro [](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) dell'app per la logica avviando una sessione di debug, è possibile impostare punti di interruzione **all'interno** delworkflow.jssu file per ogni flusso di lavoro. Non sono necessarie altre impostazioni. 

Al momento, i punti di interruzione sono supportati solo per le azioni, non per i trigger. Ogni definizione di azione ha le posizioni dei punti di interruzione seguenti:

* Impostare il punto di interruzione iniziale sulla riga che mostra il nome dell'azione. Quando questo punto di interruzione raggiunge durante la sessione di debug, è possibile esaminare gli input dell'azione prima che siano valutati.

* Impostare il punto di interruzione finale sulla riga che mostra la parentesi graffa di chiusura dell'azione (**}**). Quando questo punto di interruzione raggiunge durante la sessione di debug, è possibile esaminare i risultati dell'azione prima che l'azione venga completata.

Per aggiungere un punto di interruzione, seguire questa procedura:

1. Aprire il **workflow.jsfile** per il flusso di lavoro di cui si vuole eseguire il debug.

1. Nella riga in cui si vuole impostare il punto di interruzione, nella colonna a sinistra selezionare all'interno di tale colonna. Per rimuovere il punto di interruzione, selezionarlo.

   Quando si avvia la sessione di debug, la visualizzazione Esegui viene visualizzata sul lato sinistro della finestra del codice, mentre la barra degli strumenti Debug viene visualizzata nella parte superiore.

   > [!NOTE]
   > Se la visualizzazione Esegui non viene visualizzata automaticamente, premere CTRL+MAIUSC+D.

1. Per esaminare le informazioni disponibili quando viene raggiunto un punto di interruzione, nella visualizzazione Esegui esaminare il **riquadro** Variabili.

1. Per continuare l'esecuzione del flusso di lavoro, sulla barra degli strumenti Debug selezionare **Continua** (pulsante Riproduci).

È possibile aggiungere e rimuovere punti di interruzione in qualsiasi momento durante l'esecuzione del flusso di lavoro. Tuttavia, se si aggiorna ilworkflow.js **file** dopo l'avvio dell'esecuzione, i punti di interruzione non vengono aggiornati automaticamente. Per aggiornare i punti di interruzione, riavviare l'app per la logica.

Per informazioni generali, vedere [Punti di interruzione - Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Eseguire, testare ed eseguire il debug in locale

Per testare l'app per la logica, seguire questa procedura per avviare una sessione di debug e trovare l'URL dell'endpoint creato dal trigger Richiesta. Questo URL è necessario per poter inviare in un secondo momento una richiesta a tale endpoint.

1. Per eseguire il debug di un flusso di lavoro senza stato più facilmente, è possibile [abilitare la cronologia di esecuzione per tale flusso di lavoro.](#enable-run-history-stateless)

1. Nella barra Visual Studio Code attività aprire il menu **Esegui** e selezionare **Avvia debug** (F5).

   Verrà **visualizzata** la finestra Terminale in modo da poter esaminare la sessione di debug.

   > [!NOTE]
   > Se viene visualizzato **l'errore "L'errore esiste dopo l'esecuzione di preLaunchTask 'generateDebugSymbols'",** vedere la sezione sulla risoluzione dei problemi, La sessione di debug non viene [avviata.](#debugging-fails-to-start)

1. Trovare ora l'URL di callback per l'endpoint nel trigger Richiesta.

   1. Riaprire il riquadro Esplora risorse in modo da poter visualizzare il progetto.

   1. Dal menu **workflow.jsmenu** di scelta rapida del file selezionare **Panoramica**.

      ![Screenshot che mostra il riquadro Esplora risorse e la finestra di collegamento per workflow.jsfile con l'opzione "Panoramica" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Trovare il **valore URL di callback,** che è simile a questo URL per il trigger di richiesta di esempio:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Screenshot che mostra la pagina di panoramica del flusso di lavoro con l'URL di callback](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Per testare l'URL di callback attivando il flusso di lavoro dell'app per la logica, aprire [Postman](https://www.postman.com/downloads/) o lo strumento preferito per la creazione e l'invio di richieste.

   Questo esempio continua con Postman. Per altre informazioni, vedere [Postman Attività iniziali](https://learning.postman.com/docs/getting-started/introduction/).

   1. Sulla barra degli strumenti di Postman selezionare **Nuovo**.

      ![Screenshot che mostra Postman con il pulsante Nuovo selezionato](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Nel riquadro **Crea nuovo** selezionare Richiesta in **Blocchi predefiniti.** 

   1. Nella finestra **Salva richiesta** in **Nome richiesta** specificare un nome per la richiesta, ad esempio `Test workflow trigger` .

   1. In **Selezionare una raccolta o una cartella** in cui salvare selezionare Crea **raccolta**.

   1. In **Tutte le raccolte** specificare un nome per la raccolta da creare per organizzare le richieste, premere INVIO e selezionare Salva per <***nome-raccolta* >**. In questo esempio viene `Logic Apps requests` utilizzato come nome della raccolta.

      Viene aperto il riquadro delle richieste di Postman in modo da poter inviare una richiesta all'URL di callback per il trigger Richiesta.

      ![Screenshot che mostra Postman con il riquadro delle richieste aperto](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Tornare a Visual Studio Code. dalla pagina di panoramica del flusso di lavoro copiare il valore **della proprietà URL** di callback.

   1. Tornare a Postman. Nel riquadro della richiesta, accanto all'elenco dei metodi, che attualmente mostra **GET** come metodo di richiesta predefinito, incollare l'URL di callback copiato in precedenza nella casella dell'indirizzo e selezionare **Invia**.

      ![Screenshot che mostra Postman e l'URL di callback nella casella dell'indirizzo con il pulsante Invia selezionato](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Il flusso di lavoro dell'app per la logica di esempio invia un messaggio di posta elettronica simile all'esempio seguente:

      ![Screenshot che mostra la posta elettronica di Outlook come descritto nell'esempio](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. In Visual Studio Code tornare alla pagina di panoramica del flusso di lavoro.

   Se è stato creato un flusso di lavoro con stato, dopo che la richiesta inviata ha attivato il flusso di lavoro, la pagina di panoramica mostra lo stato di esecuzione e la cronologia del flusso di lavoro.

   > [!TIP]
   > Se lo stato di esecuzione non viene visualizzato, provare ad aggiornare la pagina di panoramica selezionando **Aggiorna**. Non viene eseguita alcuna esecuzione per un trigger ignorato a causa di criteri non soddisfatti o di assenza di dati.

   ![Screenshot che mostra la pagina di panoramica del flusso di lavoro con lo stato di esecuzione e la cronologia](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Stato dell'esecuzione | Descrizione |
   |------------|-------------|
   | **Aborted** | L'esecuzione è stata arrestata o non è stata completata a causa di problemi esterni, ad esempio un'interruzione del sistema o una sottoscrizione di Azure in ritardo. |
   | **annullato** | L'esecuzione è stata attivata e avviata ma ha ricevuto una richiesta di annullamento. |
   | **Operazione non riuscita** | Almeno un'azione nell'esecuzione non è riuscita. Non sono state impostate azioni successive nel flusso di lavoro per gestire l'errore. |
   | **Running** | L'esecuzione è stata attivata ed è in corso, ma questo stato può [](logic-apps-limits-and-config.md) essere visualizzato anche per un'esecuzione limitata a causa di limiti di azione o del [piano tariffario corrente.](https://azure.microsoft.com/pricing/details/logic-apps/) <p><p>**Suggerimento:** se si configura la [registrazione diagnostica,](monitor-logic-apps-log-analytics.md)è possibile ottenere informazioni su eventuali eventi di limitazione che si verificano. |
   | **Completato** | L'esecuzione è riuscita. Se un'azione ha avuto esito negativo, un'azione successiva nel flusso di lavoro ha gestito l'errore. |
   | **Timeout** | Timeout dell'esecuzione perché la durata corrente ha superato il limite di durata dell'esecuzione, controllato dall'impostazione Conservazione cronologia di esecuzione [ **in giorni**](logic-apps-limits-and-config.md#run-duration-retention-limits). La durata di un'esecuzione viene calcolata usando l'ora di inizio e il limite di durata dell'esecuzione in tale ora di inizio. <p><p>**Nota:** se la durata dell'esecuzione supera anche il limite di conservazione della cronologia di esecuzione *corrente,* controllato anche dall'impostazione Conservazione cronologia di esecuzione [ **in**](logic-apps-limits-and-config.md#run-duration-retention-limits)giorni , l'esecuzione viene cancellata dalla cronologia di esecuzione da un processo di pulizia giornaliero. Indipendentemente dal timeout o dal completamento dell'esecuzione, il periodo di conservazione viene sempre calcolato usando l'ora di inizio e il *limite di* conservazione corrente dell'esecuzione. Pertanto, se si riduce il limite di durata per un'esecuzione in fase di esecuzione, si verifica il timeout dell'esecuzione. Tuttavia, l'esecuzione rimane o viene cancellata dalla cronologia delle esecuzioni a seconda che la durata dell'esecuzione superi il limite di conservazione. |
   | **Attesa** | L'esecuzione non è stata avviata o è sospesa, ad esempio a causa di un'istanza del flusso di lavoro precedente ancora in esecuzione. |
   |||

1. Per esaminare gli stati per ogni passaggio in un'esecuzione specifica e gli input e gli output del passaggio, selezionare il pulsante con i puntini di sospensione (**...**) per l'esecuzione e selezionare **Mostra esecuzione**.

   ![Screenshot che mostra la riga della cronologia di esecuzione del flusso di lavoro con il pulsante con i puntini di sospensione e l'opzione "Mostra esecuzione" selezionata](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code apre la visualizzazione di monitoraggio e mostra lo stato per ogni passaggio dell'esecuzione.

   ![Screenshot che mostra ogni passaggio nell'esecuzione del flusso di lavoro e il relativo stato](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Se un'esecuzione non è riuscita e un passaggio nella visualizzazione di monitoraggio mostra l'errore, questo problema potrebbe derivare da un nome di trigger o un nome di azione più lungo che fa sì che il Uniform Resource Identifier (URI) sottostante superi il limite di caratteri `400 Bad Request` predefinito. Per altre informazioni, vedere ["400 Richiesta non valida".](#400-bad-request)

   Ecco i possibili stati che ogni passaggio del flusso di lavoro può avere:

   | Stato dell'azione | Icona | Descrizione |
   |---------------|------|-------------|
   | **Aborted** | ![Icona per lo stato dell'azione "Interrotto"][aborted-icon] | L'azione è stata arrestata o non è stata completata a causa di problemi esterni, ad esempio un'interruzione del sistema o una sottoscrizione di Azure scaduta. |
   | **annullato** | ![Icona per lo stato dell'azione "Annullato"][cancelled-icon] | L'azione era in esecuzione ma ha ricevuto una richiesta di annullamento. |
   | **Operazione non riuscita** | ![Icona per lo stato dell'azione "Non riuscito"][failed-icon] | L'azione non è riuscita. |
   | **Running** | ![Icona per lo stato dell'azione "In esecuzione"][running-icon] | L'azione è attualmente in esecuzione. |
   | **Ignorato** | ![Icona per lo stato dell'azione "Ignorato"][skipped-icon] | L'azione è stata ignorata perché l'azione immediatamente precedente non è riuscita. Un'azione ha una condizione che richiede che l'azione precedente venga completata correttamente `runAfter` prima che l'azione corrente possa essere eseguita. |
   | **Completato** | ![Icona per lo stato dell'azione "Operazione completata"][succeeded-icon] | L'azione è riuscita. |
   | **Esito positivo con tentativi** | ![Icona per lo stato dell'azione "Operazione completata con tentativi"][succeeded-with-retries-icon] | L'azione è riuscita ma solo dopo uno o più tentativi. Per esaminare la cronologia dei tentativi, nella visualizzazione dei dettagli della cronologia di esecuzione selezionare l'azione in modo che sia possibile visualizzare gli input e gli output. |
   | **Timeout** | ![Icona per lo stato dell'azione "Timeout"][timed-out-icon] | L'azione è stata arrestata a causa del limite di timeout specificato dalle impostazioni dell'azione. |
   | **Attesa** | ![Icona per lo stato dell'azione "In attesa"][waiting-icon] | Si applica a un'azione webhook in attesa di una richiesta in ingresso da un chiamante. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Per esaminare gli input e gli output per ogni passaggio, selezionare il passaggio da esaminare.

   ![Screenshot che mostra lo stato di ogni passaggio del flusso di lavoro e gli input e gli output nell'azione espansa "Invia un messaggio di posta elettronica"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Per esaminare ulteriormente gli input e gli output non elaborati per tale passaggio, selezionare **Mostra input non** elaborati o Mostra output non **elaborati**.

1. Per arrestare la sessione di debug, **scegliere** Arresta debug **dal** menu Esegui (MAIUSC+F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Restituire una risposta

Per restituire una risposta al chiamante che ha inviato una richiesta all'app per la logica, è possibile usare l'azione [predefinita Risposta](../connectors/connectors-native-reqres.md) per un flusso di lavoro che inizia con il trigger Richiesta.

1. Nell'azione Invia messaggio di posta elettronica della **finestra** di progettazione del flusso di lavoro selezionare **Nuovo passaggio**.

   Nella **finestra di progettazione viene** visualizzato  il prompt Scegliere un'operazione e il riquadro Aggiungi un'azione viene riaperto in modo da poter selezionare l'azione successiva.

1. Nella casella **di ricerca** Scegliere  un'azione del riquadro Aggiungi un'azione assicurarsi che sia **selezionata l'opzione** Predefinita. Nella casella di ricerca immettere `response` e selezionare **l'azione** Risposta.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro con l'azione Response selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Quando **l'azione** Risposta viene visualizzata nella finestra di progettazione, viene aperto automaticamente il riquadro dei dettagli dell'azione.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro con il riquadro dei dettagli dell'azione "Risposta" aperto e la proprietà "Corpo" impostata sul valore della proprietà "Corpo" dell'azione "Invia un messaggio di posta elettronica".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Nella **scheda** Parametri specificare le informazioni necessarie per la funzione che si vuole chiamare.

   Questo esempio restituisce il **valore della** proprietà Body restituito dall'azione Invia un messaggio **di posta** elettronica.

   1. Fare clic **all'interno della** casella della proprietà Corpo per visualizzare l'elenco di contenuto dinamico e visualizzare i valori di output disponibili del trigger e delle azioni precedenti nel flusso di lavoro.

      ![Screenshot che mostra il riquadro dei dettagli dell'azione "Risposta" con il puntatore del mouse all'interno della proprietà "Corpo" in modo che venga visualizzato l'elenco di contenuto dinamico.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Nell'elenco di contenuto dinamico, in **Invia un messaggio di posta elettronica** selezionare **Corpo.**

      ![Screenshot che mostra l'elenco di contenuto dinamico aperto. Nell'elenco, sotto l'intestazione "Invia un messaggio di posta elettronica", viene selezionato il valore di output "Corpo".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Al termine, la proprietà Corpo  dell'azione Risposta è  ora impostata sul valore di **output** Corpo dell'azione Invia un messaggio di posta elettronica.

      ![Screenshot che mostra lo stato di ogni passaggio del flusso di lavoro oltre agli input e agli output nell'azione "Response" espansa.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Nella finestra di progettazione selezionare **Salva**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Eseguire di nuovo il test dell'app per la logica

Dopo aver apportato gli aggiornamenti all'app per la logica, è possibile eseguire un altro test rieseguindo il debugger in Visual Studio e inviando un'altra richiesta per attivare l'app per la logica aggiornata, in modo analogo ai passaggi in [Eseguire, testare](#run-test-debug-locally)ed eseguire il debug in locale.

1. Nella barra Visual Studio Code attività, aprire il menu **Esegui** e selezionare **Avvia debug** (F5).

1. In Postman o nello strumento per la creazione e l'invio di richieste inviare un'altra richiesta per attivare il flusso di lavoro.

1. Se è stato creato un flusso di lavoro con stato, nella pagina di panoramica del flusso di lavoro controllare lo stato per l'esecuzione più recente. Per visualizzare lo stato, gli input e gli output per ogni passaggio dell'esecuzione, selezionare il pulsante con i puntini di sospensione (**...**) per l'esecuzione e selezionare **Mostra esecuzione**.

   Ecco ad esempio lo stato di un'esecuzione dopo l'aggiornamento del flusso di lavoro di esempio con l'azione Risposta.

   ![Screenshot che mostra lo stato di ogni passaggio nel flusso di lavoro aggiornato e gli input e gli output nell'azione "Risposta" espansa.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Per arrestare la sessione di debug, **scegliere** Arresta debug **dal** menu Esegui (MAIUSC+F5).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Trovare i nomi di dominio per l'accesso al firewall

Prima di distribuire ed eseguire il flusso di lavoro dell'app per la logica nel portale di Azure, se l'ambiente ha requisiti di rete rigorosi o firewall che limitano il traffico, è necessario configurare le autorizzazioni per tutte le connessioni trigger o azione presenti nel flusso di lavoro.

Per trovare i nomi di dominio completi (FQDN) per queste connessioni, seguire questa procedura:

1. Nel progetto di app per la logica aprire ilconnections.jsnel **file,** creato dopo aver aggiunto il primo trigger o azione basata sulla connessione al flusso di lavoro e trovare l'oggetto `managedApiConnections` .

1. Per ogni connessione creata, trovare, copiare e salvare il valore della proprietà in un punto sicuro in modo da poter configurare il `connectionRuntimeUrl` firewall con queste informazioni.

   Questo esempio **connections.jsfile** contiene due connessioni, una connessione AS2 e una connessione a Office 365 con questi `connectionRuntimeUrl` valori:

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Distribuisci in Azure

Da Visual Studio Code, è possibile pubblicare direttamente il progetto in Azure, che distribuisce l'app per la logica usando il nuovo tipo di risorsa App per la logica **(anteprima).** Analogamente alla risorsa app per le funzioni in Funzioni di Azure, la distribuzione per questo nuovo tipo di risorsa richiede la selezione di un piano [di hosting](../app-service/overview-hosting-plans.md)e di un piano tariffario, che è possibile configurare durante la distribuzione. Per altre informazioni sui piani di hosting e sui prezzi, vedere questi argomenti:

* [Aumentare le dimensioni di un Servizio app di Azure](../app-service/manage-scale-up.md)
* [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)

È possibile pubblicare l'app per la logica come nuova risorsa, che crea automaticamente tutte le risorse necessarie, ad esempio un account Archiviazione di Azure, in modo analogo ai requisiti [dell'app per le funzioni.](../azure-functions/storage-considerations.md) In caso contrario, è possibile pubblicare l'app per la logica in una risorsa dell'app per la logica **(anteprima)** distribuita in precedenza, che sovrascrive l'app per la logica.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Pubblicare in una nuova risorsa app per la logica (anteprima)

1. Nella barra Visual Studio Code attività selezionare l'icona Di Azure.

1. Nella barra degli strumenti del riquadro Azure: App per **la logica (anteprima)** **selezionare Distribuisci in App per la logica.**

   ![Screenshot che mostra il riquadro "Azure: App per la logica (anteprima)" e la barra degli strumenti del riquadro con l'opzione "Distribuisci in app per la logica" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Se richiesto, selezionare la sottoscrizione di Azure da usare per la distribuzione dell'app per la logica.

1. Nell'elenco visualizzato Visual Studio Code, selezionare una delle opzioni seguenti:

   * **Creare una nuova app per la logica (anteprima) in Azure** (rapida)
   * **Creare una nuova app per la logica (anteprima) in Azure Advanced**
   * Una risorsa app **per la logica (anteprima) distribuita in** precedenza, se esistente

   Questo esempio continua con **Creare una nuova app per la logica (anteprima) in Azure Advanced.**

   ![Screenshot che mostra il riquadro "Azure: App per la logica (anteprima)" con un elenco con l'opzione "Crea nuova app per la logica (anteprima) in Azure" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Per creare la nuova risorsa **app per la logica (anteprima),** seguire questa procedura:

   1. Specificare un nome univoco globale per la nuova app per la logica, ovvero il nome da usare per la risorsa App per la logica **(anteprima).** In questo esempio viene utilizzato `Fabrikam-Workflows-App`.

      ![Screenshot che mostra il riquadro "Azure: App per la logica (anteprima)" e un prompt per specificare un nome per la nuova app per la logica da creare.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selezionare un [piano di hosting](../app-service/overview-hosting-plans.md) per la nuova app per la logica, ovvero Piano di servizio [ **app** (dedicato)](../azure-functions/dedicated-plan.md) o [**Premium.**](../azure-functions/functions-premium-plan.md)

      > [!IMPORTANT]
      > I piani a consumo non sono supportati né disponibili per questo tipo di risorsa. Il piano selezionato influisce sulle funzionalità e sui piani tariffari disponibili in un secondo momento. Per altre informazioni, vedere questi argomenti: 
      >
      > * [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)
      > * [Dettagli sui prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Ad esempio, il piano Premium fornisce l'accesso alle funzionalità di rete, ad esempio connettersi e integrarsi privatamente con le reti virtuali di Azure, in modo simile a Funzioni di Azure quando si creano e distribuiscono le app per la logica. 
      > Per altre informazioni, vedere questi argomenti:
      > 
      > * [Opzioni di rete di Funzioni di Azure](../azure-functions/functions-networking-options.md)
      > * [App per la logica di Azure in esecuzione ovunque - Funzionalità di rete con App per la logica di Azure Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      In questo esempio viene utilizzato **il piano di servizio app**.

      ![Screenshot che mostra il riquadro "Azure: App per la logica (anteprima)" e un prompt per selezionare "Piano di servizio app" o "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Creare un nuovo piano di servizio app o selezionare un piano esistente. In questo esempio viene selezionato **Crea nuovo piano di servizio app**.

      ![Screenshot che mostra il riquadro "Azure: App per la logica (anteprima)" e la richiesta di "Creare un nuovo piano di servizio app" o selezionare un piano di servizio app esistente.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Specificare un nome per il piano di servizio app e quindi selezionare un piano [tariffario](../app-service/overview-hosting-plans.md) per il piano. In questo esempio viene selezionato il **piano gratuito F1.**

      ![Screenshot che mostra il riquadro "Azure: App per la logica (anteprima)" e un prompt per selezionare un piano tariffario.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Per ottenere prestazioni ottimali, trovare e selezionare lo stesso gruppo di risorse del progetto per la distribuzione.

      > [!NOTE]
      > Anche se è possibile creare o usare un gruppo di risorse diverso, questa operazione potrebbe influire sulle prestazioni. Se si crea o si sceglie un gruppo di risorse diverso, ma si annulla dopo la visualizzazione della richiesta di conferma, anche la distribuzione viene annullata.

   1. Per i flussi di lavoro con stato, selezionare **Crea nuovo account di archiviazione** o un account di archiviazione esistente.

      ![Screenshot che mostra il riquadro "Azure: App per la logica (anteprima)" e una richiesta per creare o selezionare un account di archiviazione.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Se le impostazioni di creazione e distribuzione dell'app per la logica supportano [l'uso](../azure-monitor/app/app-insights-overview.md)di Application Insights , è possibile abilitare facoltativamente la registrazione diagnostica e la traccia per l'app per la logica. È possibile farlo quando si distribuisce l'app per la logica da Visual Studio Code o dopo la distribuzione. È necessario avere un'istanza Application Insights, ma è possibile creare questa risorsa [in](../azure-monitor/app/create-workspace-resource.md)anticipo, quando si distribuisce l'app per la logica o dopo la distribuzione.

      Per abilitare ora la registrazione e la traccia, seguire questa procedura:

      1. Selezionare una risorsa di Application Insights esistente o **Crea nuova Application Insights risorsa**.

      1. Nel [portale di Azure](https://portal.azure.com)passare alla risorsa Application Insights.

      1. Nel menu delle risorse selezionare **Panoramica.** Trovare e copiare il valore **della chiave di** strumentazione.

      1. Nella Visual Studio Code radice del progetto aprire illocal.settings.js **nel** file .

      1. `Values`Nell'oggetto aggiungere la proprietà e impostare il valore sulla chiave di `APPINSIGHTS_INSTRUMENTATIONKEY` strumentazione, ad esempio:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > È possibile controllare se i nomi dei trigger e delle azioni vengono visualizzati correttamente nell Application Insights istanza.
         >
         > 1. Nel portale di Azure passare alla risorsa Application Insights lavoro.
         >
         > 2. Nel menu della risorsa, in **Analisi** selezionare **Mappa delle applicazioni.**
         >
         > 3. Esaminare i nomi delle operazioni visualizzati nella mappa.
         >
         > Alcune richieste in ingresso da trigger predefiniti potrebbero essere visualizzate come duplicati nella mappa delle applicazioni. 
         > Anziché usare il formato , questi duplicati usano il nome del flusso di lavoro come nome dell'operazione e provengono `WorkflowName.ActionName` dall'host Funzioni di Azure.

      1. Successivamente, è possibile modificare facoltativamente il livello di gravità per i dati di traccia raccolti dall'app per la logica e inviati all'Application Insights istanza.

         Ogni volta che si verifica un evento correlato al flusso di lavoro, ad esempio quando viene attivato un flusso di lavoro o quando viene eseguita un'azione, il runtime genera varie tracce. Queste tracce coprono la durata del flusso di lavoro e includono, ma non solo, i tipi di evento seguenti:

         * Attività del servizio, ad esempio avvio, arresto ed errori.
         * Processi e attività del dispatcher.
         * Attività del flusso di lavoro, ad esempio trigger, azione ed esecuzione.
         * Attività di richiesta di archiviazione, ad esempio esito positivo o negativo.
         * Attività di richiesta HTTP, ad esempio in ingresso, in uscita, esito positivo e negativo.
         * Eventuali tracce di sviluppo, ad esempio i messaggi di debug.

         Ogni tipo di evento viene assegnato a un livello di gravità. Ad esempio, il livello acquisisce i messaggi più dettagliati, mentre il livello acquisisce le attività generali nel flusso di lavoro, ad esempio quando l'app per la logica, il flusso di lavoro, il trigger e le azioni vengono `Trace` `Information` avviati e arrestati. Questa tabella descrive i livelli di gravità e i relativi tipi di traccia:

         | Livello di gravità | Tipo di traccia |
         |----------------|------------|
         | Critico | Log che descrivono un errore irreversibile nell'app per la logica. |
         | Debug | Log che è possibile usare per l'analisi durante lo sviluppo, ad esempio le chiamate HTTP in ingresso e in uscita. |
         | Errore | Log che indicano un errore nell'esecuzione del flusso di lavoro, ma non un errore generale nell'app per la logica. |
         | Informazioni | Log che tiene traccia dell'attività generale nell'app per la logica o nel flusso di lavoro, ad esempio: <p><p>- All'avvio e alla fine di un trigger, un'azione o un'esecuzione. <br>- Quando l'app per la logica viene avviata o terminata. |
         | Trace | Log che contengono i messaggi più dettagliati, ad esempio richieste di archiviazione o attività del dispatcher, oltre a tutti i messaggi correlati all'attività di esecuzione del flusso di lavoro. |
         | Avviso | Log che evidenziano uno stato anomalo nell'app per la logica, ma non ne impediscono l'esecuzione. |
         |||

         Per impostare il livello di gravità, a livello di radice del progetto, aprire ilhost.js **nel** file e trovare l'oggetto `logging` . Questo oggetto controlla il filtro dei log per tutti i flussi di lavoro nell'app per la logica e segue il [layout ASP.NET Core per il filtro del tipo di log](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Se `logging` l'oggetto non contiene un `logLevel` oggetto che include la proprietà , aggiungere tali `Host.Triggers.Workflow` elementi. Impostare la proprietà sul livello di gravità per il tipo di traccia desiderato, ad esempio:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Al termine della procedura di distribuzione, Visual Studio Code la creazione e la distribuzione delle risorse necessarie per la pubblicazione dell'app per la logica.

1. Per esaminare e monitorare il processo di distribuzione, **scegliere** Output dal menu **Visualizza.** Nell'elenco della barra degli strumenti della finestra Output **selezionare App per la logica di Azure**.

   ![Screenshot che mostra la finestra Output con l'opzione "App per la logica di Azure" selezionata nell'elenco della barra degli strumenti insieme allo stato e allo stato della distribuzione.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Al Visual Studio Code completare la distribuzione dell'app per la logica in Azure, viene visualizzato il messaggio seguente:

   ![Screenshot che mostra un messaggio che indica che la distribuzione in Azure è stata completata correttamente.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   L'app per la logica è ora attiva in Azure e abilitata per impostazione predefinita.

Successivamente, è possibile apprendere come eseguire queste attività:

* [Aggiungere un flusso di lavoro vuoto al progetto](#add-workflow-existing-project).

* [Gestire le app per la logica distribuite Visual Studio Code](#manage-deployed-apps-vs-code) o usando il [portale di Azure](#manage-deployed-apps-portal).

* [Abilitare la cronologia di esecuzione nei flussi di lavoro senza stato.](#enable-run-history-stateless)

* [Abilitare la visualizzazione di monitoraggio nel portale di Azure per un'app per la logica distribuita.](#enable-monitoring)

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Aggiungere un flusso di lavoro vuoto al progetto

È possibile avere più flussi di lavoro nel progetto di app per la logica. Per aggiungere un flusso di lavoro vuoto al progetto, seguire questa procedura:

1. Nella barra Visual Studio Code attività selezionare l'icona Di Azure.

1. Nel riquadro Azure accanto ad Azure: App per la logica **(anteprima)** selezionare Crea flusso **di** lavoro (icona per App per la logica di Azure).

1. Selezionare il tipo di flusso di lavoro da aggiungere: **Con** stato o **Senza stato**

1. Specificare un nome per il flusso di lavoro.

Al termine, nel progetto viene visualizzata una nuova cartella del flusso di lavoro insieme a unworkflow.js **nel** file per la definizione del flusso di lavoro.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Gestire le app per la logica distribuite in Visual Studio Code

In Visual Studio Code è possibile visualizzare tutte le app per la logica distribuite  nella sottoscrizione di Azure, che si tratta del tipo di risorsa App per la logica originale o App per la logica **(anteprima)** e selezionare le attività che consentono di gestire tali app per la logica. Tuttavia, per accedere a entrambi i  tipi di risorse, sono necessarie le estensioni App per la logica di Azure e **App per la logica di Azure (anteprima)** per Visual Studio Code.

1. Sulla barra degli strumenti a sinistra selezionare l'icona di Azure. Nel riquadro **Azure: App per la logica (anteprima)** espandere la sottoscrizione, che mostra tutte le app per la logica distribuite per la sottoscrizione.

1. Aprire l'app per la logica che si vuole gestire. Dal menu di scelta rapida dell'app per la logica selezionare l'attività che si vuole eseguire.

   Ad esempio, è possibile selezionare attività come l'arresto, l'avvio, il riavvio o l'eliminazione dell'app per la logica distribuita.

   ![Screenshot che mostra Visual Studio Code con il riquadro di estensione "App per la logica di Azure (anteprima)" aperto e il flusso di lavoro distribuito.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Per visualizzare tutti i flussi di lavoro nell'app per la logica, espandere l'app per la logica e quindi espandere il **nodo Flussi di** lavoro.

1. Per visualizzare un flusso di lavoro specifico, aprire il menu di scelta rapida del flusso di lavoro e selezionare Apri **nella** finestra di progettazione , che apre il flusso di lavoro in modalità di sola lettura.

   Per modificare il flusso di lavoro, sono disponibili le opzioni seguenti:

   * In Visual Studio Code aprire ilworkflow.jsdel progetto nel **file** nella finestra di progettazione del flusso di lavoro, apportare le modifiche e ridistribuire l'app per la logica in Azure.

   * Nel portale di Azure trovare [e aprire l'app per la logica.](#manage-deployed-apps-portal) Trovare, modificare e salvare il flusso di lavoro.

1. Per aprire l'app per la logica distribuita nel portale di Azure, aprire il menu di scelta rapida dell'app per la logica e **selezionare Apri nel portale**.

   Il portale di Azure viene aperto nel browser, accede automaticamente al portale se si è connessi a Visual Studio Code e visualizza l'app per la logica.

   ![Screenshot che mostra la pagina portale di Azure per l'app per la logica in Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   È anche possibile accedere separatamente all'portale di Azure, usare la casella di ricerca del portale per trovare l'app per la logica e quindi selezionare l'app per la logica dall'elenco dei risultati.

   ![Screenshot che mostra la portale di Azure e la barra di ricerca con i risultati della ricerca per l'app per la logica distribuita, che viene visualizzata come selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Gestire le app per la logica distribuite nel portale

Nella portale di Azure è possibile visualizzare tutte le app per la logica distribuite presenti nella  sottoscrizione di Azure, sia che si tratta del tipo di risorsa App per la logica originale che del tipo di risorsa App per la logica **(anteprima).** Attualmente, ogni tipo di risorsa è organizzato e gestito come categorie separate in Azure. Per trovare le app per la logica con il **tipo di risorsa App** per la logica (anteprima), seguire questa procedura:

1. Nella casella portale di Azure ricerca immettere `logic app preview` . Quando viene visualizzato l'elenco dei risultati, **in Servizi** selezionare **App per la logica (anteprima).**

   ![Screenshot che mostra la casella portale di Azure ricerca con il testo di ricerca "anteprima dell'app per la logica".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Nel riquadro App per la logica **(anteprima)** trovare e selezionare l'app per la logica distribuita da Visual Studio Code.

   ![Screenshot che mostra le portale di Azure e le risorse dell'app per la logica (anteprima) distribuite in Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Il portale di Azure apre la pagina delle singole risorse per l'app per la logica selezionata.

   ![Screenshot che mostra la pagina delle risorse del flusso di lavoro dell'app per la logica nel portale di Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Per visualizzare i flussi di lavoro in questa app per la logica, nel menu dell'app per la logica selezionare **Flussi di lavoro.**

   Il **riquadro Flussi di** lavoro mostra tutti i flussi di lavoro nell'app per la logica corrente. In questo esempio viene illustrato il flusso di lavoro creato in Visual Studio Code.

   ![Screenshot che mostra una pagina delle risorse "App per la logica (anteprima)" con il riquadro "Flussi di lavoro" aperto e il flusso di lavoro distribuito](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Per visualizzare un flusso di lavoro, nel **riquadro Flussi di** lavoro selezionare tale flusso di lavoro.

   Verrà aperto il riquadro del flusso di lavoro in cui sono visualizzate altre informazioni e attività che è possibile eseguire sul flusso di lavoro.

   Ad esempio, per visualizzare i passaggi nel flusso di lavoro, selezionare **Progettazione**.

   ![Screenshot che mostra il riquadro "Panoramica" del flusso di lavoro selezionato, mentre il menu del flusso di lavoro mostra il comando "Designer" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Verrà aperta la finestra di progettazione del flusso di lavoro e verrà visualizzato il flusso di lavoro Visual Studio Code. È ora possibile apportare modifiche a questo flusso di lavoro nel portale di Azure.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e il flusso di lavoro distribuiti da Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Aggiungere un altro flusso di lavoro nel portale

Tramite il portale di Azure, è possibile aggiungere flussi di lavoro vuoti a una risorsa dell'app per la logica **(anteprima)** distribuita da Visual Studio Code e compilare tali flussi di lavoro nel portale di Azure.

1. Nel riquadro [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa **app per la logica distribuita (anteprima).**

1. Scegliere Flussi di lavoro dal menu dell'app per la **logica.** Nel riquadro **Flussi di** lavoro selezionare **Aggiungi**.

   ![Screenshot che mostra il riquadro "Flussi di lavoro" e la barra degli strumenti dell'app per la logica selezionata con il comando "Aggiungi" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Nel riquadro **Nuovo flusso di** lavoro specificare il nome per il flusso di lavoro. Selezionare **Stateful (Con stato)** **o Stateless** Create (Crea senza **>** **stato).**

   Dopo che Azure ha distribuito il  nuovo flusso di lavoro, visualizzato nel riquadro Flussi di lavoro, selezionare tale flusso di lavoro in modo da poter gestire ed eseguire altre attività, ad esempio l'apertura della finestra di progettazione o della visualizzazione codice.

   ![Screenshot che mostra il flusso di lavoro selezionato con le opzioni di gestione e revisione.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Ad esempio, aprendo la finestra di progettazione per un nuovo flusso di lavoro viene visualizzato un canvas vuoto. È ora possibile compilare questo flusso di lavoro nel portale di Azure.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e un flusso di lavoro vuoto.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Abilitare la cronologia di esecuzione per i flussi di lavoro senza stato

Per eseguire più facilmente il debug di un flusso di lavoro senza stato, è possibile abilitare la cronologia di esecuzione per tale flusso di lavoro e quindi disabilitare la cronologia di esecuzione al termine. Seguire questa procedura per Visual Studio Code oppure, se si lavora nel portale di Azure, vedere Creare flussi di lavoro con stato e senza [stato nel portale di Azure](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. Nel progetto Visual Studio Code espandere la cartella **workflow-designtime** e aprire illocal.settings.js **file** .

1. Aggiungere la `Workflows.{yourWorkflowName}.operationOptions` proprietà e impostare il valore su , ad `WithStatelessRunHistory` esempio:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS o Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Per disabilitare la cronologia di esecuzione al termine, impostare la proprietà `Workflows.{yourWorkflowName}.OperationOptions` su o eliminare la proprietà e il relativo `None` valore.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Abilitare la visualizzazione di monitoraggio nel portale di Azure

Dopo aver distribuito una risorsa app per la logica **(anteprima)** da Visual Studio Code ad Azure, è possibile esaminare qualsiasi cronologia di  esecuzione disponibile e i dettagli per un flusso di lavoro in tale risorsa usando il portale di Azure e l'esperienza Di monitoraggio per tale flusso di lavoro. Tuttavia, è prima necessario abilitare la funzionalità di **visualizzazione** Monitoraggio nella risorsa dell'app per la logica.

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa **App per la logica distribuita (anteprima).**

1. Nel menu della risorsa selezionare **CORS** in **API.**

1. Nel riquadro **CORS** aggiungere il carattere jolly (*) in **Origini consentite.**

1. Al termine, sulla barra degli strumenti **CORS** selezionare **Salva.**

   ![Screenshot che mostra l'portale di Azure con una risorsa app per la logica distribuita (anteprima). Nel menu delle risorse viene selezionato "CORS" con una nuova voce per "Origini consentite" impostata sul carattere jolly "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Abilitare o aprire la Application Insights dopo la distribuzione

Durante l'esecuzione del flusso di lavoro, l'app per la logica genera dati di telemetria insieme ad altri eventi. È possibile usare questi dati di telemetria per ottenere una migliore visibilità sull'esecuzione del flusso di lavoro e sul funzionamento del runtime di App per la logica in vari modi. È possibile monitorare il flusso di lavoro usando [Application Insights](../azure-monitor/app/app-insights-overview.md), che fornisce near real-time telemetria (metriche in tempo reale). Questa funzionalità consente di analizzare più facilmente gli errori e i problemi di prestazioni quando si usano questi dati per diagnosticare problemi, configurare avvisi e creare grafici.

Se le impostazioni di creazione e distribuzione dell'app per la logica supportano l'uso di [Application Insights](../azure-monitor/app/app-insights-overview.md), è possibile abilitare facoltativamente la registrazione diagnostica e la traccia per l'app per la logica. È possibile farlo quando si distribuisce l'app per la logica da Visual Studio Code o dopo la distribuzione. È necessario avere un'istanza Application Insights, ma è possibile creare questa risorsa [in](../azure-monitor/app/create-workspace-resource.md)anticipo, quando si distribuisce l'app per la logica o dopo la distribuzione.

Per abilitare Application Insights in un'app per la logica distribuita o per esaminare Application Insights dati quando sono già abilitati, seguire questa procedura:

1. Nell'portale di Azure trovare l'app per la logica distribuita.

1. Nel menu dell'app per la logica in **Impostazioni** selezionare **Application Insights**.

1. Se Application Insights abilitata, nel riquadro Application Insights **selezionare** Attiva **Application Insights**. Dopo l'aggiornamento del riquadro, nella parte inferiore selezionare **Applica**.

   Se Application Insights abilitata, nel **riquadro** Application Insights selezionare Visualizza Application Insights **dati**.

Dopo Application Insights, è possibile esaminare varie metriche per l'app per la logica. Per altre informazioni, vedere questi argomenti:

* [App per la logica di Azure esecuzione via Internet - Monitoraggio con Application Insights - parte 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [App per la logica di Azure esecuzione via Internet - Monitoraggio con Application Insights - parte 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Eseguire la distribuzione in Docker

È possibile distribuire l'app per la logica in un [contenitore Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) come ambiente host usando l'interfaccia della riga [di comando di .NET.](/dotnet/core/tools/) Con questi comandi è possibile compilare e pubblicare il progetto dell'app per la logica. È quindi possibile compilare ed eseguire il contenitore Docker come destinazione per la distribuzione dell'app per la logica.

Se non si ha familiarità con Docker, esaminare questi argomenti:

* [Che cos'è Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Introduzione a contenitori e Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introduzione a .NET e Docker](/dotnet/core/docker/introduction)
* [Contenitori, immagini e registri Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Esercitazione: Introduzione a Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Requisiti

* Account Archiviazione di Azure che l'app per la logica usa per la distribuzione

* Un file Docker per il flusso di lavoro da usare durante la compilazione del contenitore Docker

  Ad esempio, questo file Docker di esempio distribuisce un'app per la logica e specifica la stringa di connessione che contiene la chiave di accesso per l'account Archiviazione di Azure usato per pubblicare l'app per la logica nel portale di Azure. Per trovare questa stringa, vedere Ottenere la stringa [di connessione dell'account di archiviazione.](#find-storage-account-connection-string) Per altre informazioni, vedere [Procedure consigliate per la scrittura di file Docker.](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
  
  > [!IMPORTANT]
  > Per gli scenari di produzione, assicurarsi di proteggere e proteggere tali segreti e informazioni riservate, ad esempio usando un insieme di credenziali delle chiavi. Per i file Docker in particolare, vedere [Build images with BuildKit (Creare immagini con BuildKit)](https://docs.docker.com/develop/develop-images/build_enhancements/) e Manage sensitive data with Docker Secrets (Gestire i dati [sensibili con i segreti Docker).](https://docs.docker.com/engine/swarm/secrets/)

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Ottenere la stringa di connessione dell'account di archiviazione

Prima di poter compilare ed eseguire l'immagine del contenitore Docker, è necessario ottenere la stringa di connessione che contiene la chiave di accesso all'account di archiviazione. In precedenza è stato creato questo account di archiviazione come per usare l'estensione in macOS o Linux o quando è stata distribuita l'app per la logica nel portale di Azure.

Per trovare e copiare questa stringa di connessione, seguire questa procedura:

1. Nel menu portale di Azure account di archiviazione in **Impostazioni** selezionare **Chiavi di accesso.** 

1. Nel riquadro **Chiavi di accesso** trovare e copiare la stringa di connessione dell'account di archiviazione, simile all'esempio seguente:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Screenshot che mostra l'portale di Azure con le chiavi di accesso dell'account di archiviazione e la stringa di connessione copiate.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Per altre informazioni, vedere Gestire [le chiavi dell'account di archiviazione.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Salvare la stringa di connessione in un luogo sicuro in modo da poter aggiungere questa stringa al file Docker da usare per la distribuzione. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Trovare la chiave master per l'account di archiviazione

Quando il flusso di lavoro contiene un trigger di richiesta, è necessario ottenere [l'URL di callback](#get-callback-url-request-trigger) del trigger dopo aver compilato ed eseguito l'immagine del contenitore Docker. Per questa attività, è anche necessario specificare il valore della chiave master per l'account di archiviazione da usare per la distribuzione.

1. Per trovare questa chiave master, nel progetto aprire **azure-webjobs-secrets/{deployment-name}/host.jsnel** file .

1. Trovare la `AzureWebJobsStorage` proprietà e copiare il valore della chiave da questa sezione:

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. Salvare il valore della chiave in un punto sicuro per usarlo in un secondo momento.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Compilare ed eseguire l'immagine del contenitore Docker

1. Compilare l'immagine del contenitore Docker usando il file Docker ed eseguendo questo comando:

   `docker build --tag local/workflowcontainer .`

   Per altre informazioni, vedere [docker build](https://docs.docker.com/engine/reference/commandline/build/).

1. Eseguire il contenitore in locale usando questo comando:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Per altre informazioni, vedere [docker run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Ottenere l'URL di callback per il trigger di richiesta

Per un flusso di lavoro che usa il trigger Request, ottenere l'URL di callback del trigger inviando questa richiesta:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

Il `{trigger-name}` valore è il nome del trigger di richiesta visualizzato nella definizione JSON del flusso di lavoro. Il valore è definito nell'account Archiviazione di Azure impostato per la proprietà all'interno del `{master-key}` `AzureWebJobsStorage` file, **azure-webjobs-secrets/{deployment-name}/host.jsin**. Per altre informazioni, vedere Trovare [la chiave master dell'account di archiviazione.](#find-storage-account-master-key)

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Eliminare elementi dalla finestra di progettazione

Per eliminare un elemento nel flusso di lavoro dalla finestra di progettazione, seguire una delle procedure seguenti:

* Selezionare l'elemento, aprire il menu di scelta rapida dell'elemento (MAIUSC+F10) e selezionare **Elimina.** Per confermare, scegliere **OK**.

* Selezionare l'elemento e premere CANC. Per confermare, scegliere **OK**.

* Selezionare l'elemento in modo che si apra il riquadro dei dettagli per tale elemento. Nell'angolo superiore destro del riquadro aprire il menu con i puntini di sospensione (**...**) e selezionare **Elimina**. Per confermare, scegliere **OK**.

  ![Screenshot che mostra un elemento selezionato nella finestra di progettazione con il riquadro dei dettagli aperto, i puntini di sospensione selezionati e il comando "Elimina".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Se il menu con i puntini di sospensione non è visibile, espandere la finestra Visual Studio Code modo che il riquadro dei dettagli mostra i puntini di sospensione (**...**) nell'angolo superiore destro.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Risolvere errori e problemi

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Non è possibile aprire la finestra di progettazione

Quando si tenta di aprire la finestra di progettazione, viene visualizzato l'errore "Impossibile iniziare la fase di progettazione del flusso **di lavoro".** Se in precedenza si è tentato di aprire la finestra di progettazione e quindi si è interrotto o eliminato il progetto, il bundle di estensioni potrebbe non essere scaricato correttamente. Per verificare se questa è la causa del problema, seguire questa procedura:

  1. In Visual Studio Code aprire la finestra Output. Scegliere Output **dal** menu **Visualizza.**

  1. Nell'elenco nella barra del titolo della finestra Output selezionare **App per la logica di Azure (anteprima)** per poter esaminare l'output dell'estensione, ad esempio:

     ![Screenshot che mostra la finestra Output con l'opzione "App per la logica di Azure" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Esaminare l'output e verificare se viene visualizzato questo messaggio di errore:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Per correggere l'errore, eliminare la cartella **ExtensionBundles** in questo percorso **...\Users \{ nomeutente}\AppData\Local\Temp\Functions\ExtensionBundles** e riprovare ad aprire il **workflow.js** nel file nella finestra di progettazione.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Nuovi trigger e azioni non sono presenti nella selezione della finestra di progettazione per i flussi di lavoro creati in precedenza

App per la logica di Azure Preview supporta le azioni incorporate per le operazioni delle funzioni di Azure, le operazioni Liquid e le operazioni XML, ad esempio la convalida **XML** e **la trasformazione XML.** Tuttavia, per le app per la logica create in precedenza, queste azioni potrebbero non essere visualizzate nella selezione della finestra di progettazione per selezionare se Visual Studio Code usa una versione obsoleta del bundle di estensioni, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Inoltre, le azioni e il connettore per le operazioni delle funzioni di **Azure** non vengono visualizzati nella selezione della finestra di progettazione a meno che non sia stata abilitata o selezionata l'opzione Usa connettori di **Azure** durante la creazione dell'app per la logica. Se i connettori distribuiti in Azure non sono stati abilitati al momento della creazione dell'app, è possibile abilitarli dal progetto in Visual Studio Code. Aprire il **workflow.jsmenu** di scelta rapida e selezionare **Usa connettori da Azure.**

Per correggere il bundle obsoleto, seguire questa procedura per eliminare il bundle obsoleto, in modo da Visual Studio Code automaticamente il bundle di estensioni alla versione più recente.

> [!NOTE]
> Questa soluzione si applica solo alle app per la logica create e distribuite usando Visual Studio Code con l'estensione App per la logica di Azure (anteprima), non le app per la logica create usando l'portale di Azure. Vedere [Trigger e azioni supportati mancanti nella finestra di progettazione nella](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions)portale di Azure .

1. Salvare il lavoro che non si vuole perdere e chiudere Visual Studio.

1. Nel computer passare alla cartella seguente, che contiene le cartelle con controllo delle versioni per il bundle esistente:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Eliminare la cartella della versione per il bundle precedente, ad esempio se si dispone di una cartella per la versione 1.1.3, eliminare tale cartella.

1. Passare ora alla cartella seguente, che contiene le cartelle con controllo delle versioni per il pacchetto NuGet richiesto:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Eliminare la cartella della versione per il pacchetto precedente, ad esempio se si dispone di una cartella per la versione 1.0.0.8-preview, eliminare tale cartella.

1. Riaprire Visual Studio Code, il progetto e il **workflow.jsnel** file nella finestra di progettazione.

I trigger e le azioni mancanti vengono ora visualizzati nella finestra di progettazione.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 Richiesta non valida" viene visualizzato in un trigger o in un'azione

Quando un'esecuzione ha esito negativo e si esamina l'esecuzione nella visualizzazione di monitoraggio, questo errore potrebbe essere visualizzato in un trigger o in un'azione con un nome più lungo, che fa in modo che il Uniform Resource Identifier (URI) sottostante superi il limite di caratteri predefinito.

Per risolvere questo problema e modificare l'URI più lungo, modificare le chiavi del Registro di sistema e `UrlSegmentMaxCount` nel computer seguendo questa `UrlSegmentMaxLength` procedura. I valori predefiniti di queste chiavi sono descritti in questo argomento,Http.sys del Registro [ di sistema per Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Prima di iniziare, assicurarsi di salvare il lavoro. Questa soluzione richiede il riavvio del computer al termine, in modo che le modifiche possano essere applicate.

1. Nel computer aprire la finestra **Esegui** ed eseguire il `regedit` comando , che apre l'editor del Registro di sistema.

1. Nella casella **Controllo account utente** selezionare **Sì** per consentire le modifiche apportate al computer.

1. Nel riquadro sinistro, in **Computer**, espandere i nodi lungo il percorso, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** e quindi selezionare **Parametri**.

1. Nel riquadro destro trovare le chiavi del `UrlSegmentMaxCount` Registro di sistema e `UrlSegmentMaxLength` .

1. Aumentare questi valori di chiave in modo che gli URI possano contenere i nomi da usare. Se queste chiavi non esistono, aggiungerle alla **cartella Parameters** seguendo questa procedura:

   1. Dal menu **di** scelta rapida Parametri selezionare **Nuovo**  >  **valore DWORD (32 bit).**

   1. Nella casella di modifica visualizzata immettere `UrlSegmentMaxCount` come nuovo nome della chiave.

   1. Aprire il menu di scelta rapida del nuovo tasto e selezionare **Modifica.**

   1. Nella casella **Modifica stringa** visualizzata immettere il valore **della** chiave dati Valore desiderato in formato esadecimale o decimale. Ad esempio, in formato esadecimale equivale a `400` in `1024` decimale.

   1. Per aggiungere il `UrlSegmentMaxLength` valore della chiave, ripetere questi passaggi.

   Dopo aver incrementato o aggiunto questi valori di chiave, l'editor del Registro di sistema è simile all'esempio seguente:

   ![Screenshot che mostra l'editor del Registro di sistema.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Quando si è pronti, riavviare il computer in modo che le modifiche possano essere applicate.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Non è possibile avviare la sessione di debug

Quando si tenta di avviare una sessione di debug, viene visualizzato l'errore "Errore esistente dopo **l'esecuzione di preLaunchTask 'generateDebugSymbols'".** Per risolvere questo problema, modificare il **tasks.jsfile** nel progetto per ignorare la generazione dei simboli.

1. Nel progetto espandere la cartella **.vscode** e aprire iltasks.js **file** .

1. Nell'attività seguente eliminare la riga , insieme alla virgola che `"dependsOn: "generateDebugSymbols"` termina la riga precedente, ad esempio:

   Prima di:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Dopo:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Passaggi successivi

Le esperienze dell'utente con l'estensione App per la logica di Azure (anteprima) sono molto condivise.

* Per i bug o i problemi, [creare i problemi in GitHub.](https://github.com/Azure/logicapps/issues)
* Per domande, richieste, commenti e altri commenti, [usare questo modulo di feedback.](https://aka.ms/lafeedback)
