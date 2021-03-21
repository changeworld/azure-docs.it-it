---
title: Creare flussi di lavoro di anteprima di app per la logica in Visual Studio Code
description: Compilare ed eseguire flussi di lavoro per scenari di automazione e integrazione in Visual Studio Code con l'estensione app per la logica di Azure (anteprima).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: f7f8082cc9120345336610d5cb49741140d3b606
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557013"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Creare flussi di lavoro con stato e senza stato in Visual Studio Code con l'estensione app per la logica di Azure (anteprima)

> [!IMPORTANT]
> Questa funzionalità è in anteprima pubblica, viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Con l'anteprima di app per la [logica di Azure](logic-apps-overview-preview.md)puoi creare soluzioni di automazione e integrazione tra app, dati, servizi cloud e sistemi creando ed eseguendo app per la logica che includono flussi di lavoro con [ *stato* e senza  stato](logic-apps-overview-preview.md#stateful-stateless) in Visual Studio Code usando l'estensione app per la logica di Azure (anteprima). Usando questo nuovo tipo di app per la logica, è possibile creare più flussi di lavoro basati sul runtime di anteprima di app per la logica di Azure riprogettato, che offre portabilità, migliori prestazioni e flessibilità per la distribuzione e l'esecuzione in diversi ambienti host, non solo Azure, ma anche contenitori docker. Per altre informazioni sul nuovo tipo di app per la logica, vedere [Panoramica dell'anteprima di app per la logica di Azure](logic-apps-overview-preview.md).

![Screenshot che mostra Visual Studio Code, il progetto di app per la logica e il flusso di lavoro.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

In Visual Studio Code è possibile iniziare creando un progetto in cui è possibile compilare ed eseguire *localmente* i flussi di lavoro dell'app per la logica nell'ambiente di sviluppo usando l'estensione app per la logica di Azure (anteprima). Anche se è possibile iniziare [creando una nuova risorsa **app per la logica (anteprima)** nel portale di Azure](create-stateful-stateless-workflows-azure-portal.md), entrambi gli approcci consentono di distribuire ed eseguire l'app per la logica negli stessi tipi di ambienti host.

Nel frattempo, è comunque possibile creare il tipo originale di app per la logica. Sebbene le esperienze di sviluppo in Visual Studio Code differenze tra i tipi di app per la logica originali e nuovi, la sottoscrizione di Azure può includere entrambi i tipi. È possibile visualizzare e accedere a tutte le app per la logica distribuite nella sottoscrizione di Azure, ma le app sono organizzate nelle rispettive categorie e sezioni.

Questo articolo illustra come creare un'app per la logica e un flusso di lavoro in Visual Studio Code usando l'estensione app per la logica di Azure (anteprima) ed eseguendo queste attività di alto livello:

* Creare un progetto per l'app per la logica e il flusso di lavoro.

* Aggiungere un trigger e un'azione.

* Eseguire, testare, eseguire il debug ed esaminare la cronologia di esecuzione in locale.

* Trovare i dettagli del nome di dominio per l'accesso al firewall.

* Eseguire la distribuzione in Azure, che include facoltativamente l'abilitazione di Application Insights.

* Gestire l'app per la logica distribuita in Visual Studio Code e il portale di Azure.

* Abilitare la cronologia di esecuzione per i flussi di lavoro senza stato.

* Abilitare o aprire il Application Insights dopo la distribuzione.

* Eseguire la distribuzione in un contenitore Docker che può essere eseguito ovunque.

> [!NOTE]
> Per informazioni sui problemi noti correnti, vedere la pagina relativa alle app per la [logica Public Preview problemi noti in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="access-and-connectivity"></a>Accesso e connettività

* Accedere a Internet in modo da poter scaricare i requisiti, connettersi da Visual Studio Code all'account Azure e pubblicare da Visual Studio Code ad Azure, un contenitore Docker o un altro ambiente.

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Per compilare la stessa app per la logica di esempio in questo articolo, è necessario un account di posta elettronica di Office 365 Outlook che usa un account Microsoft aziendale o dell'Istituto di istruzione per accedere.

  Se si sceglie di usare un [connettore di posta elettronica diverso supportato da app per la logica di Azure](/connectors/), ad esempio Outlook.com o [gmail](../connectors/connectors-google-data-security-privacy-policy.md), è comunque possibile seguire l'esempio e la procedura complessiva generale è la stessa, ma l'interfaccia utente e le opzioni potrebbero variare in qualche modo. Se ad esempio si usa il connettore Outlook.com, usare il account Microsoft personale per accedere.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Requisiti di archiviazione

#### <a name="windows"></a>Windows

Per compilare ed eseguire localmente il progetto di app per la logica in Visual Studio Code quando si usa Windows, seguire questa procedura per configurare l'emulatore di archiviazione di Azure:

1. Scaricare e installare l' [emulatore di archiviazione di Azure 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Se non ne è già disponibile uno, è necessario disporre di un'installazione locale del database SQL, ad esempio la [versione gratuita SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658), in modo che l'emulatore possa essere eseguito.

   Per altre informazioni, vedere [usare l'emulatore di archiviazione di Azure per sviluppo e test](../storage/common/storage-use-emulator.md).

1. Prima di poter eseguire il progetto, assicurarsi di avviare l'emulatore.

   ![Screenshot che mostra l'emulatore di archiviazione di Azure in esecuzione.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS e Linux

Per compilare ed eseguire localmente il progetto di app per la logica in Visual Studio Code quando si usa macOS o Linux, seguire questa procedura per creare e configurare un account di archiviazione di Azure.

> [!NOTE]
> Attualmente, la finestra di progettazione in Visual Studio Code non funziona in un sistema operativo Linux, ma è comunque possibile eseguire la compilazione, l'esecuzione e la distribuzione di app per la logica che usano il runtime di anteprima delle app per la logica in macchine virtuali basate su Linux. Per il momento è possibile compilare le app per la logica in Visual Studio Code in Windows o macOS e quindi distribuirle in una macchina virtuale basata su Linux.

1. Accedere al [portale di Azure](https://portal.azure.com)e [creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal), che è un [prerequisito per funzioni di Azure](../azure-functions/storage-considerations.md).

1. Nel menu account di archiviazione selezionare **chiavi di accesso** in **Impostazioni**.

1. Nel riquadro **chiavi di accesso** individuare e copiare la stringa di connessione dell'account di archiviazione, che ha un aspetto simile all'esempio seguente:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Screenshot che mostra la portale di Azure con le chiavi di accesso dell'account di archiviazione e la stringa di connessione copiata.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Per altre informazioni, vedere [gestire le chiavi dell'account di archiviazione](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Salvare la stringa di connessione in un punto sicuro. Dopo aver creato il progetto di app per la logica in Visual Studio Code, è necessario aggiungere la stringa al **local.settings.js** nel file nella cartella del livello radice del progetto.

   > [!IMPORTANT]
   > Se si prevede di eseguire la distribuzione in un contenitore Docker, è necessario aggiungere anche questa stringa di connessione al file Docker usato per la distribuzione.

### <a name="tools"></a>Strumenti

* [Visual Studio Code 1.30.1 (gennaio 2019) o versione successiva](https://code.visualstudio.com/), che è gratuita. Inoltre, scaricare e installare questi strumenti per Visual Studio Code, se non sono già presenti:

  * [Estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), che fornisce un'unica esperienza comune di accesso di Azure e di filtro delle sottoscrizioni per tutte le altre estensioni di azure in Visual Studio Code.

  * [C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), che consente la funzionalità F5 per eseguire l'app per la logica.

  * [Azure Functions core tools 3.0.3245 o versioni successive](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) utilizzando la versione di Microsoft Installer (MSI), ovvero `func-cli-3.0.3245-x*.msi` .

    Questi strumenti includono una versione dello stesso runtime che alimenta il runtime di funzioni di Azure, usato dall'estensione di anteprima in Visual Studio Code.

    > [!IMPORTANT]
    > Se si dispone di un'installazione precedente a queste versioni, disinstallare prima la versione o assicurarsi che la variabile di ambiente PATH punti alla versione scaricata e installata.

  * [Estensione app per la logica di Azure (anteprima) per Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Questa estensione offre la possibilità di creare app per la logica in cui è possibile compilare flussi di lavoro con stato e senza stato che vengono eseguiti localmente in Visual Studio Code e quindi distribuire tali app per la logica direttamente in Azure o nei contenitori docker.

    Attualmente, è possibile installare l'estensione app per la logica di Azure originale e l'estensione di anteprima pubblica in Visual Studio Code. Sebbene le esperienze di sviluppo differiscano in alcuni modi tra le estensioni, la sottoscrizione di Azure può includere entrambi i tipi di app per la logica creati con le estensioni. Visual Studio Code Mostra tutte le app per la logica distribuite nella sottoscrizione di Azure, ma le organizza in sezioni diverse in base ai nomi di estensione, alle app per la **logica** e alle app per la **logica di Azure (anteprima)**.

    > [!IMPORTANT]
    > Se sono stati creati progetti di app per la logica con l'estensione di anteprima privata precedente, questi progetti non funzioneranno con l'estensione di anteprima pubblica. Tuttavia, è possibile eseguire la migrazione di questi progetti dopo la disinstallazione dell'estensione di anteprima privata, eliminare i file associati e installare l'estensione di anteprima pubblica. Si crea quindi un nuovo progetto in Visual Studio Code e si copia il file di **definizione del flusso di lavoro** dell'app per la logica creato in precedenza nel nuovo progetto. Per ulteriori informazioni, vedere [eseguire la migrazione dall'estensione di anteprima privata](#migrate-private-preview).
    > 
    > Se sono stati creati progetti di app per la logica con l'estensione di anteprima pubblica precedente, è possibile continuare a usare tali progetti senza passaggi di migrazione.

    **Per installare l'estensione app per la **logica di Azure (anteprima)** , seguire questa procedura:**

    1. In Visual Studio Code, sulla barra degli strumenti a sinistra, selezionare **estensioni**.

    1. Nella casella di ricerca estensioni immettere `azure logic apps preview` . Dall'elenco dei risultati selezionare app per la **logica di Azure (anteprima)** **>** .

       Al termine dell'installazione, l'estensione anteprima viene visualizzata nell'elenco **estensioni: installato** .

       ![Screenshot che mostra l'elenco delle estensioni installate di Visual Studio Code con l'estensione "app per la logica di Azure (anteprima)" sottolineata.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Se l'estensione non viene visualizzata nell'elenco installato, provare a riavviare Visual Studio Code.

* Per usare l' [azione operazioni di codice inline](../logic-apps/logic-apps-add-run-inline-code.md) che esegue JavaScript, installare [Node.js versioni 10. x.x. x, 11. x. x o 12. x.x.](https://nodejs.org/en/download/releases/)x.

  > [!TIP] 
  > Per Windows, scaricare la versione MSI. Se invece si usa la versione ZIP, è necessario rendere manualmente Node.js disponibili usando una variabile di ambiente PATH per il sistema operativo.

* Per eseguire localmente trigger e azioni basati su webhook, ad esempio il [trigger http webhook incorporato](../connectors/connectors-native-webhook.md), in Visual Studio Code, è necessario [configurare l'invio per l'URL callback](#webhook-setup).

* Per testare l'app per la logica di esempio creata in questo articolo, è necessario uno strumento che può inviare chiamate al trigger di richiesta, che è il primo passaggio nell'app per la logica di esempio. Se non si dispone di uno strumento di questo tipo, è possibile scaricare, installare e usare il [post](https://www.postman.com/downloads/).

* Se si crea l'app per la logica e la si distribuisce con impostazioni che supportano l'uso di [Application Insights](../azure-monitor/app/app-insights-overview.md), è possibile abilitare facoltativamente la registrazione e la traccia di diagnostica per l'app per la logica. Questa operazione può essere eseguita quando si distribuisce l'app per la logica da Visual Studio Code o dopo la distribuzione. È necessario disporre di un'istanza di Application Insights, ma è possibile creare questa risorsa [in anticipo](../azure-monitor/app/create-workspace-resource.md), quando si distribuisce l'app per la logica o dopo la distribuzione.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Eseguire la migrazione dall'estensione di anteprima privata

Tutti i progetti di app per la logica creati con l'estensione app per la **logica di Azure (anteprima privata)** non funzioneranno con l'estensione di anteprima pubblica. È tuttavia possibile eseguire la migrazione di questi progetti in nuovi progetti attenendosi alla procedura seguente:

1. Disinstallare l'estensione di anteprima privata.

1. Eliminare tutte le cartelle del bundle di estensione e del pacchetto NuGet associate nei percorsi seguenti:

   * La cartella **Microsoft. Azure. Functions. ExtensionBundle. Workflows** , che contiene i bundle di estensione precedenti e si trova lungo uno dei percorsi seguenti:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * La cartella **Microsoft. Azure. Workflows. webjobs. Extension** , che è la cache [NuGet](/nuget/what-is-nuget) per l'estensione di anteprima privata, si trova lungo questo percorso:

     `C:\Users\{userName}\.nuget\packages`

1. Installare l'estensione app per la **logica di Azure (anteprima)** .

1. Creare un nuovo progetto in Visual Studio Code.

1. Copiare il file di **definizione del flusso di lavoro** dell'app per la logica creato in precedenza nel nuovo progetto.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurare Visual Studio Code

1. Per assicurarsi che tutte le estensioni siano installate correttamente, ricaricare o riavviare Visual Studio Code.

1. Confermare che Visual Studio Code trova e installa automaticamente gli aggiornamenti delle estensioni in modo che l'estensione di anteprima ottenga gli aggiornamenti più recenti. In caso contrario, è necessario disinstallare manualmente la versione obsoleta e installare la versione più recente.

   1. Nel menu **file** passare a **Preferenze** **>** **Impostazioni**.

   1. Nella scheda **utente** passare a **funzionalità** **>** **estensioni**.

   1. Verificare che sia selezionata l'opzione **Controlla automaticamente aggiornamenti** e **aggiornamento automatico** .

Inoltre, per impostazione predefinita, le impostazioni seguenti sono abilitate e impostate per l'estensione di anteprima delle app per la logica:

* **App per la logica di Azure V2: runtime del progetto**, che è impostato sulla versione **~ 3**

  > [!NOTE]
  > Questa versione è necessaria per usare le [azioni operazioni di codice inline](../logic-apps/logic-apps-add-run-inline-code.md).

* **App per la logica di Azure V2: Gestione viste sperimentali**, che consente la finestra di progettazione più recente in Visual Studio Code. Se si verificano problemi nella finestra di progettazione, ad esempio il trascinamento e l'eliminazione di elementi, disattivare questa impostazione.

Per trovare e confermare queste impostazioni, seguire questa procedura:

1. Nel menu **file** passare a **Preferenze** **>** **Impostazioni**.

1. Nella scheda **utente** passare a **>** **estensioni** app per la **>** **logica di Azure (anteprima)**.

   Ad esempio, è possibile trovare le **app per la logica di Azure V2: Project Runtime** oppure usare la casella di ricerca per trovare altre impostazioni:

   ![Screenshot che mostra le impostazioni di Visual Studio Code per l'estensione "app per la logica di Azure (anteprima)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

1. Nella barra attività Visual Studio Code selezionare l'icona Azure.

   ![Screenshot che mostra Visual Studio Code barra attività e l'icona di Azure selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Nel riquadro di Azure, in **Azure: app per la logica (anteprima)**, selezionare **Accedi ad Azure**. Quando viene visualizzata la pagina autenticazione Visual Studio Code, accedere con l'account Azure.

   ![Screenshot che mostra il riquadro di Azure e il collegamento selezionato per l'accesso ad Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Dopo aver eseguito l'accesso, il riquadro Azure Mostra le sottoscrizioni nell'account Azure. Se si dispone anche dell'estensione rilasciata pubblicamente, è possibile trovare qualsiasi app per la logica creata con tale estensione nella sezione **app** per la logica, non nella sezione app per la logica **(anteprima)** .
   
   Se le sottoscrizioni previste non vengono visualizzate o se si desidera che il riquadro visualizzi solo sottoscrizioni specifiche, attenersi alla seguente procedura:

   1. Nell'elenco sottoscrizioni spostare il puntatore accanto alla prima sottoscrizione fino a quando non viene visualizzato il pulsante **Seleziona sottoscrizioni** (icona filtro). Selezionare l'icona del filtro.

      ![Screenshot che mostra il riquadro di Azure e l'icona del filtro selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      In alternativa, nella barra di stato Visual Studio Code selezionare l'account Azure. 

   1. Quando viene visualizzato un elenco di sottoscrizioni, selezionare le sottoscrizioni desiderate e quindi assicurarsi di selezionare **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Creazione di un progetto locale

Prima di poter creare l'app per la logica, creare un progetto locale in modo da poter gestire, eseguire e distribuire l'app per la logica da Visual Studio Code. Il progetto sottostante è simile a un progetto di funzioni di Azure, noto anche come progetto di app per le funzioni. Tuttavia, questi tipi di progetto sono separati l'uno dall'altro, quindi le app per la logica e le app per le funzioni non possono esistere nello stesso progetto.

1. Nel computer creare una cartella locale *vuota* da usare per il progetto che verrà creato in un secondo momento in Visual Studio Code.

1. In Visual Studio Code chiudere tutte le cartelle aperte.

1. Nel riquadro di Azure, accanto ad **Azure: app per la logica (anteprima)**, selezionare **Crea nuovo progetto** (icona che mostra una cartella e un fulmine).

   ![Screenshot che mostra la barra degli strumenti del riquadro di Azure con l'opzione "Crea nuovo progetto" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Se Windows Defender Firewall richiede di concedere l'accesso alla rete per `Code.exe` , che è Visual Studio Code e per `func.exe` , che è il Azure Functions Core Tools, selezionare **reti private, ad esempio la rete domestica o di lavoro** **>** **Consenti accesso**.

1. Passare al percorso in cui è stata creata la cartella del progetto, selezionare la cartella e continuare.

   ![Screenshot che mostra la finestra di dialogo "Seleziona cartella" con una cartella di progetto appena creata e il pulsante "Seleziona" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Dall'elenco dei modelli visualizzato, selezionare flusso di **lavoro con stato** o **flusso di lavoro** senza stato. Questo esempio Mostra come selezionare un **flusso di lavoro con stato**.

   ![Screenshot che mostra l'elenco dei modelli di flusso di lavoro con "flusso di lavoro con stato" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Specificare un nome per il flusso di lavoro e premere INVIO. In questo esempio viene usato `Fabrikam-Stateful-Workflow` come nome.

   ![Screenshot che mostra il nome del flusso di lavoro "Crea nuovo flusso di lavoro con stato (3/4)" e "Fabrikam-con stato-flusso di lavoro".](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code termina la creazione del progetto e apre il **workflow.jssul** file per il flusso di lavoro nell'editor di codice.

   > [!NOTE]
   > Se viene richiesto di selezionare la modalità di apertura del progetto, selezionare **Apri nella finestra corrente** se si desidera aprire il progetto nella finestra Visual Studio Code corrente. Per aprire una nuova istanza per Visual Studio Code, selezionare **Apri in una nuova finestra**.

1. Dalla barra degli strumenti di Visual Studio aprire il riquadro di esplorazione, se non è già aperto.

   Il riquadro di esplorazione Mostra il progetto, che ora include i file di progetto generati automaticamente. Il progetto, ad esempio, contiene una cartella che mostra il nome del flusso di lavoro. All'interno di questa cartella, il **workflow.jsnel** file contiene la definizione JSON sottostante del flusso di lavoro.

   ![Screenshot che mostra il riquadro di esplorazione con la cartella del progetto, la cartella del flusso di lavoro e il file "workflow.json".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Se si usa macOS o Linux, configurare l'accesso all'account di archiviazione attenendosi alla procedura seguente, che è necessario per eseguire localmente il progetto:

   1. Nella cartella radice del progetto aprire il **local.settings.jssu** file.

      ![Screenshot che mostra il riquadro di esplorazione e il file "local.settings.json" nel progetto.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Sostituire il `AzureWebJobsStorage` valore della proprietà con la stringa di connessione dell'account di archiviazione salvata in precedenza, ad esempio:

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

   1. Al termine, assicurarsi di salvare le modifiche.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Abilitare la creazione di connettori incorporati

È possibile creare connettori predefiniti per tutti i servizi necessari usando il [Framework di estendibilità della versione di anteprima](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Analogamente ai connettori incorporati, ad esempio il bus di servizio di Azure e SQL Server, questi connettori forniscono velocità effettiva più elevata, bassa latenza, connettività locale ed esecuzione nativa nello stesso processo del runtime di anteprima.

La funzionalità di creazione è attualmente disponibile solo in Visual Studio Code, ma non è abilitata per impostazione predefinita. Per creare questi connettori, è necessario prima convertire il progetto da estensione basata su Bundle (Node.js) a NuGet basato su pacchetti (.NET).

> [!IMPORTANT]
> Questa azione è un'operazione unidirezionale che non è possibile annullare.

1. Nel riquadro di esplorazione, alla radice del progetto, spostare il puntatore del mouse su un'area vuota sotto tutti gli altri file e cartelle, aprire il menu di scelta rapida e selezionare **Converti in progetto di app per la logica basata su NuGet**.

   ![Screenshot che mostra il riquadro di esplorazione con il menu di scelta rapida del progetto aperto da un'area vuota nella finestra del progetto.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Quando viene visualizzato il prompt, confermare la conversione del progetto.

1. Per continuare, rivedere e seguire i passaggi descritti nell'articolo app per la [logica di Azure che eseguono l'estensibilità dei connettori incorporati in qualsiasi](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)posizione.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Aprire il file di definizione del flusso di lavoro nella finestra di progettazione

1. Controllare le versioni installate nel computer eseguendo questo comando:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Se si dispone di .NET Core SDK 5. x, questa versione potrebbe impedire l'apertura della definizione del flusso di lavoro sottostante dell'app per la logica nella finestra di progettazione. Anziché disinstallare questa versione, nella cartella radice del progetto creare un **global.jsin** un file che faccia riferimento alla versione 3. x del runtime di .NET Core che è successiva a 3.1.201, ad esempio:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Assicurarsi di aggiungere in modo esplicito il **global.js** nel file nella cartella radice del progetto dall'interno Visual Studio Code. In caso contrario, la finestra di progettazione non verrà aperta.

1. Espandere la cartella del progetto per il flusso di lavoro. Aprire il **workflow.js** dal menu di scelta rapida del file e selezionare **Apri in finestra di progettazione**.

   ![Screenshot che mostra il riquadro di esplorazione e la finestra di scelta rapida per il workflow.jsnel file con l'opzione "Apri in progettazione" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Dall'elenco **Abilita connettori in Azure** selezionare **usa connettori di Azure**, che si applica a tutti i connettori gestiti disponibili e distribuiti in Azure, non solo ai connettori per i servizi di Azure.

   ![Screenshot che mostra il riquadro di esplorazione con l'elenco "Abilita connettori in Azure" aperto e "usa connettori da Azure" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > I flussi di lavoro senza stato attualmente supportano solo *azioni* per i [connettori gestiti](../connectors/apis-list.md#managed-api-connectors), distribuiti in Azure e non trigger. Sebbene sia possibile abilitare i connettori in Azure per il flusso di lavoro senza stato, nella finestra di progettazione non vengono visualizzati i trigger del connettore gestito da selezionare.

1. Dall'elenco **Seleziona sottoscrizione** selezionare la sottoscrizione di Azure da usare per il progetto di app per la logica.

   ![Screenshot che mostra il riquadro di esplorazione con la casella "Seleziona sottoscrizione" e la sottoscrizione selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Dall'elenco gruppi di risorse selezionare **Crea nuovo gruppo di risorse**.

   ![Screenshot che mostra il riquadro di esplorazione con i gruppi di risorse e l'elenco "Crea nuovo gruppo di risorse" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Specificare un nome per il gruppo di risorse e premere INVIO. In questo esempio viene utilizzato `Fabrikam-Workflows-RG`.

   ![Screenshot che mostra il riquadro di esplorazione e il nome del gruppo di risorse.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Dall'elenco percorsi trovare e selezionare l'area di Azure da usare durante la creazione del gruppo di risorse e delle risorse. Questo esempio usa **Stati Uniti centro-occidentali**.

   ![Screenshot che mostra il riquadro di esplorazione con l'elenco delle posizioni e la sezione "Stati Uniti centro-occidentali" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Dopo aver eseguito questo passaggio, Visual Studio Code apre la finestra di progettazione del flusso di lavoro.

   > [!NOTE]
   > Quando Visual Studio Code avvia l'API della fase di progettazione del flusso di lavoro, è possibile che venga ricevuto un messaggio che l'avvio potrebbe richiedere alcuni secondi. È possibile ignorare questo messaggio oppure fare clic su **OK**.
   >
   > Se la finestra di progettazione non viene aperta, rivedere la sezione risoluzione dei problemi. [Impossibile aprire Progettazione](#designer-fails-to-open).

   Quando viene visualizzata la finestra di progettazione, il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione ed è selezionato per impostazione predefinita, che mostra il riquadro **Aggiungi un'azione** .

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Aggiungere quindi [un trigger e le azioni](#add-trigger-actions) al flusso di lavoro.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Aggiungere un trigger e le azioni

Dopo aver aperto la finestra di progettazione, il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione ed è selezionato per impostazione predefinita. È ora possibile iniziare a creare il flusso di lavoro aggiungendo un trigger e le azioni.

Il flusso di lavoro in questo esempio usa questo trigger e le azioni seguenti:

* [Trigger di richiesta](../connectors/connectors-native-reqres.md)incorporato, **quando viene ricevuta una richiesta http**, che riceve le chiamate in ingresso o le richieste e crea un endpoint che può essere chiamato da altri servizi o app per la logica.

* L' [azione Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), **Invia un messaggio di posta elettronica**.

* [Azione di risposta](../connectors/connectors-native-reqres.md)predefinita, usata per inviare una risposta e restituire i dati al chiamante.

### <a name="add-the-request-trigger"></a>Aggiungere il trigger Request

1. Accanto alla finestra di progettazione, nel riquadro **Aggiungi un trigger** , nella casella di ricerca **scegliere un'operazione** verificare che sia selezionata l'opzione **predefinito** , in modo che sia possibile selezionare un trigger che viene eseguito in modalità nativa.

1. Nella casella di ricerca **scegliere un'operazione** immettere `when a http request` e selezionare il trigger di richiesta incorporato denominato **quando viene ricevuta una richiesta http**.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e il * * Aggiungi un riquadro Trigger * * con il trigger "quando viene ricevuta una richiesta HTTP" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Quando il trigger viene visualizzato nella finestra di progettazione, il riquadro dei dettagli del trigger si apre per visualizzare le proprietà, le impostazioni e altre azioni del trigger.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro con il trigger "quando viene ricevuta una richiesta HTTP" selezionato e il riquadro dei dettagli del trigger aperto.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se il riquadro dei dettagli non viene visualizzato, verifica che il trigger sia selezionato nella finestra di progettazione.

1. Se è necessario eliminare un elemento dalla finestra di progettazione, [attenersi alla procedura seguente per eliminare elementi dalla finestra di progettazione](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Aggiungere l'azione Office 365 Outlook

1. Nella finestra di progettazione, sotto il trigger aggiunto, selezionare **nuovo passaggio**.

   Il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione e il riquadro **Aggiungi un'azione** viene riaperto in modo che sia possibile selezionare l'azione successiva.

1. Nella casella di ricerca **scegliere un'operazione** del riquadro **Aggiungi un'azione** selezionare **Azure** per poter trovare e selezionare un'azione per un connettore gestito distribuito in Azure.

   Questo esempio seleziona e usa l'azione Office 365 Outlook, **Invia un messaggio di posta elettronica (v2)**.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e il riquadro * * Aggiungi un'azione * * con l'azione di Office 365 Outlook "Invia un messaggio di posta elettronica" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Nel riquadro dei dettagli dell'azione selezionare **Accedi** per poter creare una connessione all'account di posta elettronica.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e il riquadro * * Invia un messaggio di posta elettronica (v2) * * con l'opzione "Accedi".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Quando Visual Studio Code richiede il consenso per l'accesso all'account di posta elettronica, selezionare **Apri**.

   ![Screenshot che mostra la richiesta di Visual Studio Code per consentire l'accesso.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Per evitare richieste future, selezionare **Configura domini trusted** in modo che sia possibile aggiungere la pagina di autenticazione come dominio attendibile.

1. Seguire i prompt successivi per accedere, consentire l'accesso e consentire il ritorno a Visual Studio Code.

   > [!NOTE]
   > Se passano troppo tempo prima di completare le richieste, il processo di autenticazione si interrompe e non riesce. In questo caso, tornare alla finestra di progettazione e riprovare ad accedere per creare la connessione.

1. Quando l'estensione app per la logica di Azure (anteprima) richiede il consenso per l'accesso all'account di posta elettronica, selezionare **Apri**. Seguire la richiesta successiva per consentire l'accesso.

   ![Screenshot che mostra la richiesta di estensione di anteprima per consentire l'accesso.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Per evitare richieste future, selezionare **non visualizzare più questa estensione**.

   Dopo che Visual Studio Code crea la connessione, alcuni connettori visualizzano il messaggio `The connection will be valid for {n} days only` . Questo limite di tempo si applica solo alla durata della creazione dell'app per la logica in Visual Studio Code. Dopo la distribuzione, questo limite non si applica più perché l'app per la logica è in grado di eseguire l'autenticazione in fase di esecuzione usando l' [identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md)abilitata automaticamente. Questa identità gestita è diversa dalle credenziali di autenticazione o dalla stringa di connessione utilizzata durante la creazione di una connessione. Se si disabilita questa identità gestita assegnata dal sistema, le connessioni non funzioneranno in fase di esecuzione.

1. Nella finestra di progettazione, se l'azione **Invia un messaggio di posta elettronica** non è selezionata, selezionare l'azione.

1. Nel riquadro dei dettagli dell'azione, nella scheda **parametri** , fornire le informazioni necessarie per l'azione, ad esempio:

   ![Screenshot che mostra progettazione flussi di lavoro con i dettagli per l'azione "Invia un messaggio di posta elettronica" di Office 365 Outlook.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Destinatario del messaggio di posta elettronica, che può essere l'indirizzo di posta elettronica a scopo di test. Questo esempio usa il messaggio di posta elettronica fittizio, `sophiaowen@fabrikam.com` . |
   | **Oggetto** | Sì | `An email from your example workflow` | L'oggetto del messaggio di posta elettronica |
   | **Corpo** | Sì | `Hello from your example workflow!` | Contenuto del corpo del messaggio di posta elettronica |
   ||||

   > [!NOTE]
   > Se si desidera apportare modifiche nel riquadro dettagli nella scheda **Impostazioni**, **risultato statico** o **Esegui dopo** , assicurarsi di selezionare **completato** per eseguire il commit delle modifiche prima di cambiare le schede o di spostare lo stato attivo sulla finestra di progettazione. In caso contrario, Visual Studio Code non manterrà le modifiche. Per altre informazioni, vedere la pagina relativa alle app per la [logica Public Preview problemi noti in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. Nella finestra di progettazione selezionare **Salva**.

> [!IMPORTANT]
> Per eseguire localmente un flusso di lavoro che usa un trigger o azioni basate su un webhook, ad esempio il [trigger o l'azione del webhook http incorporato](../connectors/connectors-native-webhook.md), è necessario abilitare questa funzionalità impostando [l'invio per l'URL di callback del webhook](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Abilita webhook in esecuzione localmente

Quando si usa un trigger o un'azione basata su webhook, ad esempio un **webhook http**, con un'app per la logica in esecuzione in Azure, il runtime di app per la logica sottoscrive l'endpoint del servizio generando e registrando un URL di callback con tale endpoint. Il trigger o l'azione attende quindi che l'endpoint del servizio chiami l'URL. Tuttavia, quando si lavora in Visual Studio Code, l'URL di callback generato inizia con `http://localhost:7071/...` . Questo URL è per il server localhost, che è privato, in modo che l'endpoint del servizio non possa chiamare questo URL.

Per eseguire localmente trigger e azioni basati su webhook in Visual Studio Code, è necessario configurare un URL pubblico che espone il server localhost e che inoltri in modo sicuro le chiamate dall'endpoint di servizio all'URL di callback del webhook. È possibile usare uno strumento e un servizio di provisioning, ad esempio [**ngrok**](https://ngrok.com/), che apre un tunnel HTTP alla porta localhost oppure è possibile usare uno strumento personalizzato.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Configurare l'invio delle chiamate mediante **ngrok**

1. Se non si dispone [di un account **Ngrok** , iscriversi per ottenere un account](https://dashboard.ngrok.com/signup) . In caso contrario, [accedere al proprio account](https://dashboard.ngrok.com/login).

1. Ottenere il token di autenticazione personale, che il client **ngrok** deve connettere e autenticare l'accesso all'account.

   1. Per trovare la [pagina token di autenticazione](https://dashboard.ngrok.com/auth/your-authtoken), nel menu del dashboard dell'account, espandere **autenticazione** e selezionare **il authToken**.

   1. Dalla casella **authToken** copiare il token in una posizione sicura.

1. Dalla pagina di download di [ **ngrok**](https://ngrok.com/download) o dal [Dashboard dell'account](https://dashboard.ngrok.com/get-started/setup), scaricare la versione di **ngrok** desiderata ed estrarre il file zip. Per ulteriori informazioni, vedere [passaggio 1: decomprimere per l'installazione](https://ngrok.com/download).

1. Nel computer aprire lo strumento del prompt dei comandi. Passare al percorso in cui si trova il file di **ngrok.exe** .

1. Connettere il client di **ngrok** all'account **ngrok** eseguendo il comando seguente. Per altre informazioni, vedere [passaggio 2: connettere l'account](https://ngrok.com/download).

   `ngrok authtoken <your_auth_token>`

1. Aprire il tunnel HTTP sulla porta localhost 7071 eseguendo il comando seguente. Per altre informazioni, vedere [passaggio 3: avviare il](https://ngrok.com/download)trigger.

   `ngrok http 7071`

1. Dall'output trovare la riga seguente:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Copiare e salvare l'URL con il formato seguente: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Configurare l'URL di invio nelle impostazioni dell'app

1. In Visual Studio Code, nella finestra di progettazione, aggiungere il trigger o l'azione **http + webhook** .

1. Quando viene visualizzata la richiesta per il percorso dell'endpoint host, immettere l'URL di inoltri (Reindirizzamento) creato in precedenza.

   > [!NOTE]
   > Se si ignora la richiesta, viene visualizzato un avviso che indica che è necessario fornire l'URL di invio, quindi selezionare **Configura** e immettere l'URL. Al termine di questo passaggio, la richiesta non verrà visualizzata nuovamente per i trigger di Webhook successivi o per le azioni che è possibile aggiungere.
   >
   > Per visualizzare nuovamente il prompt, al livello radice del progetto aprire il **local.settings.js** dal menu di scelta rapida del file e selezionare **Configura endpoint di reindirizzamento del webhook**. Il prompt viene visualizzato in modo da poter fornire l'URL di invio.

   Visual Studio Code aggiunge l'URL di invio al **local.settings.js** nel file nella cartella radice del progetto. Nell' `Values` oggetto, la proprietà denominata `Workflows.WebhookRedirectHostUri` ora viene visualizzata e viene impostata sull'URL di invio, ad esempio:
   
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

La prima volta che si avvia una sessione di debug locale o si esegue il flusso di lavoro senza debug, il runtime di app per la logica registra il flusso di lavoro con l'endpoint del servizio e sottoscrive tale endpoint per la notifica delle operazioni del webhook. Alla successiva esecuzione del flusso di lavoro, il runtime non verrà registrato o sottoposto a nuova sottoscrizione perché la registrazione della sottoscrizione esiste già nell'archivio locale.

Quando si arresta la sessione di debug per un'esecuzione del flusso di lavoro che usa trigger o azioni basate su webhook di esecuzione localmente, le registrazioni di sottoscrizioni esistenti non vengono eliminate. Per annullare la registrazione, è necessario rimuovere o eliminare manualmente le registrazioni della sottoscrizione.

> [!NOTE]
> Dopo l'avvio dell'esecuzione del flusso di lavoro, la finestra del terminale potrebbe visualizzare errori simili a questo esempio:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> In questo caso, aprire il **local.settings.js** nel file nella cartella radice del progetto e verificare che la proprietà sia impostata su `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Gestione dei punti di interruzione per il debug

Prima di eseguire e testare il flusso di lavoro dell'app per la logica avviando una sessione di debug, è possibile impostare punti di [interruzione](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) all'interno del **workflow.jssu** file per ogni flusso di lavoro. Non sono necessarie altre configurazioni. 

Al momento, i punti di interruzione sono supportati solo per le azioni, non per i trigger. Ogni definizione di azione presenta questi punti di interruzione:

* Impostare il punto di interruzione iniziale sulla riga che mostra il nome dell'azione. Quando il punto di interruzione viene raggiunto durante la sessione di debug, è possibile esaminare gli input dell'azione prima che vengano valutati.

* Impostare il punto di interruzione finale sulla riga che mostra la parentesi graffa di chiusura (**}**) dell'azione. Quando il punto di interruzione viene raggiunto durante la sessione di debug, è possibile esaminare i risultati dell'azione prima del completamento dell'esecuzione dell'azione.

Per aggiungere un punto di interruzione, attenersi alla procedura seguente:

1. Aprire il **workflow.jsnel** file per il flusso di lavoro di cui si desidera eseguire il debug.

1. Nella colonna sinistra della riga in cui si vuole impostare il punto di interruzione selezionare all'interno della colonna. Per rimuovere il punto di interruzione, selezionare il punto di interruzione.

   Quando si avvia la sessione di debug, la visualizzazione di esecuzione viene visualizzata sul lato sinistro della finestra del codice, mentre la barra degli strumenti di debug viene visualizzata nella parte superiore.

   > [!NOTE]
   > Se la visualizzazione esecuzione non viene visualizzata automaticamente, premere CTRL + MAIUSC + D.

1. Per esaminare le informazioni disponibili quando viene raggiunto un punto di interruzione, esaminare il riquadro **variabili** nella visualizzazione esecuzione.

1. Per continuare l'esecuzione del flusso di lavoro, sulla barra degli strumenti Debug selezionare **continua** (pulsante Riproduci).

È possibile aggiungere e rimuovere punti di interruzione in qualsiasi momento durante l'esecuzione del flusso di lavoro. Tuttavia, se si aggiorna il **workflow.jsnel** file dopo l'avvio dell'esecuzione, i punti di interruzione non vengono aggiornati automaticamente. Per aggiornare i punti di interruzione, riavviare l'app per la logica.

Per informazioni generali, vedere punti di [interruzione-Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Eseguire, testare ed eseguire il debug in locale

Per testare l'app per la logica, seguire questa procedura per avviare una sessione di debug e trovare l'URL per l'endpoint creato dal trigger di richiesta. Questo URL è necessario per poter inviare una richiesta a tale endpoint in un secondo momento.

1. Per eseguire il debug di un flusso di lavoro senza stato più facilmente, è possibile [abilitare la cronologia di esecuzione per tale flusso di lavoro](#enable-run-history-stateless).

1. Nella barra attività Visual Studio Code aprire il menu **Run (Esegui** ) e selezionare **Avvia debug** (F5).

   Viene visualizzata la finestra del **terminale** in cui è possibile esaminare la sessione di debug.

   > [!NOTE]
   > Se viene visualizzato l'errore **"errore esistente dopo l'esecuzione di preavvio ' generateDebugSymbols '"**, vedere la sezione risoluzione dei problemi. [Impossibile avviare la sessione di debug](#debugging-fails-to-start).

1. A questo punto, trovare l'URL di callback per l'endpoint nel trigger della richiesta.

   1. Riaprire il riquadro di esplorazione per poter visualizzare il progetto.

   1. Scegliere **Panoramica** dal menu di scelta rapida del file di **workflow.js** .

      ![Screenshot che mostra il riquadro di esplorazione e la finestra di scelta rapida per il workflow.jssu file con "Overview" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Trovare il valore di **URL callback** , simile a questo URL per il trigger di richiesta di esempio:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Screenshot che mostra la pagina Panoramica del flusso di lavoro con l'URL callback](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Per testare l'URL di callback attivando il flusso di lavoro dell'app per la logica, aprire il [post](https://www.postman.com/downloads/) o lo strumento preferito per la creazione e l'invio di richieste.

   Questo esempio continua con l'uso di postazione. Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione dei post](https://learning.postman.com/docs/getting-started/introduction/).

   1. Sulla barra degli strumenti del posto, selezionare **nuovo**.

      ![Screenshot che mostra il pulsante con il pulsante nuovo selezionato](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Nel riquadro **Crea nuovo** , in **blocchi predefiniti**, selezionare **richiesta**.

   1. Nella finestra **Salva richiesta** , in **nome richiesta**, specificare un nome per la richiesta, ad esempio `Test workflow trigger` .

   1. In **selezionare una raccolta o una cartella in cui salvare**, selezionare **Crea raccolta**.

   1. In **tutte le raccolte** specificare un nome per la raccolta da creare per organizzare le richieste, premere invio e selezionare **Salva per <*raccolta-nome* >**. In questo esempio viene usato `Logic Apps requests` come nome della raccolta.

      Viene visualizzato il riquadro delle richieste del post, in modo che sia possibile inviare una richiesta all'URL di callback per il trigger di richiesta.

      ![Screenshot che mostra il posto con il riquadro delle richieste aperto](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Tornare a Visual Studio Code. dalla pagina Panoramica del flusso di lavoro, copiare il valore della proprietà **URL callback** .

   1. Tornare a postazione. Nel riquadro delle richieste, accanto all'elenco dei metodi, che attualmente Mostra **Get** come metodo di richiesta predefinito, incollare l'URL di callback copiato in precedenza nella casella Address e selezionare **Send (Invia**).

      ![Screenshot che mostra l'URL dei post e callback nella casella indirizzo con il pulsante Invia selezionato](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Il flusso di lavoro dell'app per la logica di esempio Invia un messaggio di posta elettronica simile all'esempio seguente:

      ![Screenshot che mostra la posta elettronica di Outlook come descritto nell'esempio](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. In Visual Studio Code tornare alla pagina Panoramica del flusso di lavoro.

   Se è stato creato un flusso di lavoro con stato, dopo la richiesta inviata viene attivato il flusso di lavoro, nella pagina panoramica vengono visualizzati lo stato e la cronologia dell'esecuzione del flusso di lavoro.

   > [!TIP]
   > Se lo stato dell'esecuzione non viene visualizzato, provare ad aggiornare la pagina Panoramica selezionando **Aggiorna**. Non viene eseguita alcuna esecuzione per un trigger ignorato a causa di criteri non soddisfatti o per la ricerca di dati.

   ![Screenshot che mostra la pagina Panoramica del flusso di lavoro con lo stato e la cronologia di esecuzione](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Stato esecuzione | Descrizione |
   |------------|-------------|
   | **Aborted** | L'esecuzione è stata arrestata o non è stata completata a causa di problemi esterni, ad esempio un'interruzione del sistema o una sottoscrizione di Azure scaduta. |
   | **Annullato** | L'esecuzione è stata attivata e avviata ma è stata ricevuta una richiesta di annullamento. |
   | **Operazione non riuscita** | Almeno un'azione nell'esecuzione non è riuscita. Non è stata configurata alcuna azione successiva nel flusso di lavoro per gestire l'errore. |
   | **Running** | L'esecuzione è stata attivata ed è in corso, ma questo stato può anche essere visualizzato per un'esecuzione limitata a causa di [limiti di azione](logic-apps-limits-and-config.md) o del [piano tariffario corrente](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Suggerimento**: se si configura la [registrazione diagnostica](monitor-logic-apps-log-analytics.md), è possibile ottenere informazioni sugli eventi di limitazione che si verificano. |
   | **Completato** | Esecuzione completata. Se un'azione ha esito negativo, un'azione successiva nel flusso di lavoro ha gestito l'errore. |
   | **Timeout** | Si è verificato il timeout dell'esecuzione perché la durata corrente supera il limite di durata dell'esecuzione, che è controllato dall' [impostazione **conservazione cronologia di esecuzione in giorni**](logic-apps-limits-and-config.md#run-duration-retention-limits). La durata di un'esecuzione viene calcolata usando l'ora di inizio e il limite di durata dell'esecuzione all'ora di inizio. <p><p>**Nota**: se la durata dell'esecuzione supera anche il *limite di conservazione della cronologia di esecuzione* corrente, che è anche controllato dall' [impostazione **conservazione cronologia di esecuzione in giorni**](logic-apps-limits-and-config.md#run-duration-retention-limits), l'esecuzione viene cancellata dalla cronologia esecuzioni da un processo di pulizia giornaliero. Se l'esecuzione scade o viene completata, il periodo di memorizzazione viene sempre calcolato usando l'ora di inizio e il limite di conservazione *corrente* dell'esecuzione. Quindi, se si riduce il limite di durata per un'esecuzione in corso, si verifica il timeout dell'esecuzione. Tuttavia, l'esecuzione rimane o viene cancellata dalla cronologia delle esecuzioni a seconda che la durata dell'esecuzione superi il limite di conservazione. |
   | **Attesa** | L'esecuzione non è stata avviata o è stata sospesa, ad esempio, a causa di un'istanza del flusso di lavoro precedente ancora in esecuzione. |
   |||

1. Per esaminare gli Stati per ogni passaggio in un'esecuzione specifica e gli input e gli output del passaggio, selezionare il pulsante con i puntini di sospensione (**..**.) per l'esecuzione e selezionare **Mostra esecuzione**.

   ![Screenshot che mostra la riga della cronologia di esecuzione del flusso di lavoro con i puntini di sospensione e la selezione dell'opzione "Mostra esecuzione"](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code apre la visualizzazione monitoraggio e Mostra lo stato di ogni passaggio nell'esecuzione.

   ![Screenshot che mostra ogni passaggio nell'esecuzione del flusso di lavoro e il relativo stato](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Se un'esecuzione non è riuscita e un passaggio nella visualizzazione monitoraggio Mostra l' `400 Bad Request` errore, questo problema potrebbe essere causato da un nome di trigger o un nome di azione più lungo che causa il superamento del limite di caratteri predefinito per l'URI (Uniform Resource Identifier) sottostante. Per ulteriori informazioni, vedere ["400 richiesta non valida"](#400-bad-request).

   Di seguito sono riportati gli stati possibili che ogni passaggio del flusso di lavoro può avere:

   | Stato azione | Icona | Descrizione |
   |---------------|------|-------------|
   | **Aborted** | ![Icona per lo stato dell'azione "interrotto"][aborted-icon] | L'azione è stata interrotta o non è stata completata a causa di problemi esterni, ad esempio un'interruzione del sistema o una sottoscrizione di Azure scaduta. |
   | **Annullato** | ![Icona per lo stato dell'azione "annullato"][cancelled-icon] | L'azione è stata eseguita ma è stata ricevuta una richiesta di annullamento. |
   | **Operazione non riuscita** | ![Icona per lo stato dell'azione "non riuscito"][failed-icon] | L'azione non è riuscita. |
   | **Running** | ![Icona per lo stato dell'azione "in esecuzione"][running-icon] | L'azione è attualmente in esecuzione. |
   | **Ignorato** | ![Icona per lo stato dell'azione "ignorato"][skipped-icon] | L'azione è stata ignorata perché l'azione immediatamente precedente non è riuscita. Un'azione ha una `runAfter` condizione che richiede che l'azione precedente venga completata correttamente prima di poter eseguire l'azione corrente. |
   | **Completato** | ![Icona per lo stato dell'azione "succeeded"][succeeded-icon] | L'azione è riuscita. |
   | **Operazione completata con nuovi tentativi** | ![Icona per lo stato dell'azione "riuscito con ripetizione dei tentativi"][succeeded-with-retries-icon] | L'azione è stata completata ma solo dopo uno o più tentativi. Per esaminare la cronologia dei tentativi, nella visualizzazione dei dettagli della cronologia di esecuzione selezionare l'azione in modo che sia possibile visualizzare gli input e gli output. |
   | **Timeout** | ![Icona per lo stato dell'azione "timeout"][timed-out-icon] | L'azione è stata interrotta a causa del limite di timeout specificato dalle impostazioni di tale azione. |
   | **Attesa** | ![Icona per lo stato dell'azione "in attesa"][waiting-icon] | Si applica a un'azione webhook che è in attesa di una richiesta in ingresso da un chiamante. |
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

1. Per esaminare gli input e gli output per ogni passaggio, selezionare il passaggio che si desidera controllare.

   ![Screenshot che mostra lo stato di ogni passaggio nel flusso di lavoro più gli input e gli output nell'azione "Invia un messaggio di posta elettronica" espanso](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Per esaminare ulteriormente gli input e gli output non elaborati per tale passaggio, selezionare **Mostra input non elaborati** o **Mostra output** non elaborati.

1. Per arrestare la sessione di debug, scegliere **Interrompi debug** (MAIUSC + F5) dal menu **Esegui** .

<a name="return-response"></a>

## <a name="return-a-response"></a>Restituisce una risposta

Per restituire una risposta al chiamante che ha inviato una richiesta all'app per la logica, è possibile usare l'azione di [risposta](../connectors/connectors-native-reqres.md) predefinita per un flusso di lavoro che inizia con il trigger di richiesta.

1. Nella finestra di progettazione del flusso di lavoro, sotto l'azione **Invia un messaggio di posta elettronica** , selezionare **nuovo passaggio**.

   Il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione e il **riquadro Aggiungi un'azione** viene riaperto in modo che sia possibile selezionare l'azione successiva.

1. Nella casella di ricerca **scegliere un'azione** del riquadro **Aggiungi un'azione** verificare che sia selezionata l'opzione **predefinita** . Nella casella di ricerca immettere `response` e selezionare l'azione **risposta** .

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro con l'azione di risposta selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Quando l'azione **risposta** viene visualizzata nella finestra di progettazione, viene automaticamente aperto il riquadro dettagli dell'azione.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro con il riquadro dei dettagli dell'azione "risposta" aperta e la proprietà "Body" impostata sul valore della proprietà "Body" dell'azione "Invia un messaggio di posta elettronica".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Nella scheda **parametri** fornire le informazioni necessarie per la funzione che si desidera chiamare.

   Questo esempio restituisce il valore della proprietà **Body** che è l'output dell'azione **Invia un messaggio di posta elettronica** .

   1. Fare clic all'interno della casella proprietà **corpo** per visualizzare l'elenco di contenuto dinamico e visualizzare i valori di output disponibili dal trigger precedente e le azioni nel flusso di lavoro.

      ![Screenshot che mostra il riquadro dei dettagli dell'azione "risposta" con il puntatore del mouse all'interno della proprietà "Body", in modo che venga visualizzato l'elenco di contenuto dinamico.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Nell'elenco contenuto dinamico, in **Invia un messaggio di posta elettronica**, selezionare **corpo**.

      ![Screenshot che mostra l'elenco di contenuto dinamico aperto. Nell'elenco, sotto l'intestazione "Invia un messaggio di posta elettronica", viene selezionato il valore di output "Body".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Al termine, la proprietà **Body** dell'azione di risposta è ora impostata sul valore di output del **corpo** dell'azione **Invia un messaggio di posta elettronica** .

      ![Screenshot che mostra lo stato di ogni passaggio nel flusso di lavoro più gli input e gli output nell'azione di risposta espansa.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Nella finestra di progettazione selezionare **Salva**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Eseguire nuovamente il test dell'app per la logica

Dopo aver apportato gli aggiornamenti all'app per la logica, è possibile eseguire un altro test eseguendo nuovamente il debugger in Visual Studio e inviando un'altra richiesta di attivazione dell'app per la logica aggiornata, in modo analogo ai passaggi in [eseguire, testare ed](#run-test-debug-locally)eseguire il debug in locale.

1. Nella barra attività Visual Studio Code aprire il menu **Run (Esegui** ) e selezionare **Avvia debug** (F5).

1. Nel post o nello strumento per la creazione e l'invio di richieste inviare un'altra richiesta di attivazione del flusso di lavoro.

1. Se è stato creato un flusso di lavoro con stato, nella pagina Panoramica del flusso di lavoro controllare lo stato dell'esecuzione più recente. Per visualizzare lo stato, gli input e gli output per ogni passaggio di tale esecuzione, selezionare il pulsante con i puntini di sospensione (**.**..) per l'esecuzione e selezionare **Mostra esecuzione**.

   Ad esempio, di seguito è riportato lo stato dettagliato di un'esecuzione dopo che il flusso di lavoro di esempio è stato aggiornato con l'azione di risposta.

   ![Screenshot che mostra lo stato di ogni passaggio nel flusso di lavoro aggiornato più gli input e gli output nell'azione di risposta espansa.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Per arrestare la sessione di debug, scegliere **Interrompi debug** (MAIUSC + F5) dal menu **Esegui** .

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Trovare i nomi di dominio per l'accesso al firewall

Prima di distribuire ed eseguire il flusso di lavoro dell'app per la logica nel portale di Azure, se l'ambiente presenta requisiti di rete rigidi o firewall che limitano il traffico, è necessario impostare le autorizzazioni per qualsiasi trigger o connessione di azione esistente nel flusso di lavoro.

Per trovare i nomi di dominio completi (FQDN) per queste connessioni, attenersi alla procedura seguente:

1. Nel progetto dell'app per la logica aprire il **connections.js** nel file, che viene creato dopo aver aggiunto il primo trigger o azione basata sulla connessione al flusso di lavoro e aver trovato l' `managedApiConnections` oggetto.

1. Per ogni connessione creata, trovare, copiare e salvare il `connectionRuntimeUrl` valore della proprietà in un punto sicuro, in modo da poter configurare il firewall con queste informazioni.

   Questo esempio **connections.jsnel** file contiene due connessioni, una connessione AS2 e una connessione Office 365 con questi `connectionRuntimeUrl` valori:

   * AS2 `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

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

Da Visual Studio Code è possibile pubblicare direttamente il progetto in Azure, che distribuisce l'app per la logica usando il nuovo tipo di risorsa app per la **logica (anteprima)** . Analogamente alla risorsa app per le funzioni in funzioni di Azure, la distribuzione per questo nuovo tipo di risorsa richiede la selezione di un [piano di hosting e di un piano tariffario](../app-service/overview-hosting-plans.md)che è possibile configurare durante la distribuzione. Per ulteriori informazioni sui piani di hosting e i prezzi, vedere gli argomenti seguenti:

* [Scalabilità verticale di un servizio app Azure](../app-service/manage-scale-up.md)
* [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)

È possibile pubblicare l'app per la logica come nuova risorsa, che crea automaticamente le risorse necessarie, ad esempio un [account di archiviazione di Azure, in modo analogo ai requisiti dell'app per le funzioni](../azure-functions/storage-considerations.md). In alternativa, è possibile pubblicare l'app per la logica in una risorsa di app per la **logica (anteprima)** distribuita in precedenza, che sovrascrive tale app per la logica.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Pubblicare in una nuova risorsa app per la logica (anteprima)

1. Nella barra attività Visual Studio Code selezionare l'icona Azure.

1. Nella barra degli strumenti **Azure: app per la logica (anteprima)** selezionare **Distribuisci in app per la logica**.

   ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e la barra degli strumenti del riquadro con "Distribuisci in app per la logica" selezionati.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Se richiesto, selezionare la sottoscrizione di Azure da usare per la distribuzione dell'app per la logica.

1. Dall'elenco Visual Studio Code si apre, selezionare una delle opzioni seguenti:

   * **Creare una nuova app per la logica (anteprima) in Azure** (Quick)
   * **Creare una nuova app per la logica (anteprima) in Azure Advanced**
   * Una risorsa dell' **app per la logica (anteprima)** distribuita in precedenza, se presente

   Questo esempio continua con **Crea nuova app per la logica (anteprima) in Azure Advanced**.

   ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" con un elenco con "Crea nuova app per la logica (anteprima) in Azure" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Per creare la nuova risorsa app per la **logica (anteprima)** , seguire questa procedura:

   1. Fornire un nome univoco globale per la nuova app per la logica, che è il nome da usare per la risorsa app per la **logica (anteprima)** . In questo esempio viene utilizzato `Fabrikam-Workflows-App`.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per fornire un nome per la nuova app per la logica da creare.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selezionare un [piano di hosting](../app-service/overview-hosting-plans.md) per la nuova app per la logica, ovvero [ **piano di servizio app** (dedicato)](../azure-functions/dedicated-plan.md) o [**Premium**](../azure-functions/functions-premium-plan.md).

      > [!IMPORTANT]
      > I piani a consumo non sono supportati né disponibili per questo tipo di risorsa. Il piano selezionato influiscono sulle funzionalità e sui piani tariffari disponibili in un secondo momento. Per ulteriori informazioni, vedere gli argomenti seguenti: 
      >
      > * [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)
      > * [Dettagli prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Il piano Premium, ad esempio, consente di accedere alle funzionalità di rete, ad esempio la connessione e l'integrazione privata con le reti virtuali di Azure, in modo analogo alle funzioni di Azure quando si creano e si distribuiscono le app per la logica. 
      > Per ulteriori informazioni, vedere gli argomenti seguenti:
      > 
      > * [Opzioni di rete di Funzioni di Azure](../azure-functions/functions-networking-options.md)
      > * [App per la logica di Azure in esecuzione ovunque e possibilità di rete con app per la logica di Azure Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      Questo esempio usa il **piano di servizio app**.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per selezionare "piano di servizio app" o "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Creare un nuovo piano di servizio app o selezionare un piano esistente. Questo esempio Mostra come selezionare **Crea nuovo piano di servizio app**.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per "creare un nuovo piano di servizio app" o selezionare un piano di servizio app esistente.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Specificare un nome per il piano di servizio app e quindi selezionare un piano [tariffario](../app-service/overview-hosting-plans.md) per il piano. Questo esempio Mostra come selezionare il piano **F1 gratuito** .

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per selezionare un piano tariffario.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Per prestazioni ottimali, trovare e selezionare lo stesso gruppo di risorse del progetto per la distribuzione.

      > [!NOTE]
      > Sebbene sia possibile creare o usare un gruppo di risorse diverso, questa operazione potrebbe influire sulle prestazioni. Se si crea o si sceglie un gruppo di risorse diverso, ma si annulla dopo che viene visualizzata la richiesta di conferma, viene annullata anche la distribuzione.

   1. Per i flussi di lavoro con stato selezionare **Crea nuovo account di archiviazione** o un account di archiviazione esistente.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per creare o selezionare un account di archiviazione.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Se le impostazioni di creazione e distribuzione dell'app per la logica supportano l'uso di [Application Insights](../azure-monitor/app/app-insights-overview.md), è possibile abilitare facoltativamente la registrazione e la traccia di diagnostica per l'app per la logica. Questa operazione può essere eseguita quando si distribuisce l'app per la logica da Visual Studio Code o dopo la distribuzione. È necessario disporre di un'istanza di Application Insights, ma è possibile creare questa risorsa [in anticipo](../azure-monitor/app/create-workspace-resource.md), quando si distribuisce l'app per la logica o dopo la distribuzione.

      Per abilitare la registrazione e la traccia ora, seguire questa procedura:

      1. Selezionare una risorsa Application Insights esistente o **crearne una nuova Application Insights**.

      1. Nella [portale di Azure](https://portal.azure.com)passare alla risorsa Application Insights.

      1. Scegliere **Panoramica** dal menu risorsa. Trovare e copiare il valore della **chiave di strumentazione** .

      1. In Visual Studio Code, nella cartella radice del progetto, aprire il **local.settings.jssu** file.

      1. Nell' `Values` oggetto aggiungere la `APPINSIGHTS_INSTRUMENTATIONKEY` proprietà e impostare il valore sulla chiave di strumentazione, ad esempio:

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
         > È possibile verificare se i nomi dei trigger e delle azioni vengono visualizzati correttamente nell'istanza di Application Insights.
         >
         > 1. Nella portale di Azure passare alla risorsa Application Insights.
         >
         > 2. Nel menu risorsa risorsa, in **analisi**, selezionare **mappa delle applicazioni**.
         >
         > 3. Esaminare i nomi delle operazioni visualizzati nella mappa.
         >
         > Alcune richieste in ingresso provenienti da trigger predefiniti possono apparire come duplicati nella mappa delle applicazioni. 
         > Anziché usare il `WorkflowName.ActionName` formato, questi duplicati usano il nome del flusso di lavoro come nome dell'operazione e provengono dall'host di funzioni di Azure.

      1. Successivamente, è possibile modificare facoltativamente il livello di gravità per i dati di traccia raccolti e inviati dall'app per la logica all'istanza di Application Insights.

         Ogni volta che si verifica un evento correlato al flusso di lavoro, ad esempio quando viene attivato un flusso di lavoro o quando viene eseguita un'azione, il runtime genera diverse tracce. Queste tracce coprono la durata del flusso di lavoro e includono, ma non sono limitate, i tipi di evento seguenti:

         * Attività del servizio, ad esempio Start, stop ed Errors.
         * Processi e attività del dispatcher.
         * Attività del flusso di lavoro, ad esempio trigger, azione ed esecuzione.
         * Attività di richiesta di archiviazione, ad esempio esito positivo o negativo.
         * Attività di richiesta HTTP, ad esempio in ingresso, in uscita, riuscita ed errore.
         * Tutte le tracce di sviluppo, ad esempio i messaggi di debug.

         Ogni tipo di evento viene assegnato a un livello di gravità. Ad esempio, il `Trace` livello acquisisce i messaggi più dettagliati, mentre il `Information` livello acquisisce attività generali nel flusso di lavoro, ad esempio quando l'app per la logica, il flusso di lavoro, il trigger e le azioni vengono avviati e arrestati. In questa tabella vengono descritti i livelli di gravità e i tipi di traccia seguenti:

         | Livello di gravità | Tipo di traccia |
         |----------------|------------|
         | Critico | Log che descrivono un errore irreversibile nell'app per la logica. |
         | Debug | Log che è possibile usare per l'analisi durante lo sviluppo, ad esempio le chiamate HTTP in ingresso e in uscita. |
         | Errore | Log che indicano un errore nell'esecuzione del flusso di lavoro, ma non un errore generale nell'app per la logica. |
         | Informazioni | Log che tengono traccia dell'attività generale nell'app per la logica o nel flusso di lavoro, ad esempio: <p><p>-Quando un trigger, un'azione o un'esecuzione inizia e termina. <br>-Quando l'app per la logica inizia o termina. |
         | Trace | Log che contengono i messaggi più dettagliati, ad esempio le richieste di archiviazione o l'attività del dispatcher, oltre a tutti i messaggi correlati all'attività di esecuzione del flusso di lavoro. |
         | Avviso | Log che evidenziano uno stato anomalo nell'app per la logica, ma non ne impedisce l'esecuzione. |
         |||

         Per impostare il livello di gravità, a livello di radice del progetto, aprire il **host.jsnel** file e individuare l' `logging` oggetto. Questo oggetto controlla il filtro dei log per tutti i flussi di lavoro nell'app per la logica e segue il [layout ASP.NET Core per il filtraggio dei tipi di log](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

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

         Se l' `logging` oggetto non contiene un `logLevel` oggetto che include la `Host.Triggers.Workflow` proprietà, aggiungere gli elementi. Impostare la proprietà sul livello di gravità per il tipo di traccia desiderato, ad esempio:

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

   Al termine della procedura di distribuzione, Visual Studio Code avvia la creazione e la distribuzione delle risorse necessarie per la pubblicazione dell'app per la logica.

1. Per esaminare e monitorare il processo di distribuzione, scegliere **output** dal menu **Visualizza** . Dall'elenco della barra degli strumenti della finestra di output selezionare app per la **logica di Azure**.

   ![Screenshot che mostra la finestra di output con "app per la logica di Azure" selezionata nell'elenco della barra degli strumenti con l'avanzamento e lo stato della distribuzione.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Quando Visual Studio Code termina la distribuzione dell'app per la logica in Azure, viene visualizzato il messaggio seguente:

   ![Screenshot che mostra un messaggio che indica che la distribuzione in Azure è stata completata correttamente.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Congratulazioni, l'app per la logica è ora disponibile in Azure e abilitata per impostazione predefinita.

Successivamente, è possibile apprendere come eseguire queste attività:

* [Aggiungere un flusso di lavoro vuoto al progetto](#add-workflow-existing-project).

* [Gestire le app per la logica distribuite in Visual Studio Code](#manage-deployed-apps-vs-code) o usando il [portale di Azure](#manage-deployed-apps-portal).

* [Abilitare la cronologia di esecuzione in flussi di lavoro](#enable-run-history-stateless)senza stato.

* [Abilitare la visualizzazione monitoraggio nel portale di Azure per un'app per la logica distribuita](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Aggiungi flusso di lavoro vuoto al progetto

È possibile avere più flussi di lavoro nel progetto di app per la logica. Per aggiungere un flusso di lavoro vuoto al progetto, attenersi alla procedura seguente:

1. Nella barra attività Visual Studio Code selezionare l'icona Azure.

1. Nel riquadro di Azure, accanto ad **Azure: app per la logica (anteprima)**, selezionare **Crea flusso di lavoro** (icona per app per la logica di Azure).

1. Selezionare il tipo di flusso di lavoro che si desidera aggiungere: con stato **o senza** **stato**

1. Consente di specificare un nome per il flusso di lavoro.

Al termine, verrà visualizzata una nuova cartella del flusso di lavoro nel progetto insieme a un **workflow.jssu** file per la definizione del flusso di lavoro.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Gestire le app per la logica distribuite in Visual Studio Code

In Visual Studio Code, è possibile visualizzare tutte le app per la logica distribuite nella sottoscrizione di Azure, sia che si tratti **delle app per la logica originali che** del tipo di risorsa app per la **logica (anteprima)** e selezionare le attività che consentono di gestire le app per la logica. Tuttavia, per accedere a entrambi i tipi di risorse, sono necessarie le estensioni delle app per la **logica di Azure** e delle app per la **logica di Azure (anteprima)** per Visual Studio Code.

1. Sulla barra degli strumenti a sinistra selezionare l'icona Azure. Nel riquadro **Azure: app per la logica (anteprima)** espandere la sottoscrizione, che Mostra tutte le app per la logica distribuite per tale sottoscrizione.

1. Aprire l'app per la logica che si vuole gestire. Dal menu di scelta rapida dell'app per la logica selezionare l'attività che si desidera eseguire.

   Ad esempio, è possibile selezionare attività come l'arresto, l'avvio, il riavvio o l'eliminazione dell'app per la logica distribuita.

   ![Screenshot che mostra Visual Studio Code con il riquadro dell'estensione "app per la logica di Azure (anteprima)" aperta e il flusso di lavoro distribuito.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Per visualizzare tutti i flussi di lavoro nell'app per la logica, espandere l'app per la logica, quindi espandere il nodo **flussi di lavoro** .

1. Per visualizzare un flusso di lavoro specifico, aprire il menu di scelta rapida del flusso di lavoro e selezionare **Apri in finestra di progettazione** per aprire il flusso di lavoro in modalità di sola lettura.

   Per modificare il flusso di lavoro, sono disponibili le opzioni seguenti:

   * In Visual Studio Code aprire il **workflow.js** del progetto in un file nella finestra di progettazione del flusso di lavoro, apportare le modifiche e ridistribuire l'app per la logica in Azure.

   * Nella portale di Azure [individuare e aprire l'app per la logica](#manage-deployed-apps-portal). Trovare, modificare e salvare il flusso di lavoro.

1. Per aprire l'app per la logica distribuita nel portale di Azure, aprire il menu di scelta rapida dell'app per la logica e selezionare **Apri nel portale**.

   Il portale di Azure si apre nel browser, accede automaticamente al portale se è stato eseguito l'accesso a Visual Studio Code e viene visualizzata l'app per la logica.

   ![Screenshot che mostra la pagina portale di Azure per l'app per la logica in Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   È anche possibile accedere separatamente alla portale di Azure, usare la casella di ricerca del portale per trovare l'app per la logica e quindi selezionare l'app per la logica dall'elenco dei risultati.

   ![Screenshot che mostra il portale di Azure e la barra di ricerca con i risultati della ricerca per l'app per la logica distribuita, visualizzata come selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Gestire app per la logica distribuite nel portale

Nella portale di Azure è possibile visualizzare tutte le app per la logica distribuite nella sottoscrizione di Azure, sia il tipo di **risorsa app per la logica originale che** il tipo di risorsa app per la **logica (anteprima)** . Attualmente, ogni tipo di risorsa è organizzato e gestito come categorie separate in Azure. Per trovare app per la logica con il tipo di risorsa app per la **logica (anteprima)** , seguire questa procedura:

1. Nella casella di ricerca portale di Azure immettere `logic app preview` . Quando viene visualizzato l'elenco dei risultati, in **Servizi** selezionare app per la **logica (anteprima)**.

   ![Screenshot che mostra la casella di ricerca portale di Azure con il testo di ricerca "anteprima app per la logica".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Nel riquadro **app per la logica (anteprima)** individuare e selezionare l'app per la logica che è stata distribuita da Visual Studio Code.

   ![Screenshot che mostra le risorse portale di Azure e app per la logica (anteprima) distribuite in Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Il portale di Azure apre la pagina delle singole risorse per l'app per la logica selezionata.

   ![Screenshot che mostra la pagina delle risorse del flusso di lavoro dell'app per la logica nella portale di Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Per visualizzare i flussi di lavoro in questa app per la logica, selezionare **flussi di lavoro** dal menu dell'app per la logica.

   Il riquadro **flussi di lavoro** Mostra tutti i flussi di lavoro nell'app per la logica corrente. Questo esempio illustra il flusso di lavoro creato in Visual Studio Code.

   ![Screenshot che mostra una pagina delle risorse "app per la logica (anteprima)" con il riquadro "flussi di lavoro" aperto e il flusso di lavoro distribuito](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Per visualizzare un flusso di lavoro, selezionare il flusso di lavoro nel riquadro **flussi di lavoro** .

   Viene visualizzato il riquadro del flusso di lavoro con altre informazioni e attività che è possibile eseguire su tale flusso di lavoro.

   Per visualizzare, ad esempio, i passaggi nel flusso di lavoro, selezionare **finestra di progettazione**.

   ![Screenshot che mostra il riquadro "panoramica" del flusso di lavoro selezionato, mentre il menu flusso di lavoro Mostra il comando "finestra di progettazione" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Viene visualizzata la finestra di progettazione del flusso di lavoro che mostra il flusso di lavoro compilato in Visual Studio Code. È ora possibile apportare modifiche a questo flusso di lavoro nella portale di Azure.

   ![Screenshot che mostra progettazione flussi di lavoro e flusso di lavoro distribuiti da Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Aggiungere un altro flusso di lavoro nel portale

Tramite il portale di Azure è possibile aggiungere flussi di lavoro vuoti a una risorsa di app per la **logica (anteprima)** distribuita da Visual Studio Code e compilare tali flussi di lavoro nell'portale di Azure.

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa app per la logica distribuita **(anteprima)** .

1. Nel menu dell'app per la logica selezionare **flussi di lavoro**. Nel riquadro **flussi di lavoro** selezionare **Aggiungi**.

   ![Screenshot che mostra il riquadro "flussi di lavoro" dell'app per la logica selezionato e la barra degli strumenti con il comando "Aggiungi" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Nel riquadro **nuovo flusso di lavoro** specificare il nome del flusso di lavoro. Selezionare Crea con stato o **senza** **stato** **>** .

   Quando Azure distribuisce il nuovo flusso di lavoro, che viene visualizzato nel riquadro **flussi di lavoro** , selezionare il flusso di lavoro in modo da poter gestire ed eseguire altre attività, ad esempio aprendo la finestra di progettazione o la visualizzazione codice.

   ![Screenshot che mostra il flusso di lavoro selezionato con le opzioni di gestione e revisione.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Ad esempio, l'apertura della finestra di progettazione per un nuovo flusso di lavoro mostra un'area di disegno vuota. È ora possibile compilare il flusso di lavoro nella portale di Azure.

   ![Screenshot che mostra la finestra di progettazione del flusso di lavoro e un flusso di lavoro vuoto.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Abilitare la cronologia di esecuzione per i flussi di lavoro senza stato

Per eseguire il debug di un flusso di lavoro senza stato in modo più semplice, è possibile abilitare la cronologia di esecuzione per il flusso di lavoro e quindi disabilitare la cronologia di esecuzione al termine dell'operazione. Seguire questa procedura per Visual Studio Code o, se si lavora nella portale di Azure, vedere [creare flussi di lavoro con stato e senza stato nella portale di Azure](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. Nel progetto Visual Studio Code espandere la cartella **Workflow-DesignTime** e aprire il **local.settings.jssu** file.

1. Aggiungere la `Workflows.{yourWorkflowName}.operationOptions` proprietà e impostare il valore su `WithStatelessRunHistory` , ad esempio:

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

1. Per disabilitare la cronologia di esecuzione al termine, impostare la `Workflows.{yourWorkflowName}.OperationOptions` proprietà su `None` oppure eliminare la proprietà e il relativo valore.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Abilitare la visualizzazione monitoraggio nella portale di Azure

Dopo aver distribuito una risorsa di app per la **logica (anteprima)** da Visual Studio Code ad Azure, è possibile esaminare tutti i dettagli e la cronologia di esecuzione disponibili per un flusso di lavoro in tale risorsa usando i portale di Azure e l'esperienza di **monitoraggio** per tale flusso di lavoro. Tuttavia, per prima cosa è necessario abilitare la funzionalità di visualizzazione del **monitoraggio** su tale risorsa dell'app per la logica.

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa app per la logica distribuita **(anteprima)** .

1. Nel menu di tale risorsa, in **API**, selezionare **CORS**.

1. Nel riquadro **CORS** , in **origini consentite**, aggiungere il carattere jolly (*).

1. Al termine, sulla barra degli strumenti di **CORS** selezionare **Salva**.

   ![Screenshot che mostra la portale di Azure con una risorsa app per la logica distribuita (anteprima). Nel menu delle risorse è selezionato "CORS" con una nuova voce per "origini consentite" impostata sul carattere jolly "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Abilita o Apri Application Insights dopo la distribuzione

Durante l'esecuzione del flusso di lavoro, l'app per la logica emette la telemetria insieme ad altri eventi. È possibile usare questi dati di telemetria per ottenere una migliore visibilità sull'esecuzione del flusso di lavoro e sul funzionamento del runtime di app per la logica in diversi modi. È possibile monitorare il flusso di lavoro usando [Application Insights](../azure-monitor/app/app-insights-overview.md), che fornisce dati di telemetria quasi in tempo reale (metriche attive). Questa funzionalità consente di esaminare più facilmente gli errori e i problemi di prestazioni quando si utilizzano questi dati per diagnosticare i problemi, impostare gli avvisi e creare grafici.

Se le impostazioni di creazione e distribuzione dell'app per la logica supportano l'uso di [Application Insights](../azure-monitor/app/app-insights-overview.md), è possibile abilitare facoltativamente la registrazione e la traccia di diagnostica per l'app per la logica. Questa operazione può essere eseguita quando si distribuisce l'app per la logica da Visual Studio Code o dopo la distribuzione. È necessario disporre di un'istanza di Application Insights, ma è possibile creare questa risorsa [in anticipo](../azure-monitor/app/create-workspace-resource.md), quando si distribuisce l'app per la logica o dopo la distribuzione.

Per abilitare Application Insights in un'app per la logica distribuita o per esaminare Application Insights dati quando è già abilitato, attenersi alla procedura seguente:

1. Nella portale di Azure individuare l'app per la logica distribuita.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Application Insights**.

1. Se Application Insights non è abilitato, nel riquadro **Application Insights** selezionare **attiva Application Insights**. Dopo aver aggiornato il riquadro, nella parte inferiore selezionare **applica**.

   Se Application Insights è abilitato, nel riquadro **Application Insights** selezionare **Visualizza dati Application Insights**.

Dopo l'apertura di Application Insights, è possibile esaminare varie metriche per l'app per la logica. Per ulteriori informazioni, vedere gli argomenti seguenti:

* [App per la logica di Azure in esecuzione ovunque-monitoraggio con Application Insights-parte 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [App per la logica di Azure in esecuzione ovunque-monitoraggio con Application Insights-parte 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Distribuisci in Docker

È possibile distribuire l'app per la logica in un [contenitore Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) come ambiente host usando l' [interfaccia](/dotnet/core/tools/)della riga di comando di .NET. Con questi comandi, è possibile compilare e pubblicare il progetto dell'app per la logica. È quindi possibile compilare ed eseguire il contenitore Docker come destinazione per la distribuzione dell'app per la logica.

Se non si ha familiarità con Docker, vedere gli argomenti seguenti:

* [Che cos'è Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Introduzione a contenitori e Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introduzione a .NET e Docker](/dotnet/core/docker/introduction)
* [Contenitori, immagini e registri Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Esercitazione: Introduzione a Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Requisiti

* L'account di archiviazione di Azure usato dall'app per la logica per la distribuzione

* Un file Docker per il flusso di lavoro usato durante la compilazione del contenitore Docker

  Ad esempio, questo file Docker di esempio distribuisce un'app per la logica. Specifica la stringa di connessione che contiene la chiave di accesso per l'account di archiviazione di Azure usato per la pubblicazione dell'app per la logica nella portale di Azure. Per trovare questa stringa, vedere [ottenere la stringa di connessione dell'account di archiviazione](#find-storage-account-connection-string).

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

   Per altre informazioni, vedere [procedure consigliate per la scrittura di file Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Ottenere la stringa di connessione dell'account di archiviazione

Prima di poter compilare ed eseguire l'immagine del contenitore Docker, è necessario ottenere la stringa di connessione che contiene la chiave di accesso all'account di archiviazione. In precedenza è stato creato questo account di archiviazione come per usare l'estensione in macOS o Linux oppure quando è stata distribuita l'app per la logica nel portale di Azure.

Per trovare e copiare la stringa di connessione, seguire questa procedura:

1. Nel portale di Azure, nel menu account di archiviazione, in **Impostazioni**, selezionare **chiavi di accesso**. 

1. Nel riquadro **chiavi di accesso** individuare e copiare la stringa di connessione dell'account di archiviazione, che ha un aspetto simile all'esempio seguente:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Screenshot che mostra la portale di Azure con le chiavi di accesso dell'account di archiviazione e la stringa di connessione copiata.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Per altre informazioni, vedere [gestire le chiavi dell'account di archiviazione](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Salvare la stringa di connessione in un punto sicuro, in modo che sia possibile aggiungere questa stringa al file Docker usato per la distribuzione. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Trovare la chiave master per l'account di archiviazione

Quando il flusso di lavoro contiene un trigger di richiesta, è necessario [ottenere l'URL di callback del trigger dopo la](#get-callback-url-request-trigger) compilazione e l'esecuzione dell'immagine del contenitore docker. Per questa attività, è necessario specificare anche il valore della chiave master per l'account di archiviazione usato per la distribuzione.

1. Per trovare la chiave master, nel progetto aprire il file **Azure-webjobs-Secrets/{Deployment-Name}/host.js** .

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

1. Salvare il valore della chiave in un punto sicuro da usare in un secondo momento.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Compilare ed eseguire l'immagine del contenitore Docker

1. Compilare l'immagine del contenitore Docker usando il file Docker ed eseguendo questo comando:

   `docker build --tag local/workflowcontainer .`

   Per altre informazioni, vedere la pagina relativa alla [compilazione di Docker](https://docs.docker.com/engine/reference/commandline/build/).

1. Eseguire il contenitore localmente utilizzando questo comando:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Per altre informazioni, vedere [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Ottenere l'URL di callback per il trigger di richiesta

Per un flusso di lavoro che usa il trigger request, ottenere l'URL di callback del trigger inviando la richiesta seguente:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

Il `{trigger-name}` valore è il nome del trigger di richiesta che viene visualizzato nella definizione JSON del flusso di lavoro. Il `{master-key}` valore è definito nell'account di archiviazione di Azure impostato per la `AzureWebJobsStorage` proprietà all'interno del file, **Azure-webjobs-Secrets/{deployment-name}/host.json**. Per altre informazioni, vedere [trovare la chiave master dell'account di archiviazione](#find-storage-account-master-key).

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Elimina elementi dalla finestra di progettazione

Per eliminare un elemento del flusso di lavoro dalla finestra di progettazione, eseguire una di queste operazioni:

* Selezionare l'elemento, aprire il menu di scelta rapida dell'elemento (MAIUSC + F10) e selezionare **Elimina**. Per confermare, scegliere **OK**.

* Selezionare l'elemento e premere il tasto CANC. Per confermare, scegliere **OK**.

* Selezionare l'elemento in modo che il riquadro dei dettagli venga aperto per l'elemento. Nell'angolo superiore destro del riquadro aprire il menu con i puntini di sospensione (**..**.) e selezionare **Elimina**. Per confermare, scegliere **OK**.

  ![Screenshot che mostra un elemento selezionato nella finestra di progettazione con il riquadro dettagli aperto più il pulsante con i puntini di sospensione selezionati e il comando "Elimina".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Se il menu puntini di sospensione non è visibile, espandere Visual Studio Code finestra sufficientemente grande in modo che il riquadro dei dettagli mostri il pulsante con i puntini di sospensione (**..**.) nell'angolo superiore destro.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Risolvere gli errori e i problemi

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Impossibile aprire la finestra di progettazione

Quando si tenta di aprire la finestra di progettazione, viene visualizzato l'errore **"Impossibile avviare la fase di progettazione del flusso di lavoro"**. Se in precedenza si è tentato di aprire la finestra di progettazione e quindi il progetto è stato interrotto o eliminato, è possibile che il pacchetto di estensione non venga scaricato correttamente. Per verificare se la causa è il problema, attenersi alla seguente procedura:

  1. In Visual Studio Code aprire la finestra di output. Scegliere **output** dal menu **Visualizza** .

  1. Dall'elenco nella barra del titolo della finestra di output selezionare app per la **logica di Azure (anteprima)** per poter esaminare l'output dell'estensione, ad esempio:

     ![Screenshot che mostra la finestra di output con "app per la logica di Azure" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Esaminare l'output e verificare se viene visualizzato il messaggio di errore seguente:

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

   Per correggere l'errore, eliminare la cartella **ExtensionBundles** in questo percorso **. ..\Users \{ your-username} \AppData\Local\Temp\Functions\ExtensionBundles** e riprovare ad aprire il **workflow.js** nel file nella finestra di progettazione.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Nuovi trigger e azioni mancanti dal selettore della finestra di progettazione per i flussi di lavoro creati in precedenza

L'anteprima di app per la logica di Azure supporta azioni predefinite per operazioni di funzioni di Azure, operazioni Liquid e operazioni XML, ad esempio la **convalida XML** e la **trasformazione XML**. Tuttavia, per le app per la logica create in precedenza, queste azioni potrebbero non essere visualizzate nel selettore della finestra di progettazione per selezionare se Visual Studio Code Usa una versione obsoleta del bundle di estensione `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Inoltre, le azioni e il connettore per le **funzioni di Azure** non vengono visualizzati nel selettore della finestra di progettazione a meno che non sia stato abilitato o selezionato **usa connettori da Azure** quando è stata creata l'app per la logica Se non sono stati abilitati i connettori distribuiti da Azure al momento della creazione dell'app, è possibile abilitarli dal progetto in Visual Studio Code. Aprire il **workflow.js** dal menu di scelta rapida e selezionare **usa connettori da Azure**.

Per correggere il bundle obsoleto, attenersi alla procedura seguente per eliminare il bundle obsoleto, che rende Visual Studio Code aggiornare automaticamente il bundle di estensione alla versione più recente.

> [!NOTE]
> Questa soluzione si applica solo alle app per la logica create e distribuite usando Visual Studio Code con l'estensione app per la logica di Azure (anteprima), non le app per la logica create con l'portale di Azure. Vedere [trigger e azioni supportati mancanti nella finestra di progettazione nel portale di Azure](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Salvare le operazioni che non si desidera perdere e chiudere Visual Studio.

1. Nel computer individuare la cartella seguente, che contiene le cartelle con versione per il bundle esistente:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Eliminare la cartella Version per il bundle precedente, ad esempio se si dispone di una cartella per la versione 1.1.3, eliminare la cartella.

1. A questo punto, passare alla cartella seguente, che contiene le cartelle con versione per il pacchetto NuGet necessario:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Eliminare la cartella Version per il pacchetto precedente, ad esempio, se si dispone di una cartella per la versione 1.0.0.8-Preview, eliminare la cartella.

1. Riaprire Visual Studio Code, il progetto e il **workflow.jssul** file nella finestra di progettazione.

I trigger e le azioni mancanti vengono ora visualizzati nella finestra di progettazione.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 richiesta non valida" viene visualizzato in un trigger o in un'azione

Quando un'esecuzione ha esito negativo e si controlla l'esecuzione in visualizzazione monitoraggio, questo errore può essere visualizzato in un trigger o in un'azione con un nome più lungo, che fa sì che il Uniform Resource Identifier sottostante (URI) superi il limite di caratteri predefinito.

Per risolvere il problema e modificare per l'URI più lungo, modificare le chiavi del registro di sistema `UrlSegmentMaxCount` e `UrlSegmentMaxLength` nel computer attenendosi alla procedura descritta di seguito. Questi valori predefiniti della chiave sono descritti in questo argomento, [Http.sys impostazioni del registro di sistema per Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Prima di iniziare, assicurarsi di salvare il lavoro. Per questa soluzione è necessario riavviare il computer al termine dell'operazione, in modo da rendere effettive le modifiche.

1. Nel computer aprire la finestra **Esegui** ed eseguire il `regedit` comando, che consente di aprire l'editor del registro di sistema.

1. Nella casella **controllo account utente** selezionare **Sì** per consentire le modifiche apportate al computer.

1. Nel riquadro sinistro, in **computer**, espandere i nodi lungo il percorso, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters**, quindi selezionare **parametri**.

1. Nel riquadro destro trovare le chiavi del `UrlSegmentMaxCount` `UrlSegmentMaxLength` Registro di sistema e.

1. Aumentare questi valori di chiave in modo che gli URI possano contenere i nomi che si desidera utilizzare. Se queste chiavi non esistono, aggiungerle alla cartella **Parameters** attenendosi alla seguente procedura:

   1. Dal menu di scelta rapida **parametri** selezionare **nuovo**  >  **valore DWORD (32-bit)**.

   1. Nella casella di modifica visualizzata immettere `UrlSegmentMaxCount` come nuovo nome chiave.

   1. Aprire il menu di scelta rapida della nuova chiave e selezionare **modifica**.

   1. Nella casella **Modifica stringa** visualizzata immettere il valore della chiave di **dati del valore** desiderato in formato esadecimale o decimale. Ad esempio, `400` in esadecimale equivale a `1024` in decimale.

   1. Per aggiungere il `UrlSegmentMaxLength` valore della chiave, ripetere questi passaggi.

   Dopo aver aumentato o aggiunto questi valori di chiave, l'editor del registro di sistema ha un aspetto simile a questo esempio:

   ![Screenshot che mostra l'editor del registro di sistema.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Quando si è pronti, riavviare il computer per rendere effettive le modifiche.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Impossibile avviare la sessione di debug

Quando si tenta di avviare una sessione di debug, viene ricevuto l'errore **"errore esistente dopo l'esecuzione di preavvio ' generateDebugSymbols '"**. Per risolvere il problema, modificare la **tasks.js** nel file del progetto per ignorare la generazione di simboli.

1. Nel progetto espandere la cartella **. VSCODE** e aprire il **tasks.jssu** file.

1. Nell'attività seguente, eliminare la riga, `"dependsOn: "generateDebugSymbols"` , insieme alla virgola che termina la riga precedente, ad esempio:

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

Vorremmo ricevere informazioni sull'esperienza con l'estensione app per la logica di Azure (anteprima).

* Per bug o problemi, [creare i problemi in GitHub](https://github.com/Azure/logicapps/issues).
* Per domande, richieste, commenti e altri commenti, [usare questo modulo di feedback](https://aka.ms/lafeedback).
