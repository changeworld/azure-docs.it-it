---
title: Creare flussi di lavoro di anteprima di app per la logica nella portale di Azure
description: Compila ed Esegui i flussi di lavoro per gli scenari di automazione e integrazione con le app per la logica di Azure Preview nel portale di Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a7e19894a4688fe270422e93f7081f98e0b699a3
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936533"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Creare flussi di lavoro con stato e senza stato nel portale di Azure con app per la logica di Azure Preview

> [!IMPORTANT]
> Questa funzionalità è in anteprima pubblica, viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Con l'anteprima di app per la [logica di Azure](logic-apps-overview-preview.md)è possibile creare soluzioni di automazione e integrazione tra app, dati, servizi cloud e sistemi creando ed eseguendo app per la logica che includono flussi di lavoro con [ *stato* e senza stato](logic-apps-overview-preview.md#stateful-stateless) nel portale di Azure iniziando con il nuovo tipo di risorsa app per la **logica (anteprima)** . Con questo nuovo tipo di app per la logica è possibile creare più flussi di lavoro basati sul runtime di anteprima di app per la logica di Azure riprogettato, che offre portabilità, migliori prestazioni e flessibilità per la distribuzione e l'esecuzione in diversi ambienti host, non solo Azure, ma anche contenitori docker. Per altre informazioni sul nuovo tipo di app per la logica, vedere [Panoramica dell'anteprima di app per la logica di Azure](logic-apps-overview-preview.md).

![Screenshot che mostra la portale di Azure con la finestra di progettazione del flusso di lavoro per la risorsa "app per la logica (anteprima)".](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

Nella portale di Azure è possibile iniziare creando una nuova risorsa app per la **logica (anteprima)** . Anche se è possibile iniziare [creando un progetto in Visual Studio Code con l'estensione app per la logica di Azure (anteprima)](create-stateful-stateless-workflows-visual-studio-code.md), entrambi gli approcci offrono la possibilità di distribuire ed eseguire l'app per la logica negli stessi tipi di ambienti host.

Nel frattempo, è comunque possibile creare il tipo originale di app per la logica. Sebbene le esperienze di sviluppo nel portale differiscano tra i tipi di app per la logica originali e nuovi, la sottoscrizione di Azure può includere entrambi i tipi. È possibile visualizzare e accedere a tutte le app per la logica distribuite nella sottoscrizione di Azure, ma le app sono organizzate nelle rispettive categorie e sezioni.

Questo articolo illustra come compilare l'app per la logica e il flusso di lavoro nella portale di Azure usando il tipo di risorsa app per la **logica (anteprima)** ed eseguendo queste attività di alto livello:

* Creare la nuova risorsa app per la logica e aggiungere un flusso di lavoro vuoto.

* Aggiungere un trigger e un'azione.

* Attiva un'esecuzione del flusso di lavoro.

* Visualizzare la cronologia di esecuzione del flusso di lavoro.

* Abilitare o aprire il Application Insights dopo la distribuzione.

* Abilitare la cronologia di esecuzione per i flussi di lavoro senza stato.

> [!NOTE]
> Per informazioni sui problemi noti correnti, vedere la pagina relativa alle app per la [logica Public Preview problemi noti in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [account di archiviazione di Azure](../storage/common/storage-account-overview.md) perché la risorsa app per la **logica (anteprima)** è basata su funzioni di Azure e presenta [requisiti di archiviazione simili alle app per le funzioni](../azure-functions/storage-considerations.md). È possibile usare un account di archiviazione esistente oppure è possibile creare un account di archiviazione in anticipo o durante la creazione dell'app per la logica.

  > [!NOTE]
  > Le app per la [logica con stato](logic-apps-overview-preview.md#stateful-stateless) eseguono transazioni di archiviazione, ad esempio l'uso di code per la pianificazione e l'archiviazione degli Stati del flusso di lavoro in tabelle e BLOB. Queste transazioni comportano [costi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/). Per altre informazioni su come le app per la logica con stato archiviano i dati in un archivio esterno, vedere con stato e senza [stato](logic-apps-overview-preview.md#stateful-stateless).

* Per compilare la stessa app per la logica di esempio in questo articolo, è necessario un account di posta elettronica di Office 365 Outlook che usa un account Microsoft aziendale o dell'Istituto di istruzione per accedere.

  Se si sceglie di usare un [connettore di posta elettronica diverso supportato da app per la logica di Azure](/connectors/), ad esempio Outlook.com o [gmail](../connectors/connectors-google-data-security-privacy-policy.md), è comunque possibile seguire l'esempio e la procedura complessiva generale è la stessa, ma l'interfaccia utente e le opzioni potrebbero variare in qualche modo. Se ad esempio si usa il connettore Outlook.com, usare il account Microsoft personale per accedere.

* Per testare l'app per la logica di esempio creata in questo articolo, è necessario uno strumento che può inviare chiamate al trigger di richiesta, che è il primo passaggio nell'app per la logica di esempio. Se non si dispone di uno strumento di questo tipo, è possibile scaricare, installare e usare il [post](https://www.postman.com/downloads/).

* Se si crea l'app per la logica con impostazioni che supportano l'uso di [Application Insights](../azure-monitor/app/app-insights-overview.md), è possibile abilitare facoltativamente la registrazione e la traccia di diagnostica per l'app per la logica. Questa operazione può essere eseguita quando si crea l'app per la logica o dopo la distribuzione. È necessario disporre di un'istanza di Application Insights, ma è possibile creare questa risorsa [in anticipo](../azure-monitor/app/create-workspace-resource.md), quando si crea l'app per la logica o dopo la distribuzione.

## <a name="create-the-logic-app-resource"></a>Creare la risorsa dell'app per la logica

1. Accedere al [portale di Azure](https://portal.azure.com) con le credenziali dell'account Azure.

1. Nella casella di ricerca portale di Azure immettere `logic app preview` e selezionare app per la **logica (anteprima)**.

   ![Screenshot che mostra la casella di ricerca portale di Azure con il termine di ricerca "anteprima app per la logica" e la risorsa "app per la logica (anteprima)" selezionata.](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. Nella pagina **app per la logica (anteprima)** selezionare **Aggiungi**.

1. Nella pagina **Crea app per la logica (anteprima)** , nella scheda informazioni di **base** , fornire queste informazioni sull'app per la logica.

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per l'app per la logica. |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure in cui creare l'app per la logica e le risorse correlate. Il nome della risorsa deve essere univoco in tutte le aree e può contenere solo lettere, numeri, trattini ( **-** ), caratteri di sottolineatura (**_**), parentesi (**()**) e punti (**.**). <p><p>Questo esempio crea un gruppo di risorse denominato `Fabrikam-Workflows-RG` . |
   | **Nome dell'app per la logica** | Sì | <*logic-app-name*> | Nome da usare per l'app per la logica. Il nome della risorsa deve essere univoco in tutte le aree e può contenere solo lettere, numeri, trattini ( **-** ), caratteri di sottolineatura (**_**), parentesi (**()**) e punti (**.**). <p><p>Questo esempio Mostra come creare un'app per la logica denominata `Fabrikam-Workflows` . <p><p>**Nota**: il nome dell'app per la logica ottiene automaticamente il suffisso, `.azurewebsites.net` , perché la risorsa app per la **logica (anteprima)** è basata su funzioni di Azure, che usa la stessa convenzione di denominazione delle app. |
   | **Pubblica** | Sì | <*distribuzione-ambiente*> | Destinazione della distribuzione per l'app per la logica. È possibile eseguire la distribuzione in Azure selezionando **flusso di lavoro** o un contenitore docker. <p><p>Questo esempio usa il **flusso di lavoro**, ovvero la risorsa app per la **logica (anteprima)** in Azure. <p><p>Se si seleziona **contenitore Docker**, [specificare il contenitore da usare nelle impostazioni dell'app per la logica](#set-docker-container). |
   | **Area** | Sì | <*Area di Azure*> | Area di Azure da usare durante la creazione del gruppo di risorse e delle risorse. <p><p>Questo esempio usa **Stati Uniti occidentali**. |
   |||||

   Ecco un esempio:

   ![Screenshot che mostra il portale di Azure e la pagina "Crea app per la logica (anteprima)".](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Quindi, nella scheda **hosting** , fornire queste informazioni sulla soluzione di archiviazione e sul piano di hosting da usare per l'app per la logica.

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Account di archiviazione** | Sì | <*Azure-storage-account-name*> | L' [account di archiviazione di Azure](../storage/common/storage-account-overview.md) da usare per le transazioni di archiviazione. Il nome della risorsa deve essere univoco in tutte le aree e contenere 3-24 caratteri con solo numeri e lettere minuscole. Selezionare un account esistente o creare un nuovo account. <p><p>Questo esempio crea un account di archiviazione denominato `fabrikamstorageacct` . |
   | **Tipo di piano** | Sì | <*Azure-hosting-piano*> | [Piano di hosting](../app-service/overview-hosting-plans.md) da usare per la distribuzione dell'app per la logica, che può essere [**Premium**](../azure-functions/functions-premium-plan.md) o [**piano di servizio app**](../azure-functions/dedicated-plan.md). La scelta riguarda i piani tariffari che è possibile scegliere in un secondo momento. <p><p>Questo esempio usa il **piano di servizio app**. <p><p>**Nota**: Analogamente alle funzioni di Azure, il tipo di risorsa app per la **logica (anteprima)** richiede un piano di hosting e un piano tariffario. I piani di hosting a consumo non sono supportati né disponibili per questo tipo di risorsa. Per ulteriori informazioni, vedere gli argomenti seguenti: <p><p>- [Scalabilità e hosting di funzioni di Azure](../azure-functions/functions-scale.md) <br>- [Dettagli prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/) <p><p> |
   | **Piano Windows** | Sì | <*Nome piano*> | Nome del piano da utilizzare. Selezionare un piano esistente o specificare il nome per un nuovo piano. <p><p>Questo esempio usa il nome `Fabrikam-Service-Plan`. |
   | **SKU e dimensioni** | Sì | <*piano tariffario*> | Piano [tariffario](../app-service/overview-hosting-plans.md) da usare per l'hosting dell'app per la logica. Le scelte sono influenzate dal tipo di piano scelto in precedenza. Per modificare il livello predefinito, selezionare **modifica dimensioni**. È quindi possibile selezionare altri piani tariffari, in base al carico di lavoro necessario. <p><p>Questo esempio usa il piano **tariffario F1** gratuito per i carichi di lavoro di **sviluppo/test** . Per altre informazioni, vedere [Dettagli prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/). |
   |||||

1. Successivamente, se le impostazioni di creazione e distribuzione supportano l'uso di [Application Insights](../azure-monitor/app/app-insights-overview.md), è possibile abilitare facoltativamente la registrazione e la traccia di diagnostica per l'app per la logica.

   1. Nella scheda **monitoraggio** , in **Application Insights**, impostare **Abilita Application Insights** su **Sì** se non è già selezionato.

   1. Per l'impostazione di **Application Insights** , selezionare un'istanza di Application Insights esistente o creare una nuova istanza, selezionare **Crea nuovo** e specificare il nome che si desidera utilizzare.

1. Dopo che Azure ha convalidato le impostazioni dell'app per la logica, nella scheda **Verifica + crea** selezionare **Crea**.

   Esempio:

   ![Screenshot che mostra il portale di Azure e le nuove impostazioni delle risorse dell'app per la logica.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   Al termine della distribuzione di Azure, l'app per la logica è automaticamente in esecuzione, ma non esegue alcuna operazione perché non esistono flussi di lavoro.

1. Nella pagina completamento distribuzione selezionare **Vai alla risorsa** per poter iniziare a compilare il flusso di lavoro.

   ![Screenshot che mostra il portale di Azure e la distribuzione completata.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Specificare il contenitore Docker per la distribuzione

Se è stato selezionato **contenitore Docker** durante la creazione dell'app per la logica, assicurarsi di fornire informazioni sul contenitore che si vuole usare per la distribuzione dopo che la portale di Azure crea la risorsa dell'app per la **logica (anteprima)** .

1. Nella portale di Azure passare alla risorsa dell'app per la logica.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **impostazioni del contenitore**. Specificare i dettagli e il percorso per l'immagine del contenitore docker.

   ![Screenshot che mostra il menu dell'app per la logica con "impostazioni del contenitore" selezionato.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-deploy-container-settings.png)

1. Al termine, salvare le impostazioni.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Aggiungere un flusso di lavoro vuoto

1. Dopo che Azure ha aperto la risorsa, scegliere **flussi di lavoro** dal menu dell'app per la logica. Nella barra degli strumenti **flussi di lavoro** selezionare **Aggiungi**.

   ![Screenshot che mostra il menu delle risorse dell'app per la logica con "flussi di lavoro" selezionati e quindi Hen sulla barra degli strumenti, è selezionata l'opzione "Aggiungi".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Dopo l'apertura del **nuovo riquadro del flusso di lavoro** , specificare un nome per il flusso di lavoro e scegliere il tipo di flusso di lavoro con [ **stato** o senza stato](logic-apps-overview-preview.md#stateful-stateless) . Al termine, selezionare **Crea**.

   Questo esempio aggiunge un flusso di lavoro con stato vuoto denominato `Fabrikam-Stateful-Workflow` . Per impostazione predefinita, il flusso di lavoro è abilitato, ma non esegue alcuna operazione fino a quando non viene aggiunto un trigger e le azioni.

   ![Screenshot che mostra il flusso di lavoro con stato vuoto appena aggiunto "Fabrikam-state-workflow".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Aprire quindi il flusso di lavoro vuoto nella finestra di progettazione in modo che sia possibile aggiungere un trigger e un'azione.

   1. Dall'elenco flusso di lavoro selezionare il flusso di lavoro vuoto.

   1. Nel menu flusso di lavoro, in **sviluppatore** selezionare **finestra di progettazione**.

      Nell'area di progettazione, il prompt **scegliere un'operazione** è già visualizzato ed è selezionato per impostazione predefinita, in modo che venga visualizzato anche il riquadro **Aggiungi un trigger** .

      ![Screenshot che mostra la finestra di progettazione del flusso di lavoro aperta con "Scegli un'operazione" selezionata nell'area di progettazione.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Aggiungere un trigger e un'azione

Questo esempio crea un flusso di lavoro con i passaggi seguenti:

* [Trigger di richiesta](../connectors/connectors-native-reqres.md)incorporato, **quando viene ricevuta una richiesta http**, che riceve le chiamate in ingresso o le richieste e crea un endpoint che può essere chiamato da altri servizi o app per la logica.

* L' [azione Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), **Invia un messaggio di posta elettronica**.

* [Azione di risposta](../connectors/connectors-native-reqres.md)predefinita, usata per inviare una risposta e restituire i dati al chiamante.

### <a name="add-the-request-trigger"></a>Aggiungere il trigger Request

Prima di poter aggiungere un trigger a un flusso di lavoro vuoto, assicurarsi che Progettazione flussi di lavoro sia aperto e che il prompt **scegliere un'operazione** sia selezionato nell'area di progettazione.

1. Accanto all'area di progettazione, nel riquadro **Aggiungi un trigger** , nella casella di ricerca **scegliere un'operazione** verificare che la scheda **incorporata** sia selezionata. Questa scheda Mostra i trigger che vengono eseguiti in modalità nativa in app per la logica di Azure.

1. Nella casella di ricerca **scegliere un'operazione** immettere `when a http request` e selezionare il trigger di richiesta incorporato denominato **quando viene ricevuta una richiesta http**.

   ![Screenshot che mostra la finestra di progettazione e * * Aggiungi un riquadro Trigger * * con il trigger "quando viene ricevuta una richiesta HTTP" selezionato.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Quando il trigger viene visualizzato nella finestra di progettazione, il riquadro dei dettagli del trigger si apre per visualizzare le proprietà, le impostazioni e altre azioni del trigger.

   ![Screenshot che mostra la finestra di progettazione con il trigger "quando viene ricevuta una richiesta HTTP" selezionato e il riquadro dei dettagli del trigger aperto.](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se il riquadro dei dettagli non viene visualizzato, verifica che il trigger sia selezionato nella finestra di progettazione.

1. Se è necessario eliminare un elemento dalla finestra di progettazione, [attenersi alla procedura seguente per eliminare elementi dalla finestra di progettazione](#delete-from-designer).

1. Per salvare il lavoro, sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Quando si salva un flusso di lavoro per la prima volta e il flusso di lavoro inizia con un trigger di richiesta, il servizio app per la logica genera automaticamente un URL per un endpoint creato dal trigger di richiesta. Successivamente, quando si esegue il test del flusso di lavoro, si invia una richiesta a questo URL, che attiva il trigger e avvia l'esecuzione del flusso di lavoro.

### <a name="add-the-office-365-outlook-action"></a>Aggiungere l'azione Office 365 Outlook

1. Nella finestra di progettazione, sotto il trigger aggiunto, selezionare **nuovo passaggio**.

   Il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione e il riquadro **Aggiungi un'azione** viene riaperto in modo che sia possibile selezionare l'azione successiva.

   > [!NOTE]
   > Se nel riquadro **Aggiungi un'azione** viene visualizzato il messaggio di errore "Impossibile leggere la proprietà" filtro "di" non definito ", salvare il flusso di lavoro, ricaricare la pagina, riaprire il flusso di lavoro e riprovare.

1. Nella casella di ricerca **scegliere un'operazione** del riquadro **Aggiungi un'azione** selezionare **Azure**. Questa scheda Mostra i connettori gestiti disponibili e distribuiti in Azure.

   > [!NOTE]
   > Se nel riquadro **Aggiungi un'azione** viene visualizzato il messaggio di errore, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` , salvare il flusso di lavoro, ricaricare la pagina, riaprire il flusso di lavoro e provare ad aggiungere di nuovo l'azione.

   Questo esempio usa l'azione Office 365 Outlook denominata **Invia un messaggio di posta elettronica (v2)**.

   ![Screenshot che mostra la finestra di progettazione e il riquadro * * Aggiungi un'azione * * con l'azione di Office 365 Outlook "Invia un messaggio di posta elettronica" selezionata.](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. Nella scheda **Crea connessione** del riquadro dei dettagli dell'azione selezionare **Accedi** per poter creare una connessione all'account di posta elettronica.

   ![Screenshot che mostra la finestra di progettazione e il riquadro dei dettagli "Invia un messaggio di posta elettronica (v2)" con l'opzione "Accedi".](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. Quando viene richiesto il consenso per l'accesso all'account di posta elettronica, accedere con le credenziali dell'account.

   > [!NOTE]
   > Se viene ricevuto il messaggio di errore, `Failed with error: 'The browser is closed.'. Please sign in again` controllare se il browser blocca i cookie di terze parti. Se questi cookie sono bloccati, provare `https://portal.azure.com` ad aggiungere all'elenco dei siti che possono usare i cookie. Se si usa la modalità in incognito, assicurarsi che i cookie di terze parti non siano bloccati mentre si lavora in tale modalità.
   > 
   > Se necessario, ricaricare la pagina, aprire il flusso di lavoro, aggiungere di nuovo l'azione di posta elettronica e provare a creare la connessione.

   Dopo che Azure ha creato la connessione, l'azione **Invia un messaggio di posta elettronica** viene visualizzata nella finestra di progettazione ed è selezionata per impostazione predefinita. Se l'azione non è selezionata, selezionare l'azione in modo che sia aperto anche il riquadro dei dettagli.

1. Nel riquadro dei dettagli dell'azione, nella scheda **parametri** , fornire le informazioni necessarie per l'azione, ad esempio:

   ![Screenshot che mostra la finestra di progettazione e il riquadro dei dettagli "Invia un messaggio di posta elettronica" con la scheda "Parameters" selezionata.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Destinatario del messaggio di posta elettronica, che può essere l'indirizzo di posta elettronica a scopo di test. Questo esempio usa il messaggio di posta elettronica fittizio, `sophiaowen@fabrikam.com` . |
   | **Oggetto** | Sì | `An email from your example workflow` | L'oggetto del messaggio di posta elettronica |
   | **Corpo** | Sì | `Hello from your example workflow!` | Contenuto del corpo del messaggio di posta elettronica |
   ||||

   > [!NOTE]
   > Quando si apportano modifiche nel riquadro dei dettagli sulle schede **Impostazioni**, **risultato statico** o **esecuzione dopo** , assicurarsi di selezionare **completato** per confermare le modifiche prima di passare da una scheda all'altra o di spostare lo stato attivo sulla finestra di progettazione. In caso contrario, la finestra di progettazione non manterrà le modifiche.

1. Salvare il lavoro. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

Quindi, per testare il flusso di lavoro, attivare manualmente un'esecuzione.

## <a name="trigger-the-workflow"></a>Attivare il flusso di lavoro

In questo esempio, il flusso di lavoro viene eseguito quando il trigger di richiesta riceve una richiesta in ingresso, che viene inviata all'URL per l'endpoint creato dal trigger. Quando il flusso di lavoro è stato salvato per la prima volta, il servizio app per la logica ha generato automaticamente questo URL. Quindi, prima di poter inviare la richiesta per attivare il flusso di lavoro, è necessario trovare questo URL.

1. Nella finestra di progettazione del flusso di lavoro selezionare il trigger di richiesta denominato **quando viene ricevuta una richiesta http**.

1. Quando si apre il riquadro dei dettagli, nella scheda **parametri** individuare la proprietà **URL post http** . Per copiare l'URL generato, selezionare l'icona **Copia URL** (copia file) e salvare l'URL altrove per il momento. Il formato dell'URL è il seguente:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Screenshot che mostra la finestra di progettazione con il trigger di richiesta e l'URL dell'endpoint nella proprietà "HTTP POST URL".](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   Per questo esempio, l'URL ha un aspetto simile al seguente:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > È anche possibile trovare l'URL dell'endpoint nel riquadro della **Panoramica** dell'app per la logica nella proprietà **URL del flusso di lavoro** .
   >
   > 1. Scegliere **Panoramica** dal menu risorsa.
   > 1. Nel riquadro **Panoramica** trovare la proprietà **URL flusso di lavoro** .
   > 1. Per copiare l'URL dell'endpoint, spostare il puntatore oltre la fine del testo dell'URL dell'endpoint e selezionare **copia negli Appunti** (icona copia file).

1. Per testare l'URL inviando una richiesta, aprire il [post](https://www.postman.com/downloads/) o lo strumento preferito per la creazione e l'invio di richieste.

   Questo esempio continua con l'uso di postazione. Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione dei post](https://learning.postman.com/docs/getting-started/introduction/).

   1. Sulla barra degli strumenti del posto, selezionare **nuovo**.

      ![Screenshot che mostra il pulsante con il pulsante nuovo selezionato](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. Nel riquadro **Crea nuovo** , in **blocchi predefiniti**, selezionare **richiesta**.

   1. Nella finestra **Salva richiesta** , in **nome richiesta**, specificare un nome per la richiesta, ad esempio `Test workflow trigger` .

   1. In **selezionare una raccolta o una cartella in cui salvare**, selezionare **Crea raccolta**.

   1. In **tutte le raccolte** specificare un nome per la raccolta da creare per organizzare le richieste, premere invio e selezionare **Salva per <*raccolta-nome* >**. In questo esempio viene usato `Logic Apps requests` come nome della raccolta.

      Viene visualizzato il riquadro delle richieste del post, in modo che sia possibile inviare una richiesta all'URL dell'endpoint per il trigger di richiesta.

      ![Screenshot che mostra il posto con il riquadro delle richieste aperto](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. Nel riquadro delle richieste, nella casella indirizzo accanto all'elenco dei metodi, che attualmente Mostra **Get** come metodo di richiesta predefinito, incollare l'URL copiato in precedenza e selezionare **Send (Invia**).

      ![Screenshot che mostra l'URL dei post e dell'endpoint nella casella degli indirizzi con il pulsante Invia selezionato](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Quando viene attivato il trigger, il flusso di lavoro di esempio viene eseguito e invia un messaggio di posta elettronica simile all'esempio seguente:

      ![Screenshot che mostra la posta elettronica di Outlook come descritto nell'esempio](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

## <a name="review-run-history"></a>Esaminare la cronologia di esecuzione

Per un flusso di lavoro con stato, dopo l'esecuzione di ogni flusso di lavoro, è possibile visualizzare la cronologia di esecuzione, incluso lo stato dell'esecuzione complessiva, per il trigger e per ogni azione insieme ai rispettivi input e output.

1. Nella portale di Azure scegliere **monitoraggio** dal menu del flusso di lavoro.

   Il riquadro **monitoraggio** Mostra la cronologia di esecuzione per il flusso di lavoro.

   ![Screenshot che mostra il riquadro "monitoraggio" del flusso di lavoro e la cronologia di esecuzione.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Se lo stato di esecuzione più recente non viene visualizzato, sulla barra degli strumenti del riquadro **monitoraggio** selezionare **Aggiorna**. Non viene eseguita alcuna esecuzione per un trigger ignorato a causa di criteri non soddisfatti o per la ricerca di dati.

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

1. Per esaminare lo stato di ogni passaggio di un'esecuzione, selezionare l'esecuzione che si desidera esaminare.

   Viene visualizzata la visualizzazione Dettagli esecuzione e viene visualizzato lo stato di ogni passaggio nell'esecuzione.

   ![Screenshot che mostra la visualizzazione Dettagli esecuzione con lo stato di ogni passaggio nel flusso di lavoro.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   Di seguito sono riportati gli stati possibili che ogni passaggio del flusso di lavoro può avere:

   | Stato azione | Icona | Descrizione |
   |---------------|------|-------------|
   | Aborted | ![Icona per lo stato dell'azione "interrotto"][aborted-icon] | L'azione è stata interrotta o non è stata completata a causa di problemi esterni, ad esempio un'interruzione del sistema o una sottoscrizione di Azure scaduta. |
   | Operazione annullata | ![Icona per lo stato dell'azione "annullato"][cancelled-icon] | L'azione è stata eseguita ma è stata ricevuta una richiesta di annullamento. |
   | Non riuscito | ![Icona per lo stato dell'azione "non riuscito"][failed-icon] | L'azione non è riuscita. |
   | In esecuzione | ![Icona per lo stato dell'azione "in esecuzione"][running-icon] | L'azione è attualmente in esecuzione. |
   | Operazione ignorata | ![Icona per lo stato dell'azione "ignorato"][skipped-icon] | L'azione è stata ignorata perché l'azione immediatamente precedente non è riuscita. Un'azione ha una `runAfter` condizione che richiede che l'azione precedente venga completata correttamente prima di poter eseguire l'azione corrente. |
   | Completato | ![Icona per lo stato dell'azione "succeeded"][succeeded-icon] | L'azione è riuscita. |
   | Operazione completata con nuovi tentativi | ![Icona per lo stato dell'azione "riuscito con ripetizione dei tentativi"][succeeded-with-retries-icon] | L'azione è stata completata ma solo dopo uno o più tentativi. Per esaminare la cronologia dei tentativi, nella visualizzazione dei dettagli della cronologia di esecuzione selezionare l'azione in modo che sia possibile visualizzare gli input e gli output. |
   | Timeout | ![Icona per lo stato dell'azione "timeout"][timed-out-icon] | L'azione è stata interrotta a causa del limite di timeout specificato dalle impostazioni di tale azione. |
   | Attesa | ![Icona per lo stato dell'azione "in attesa"][waiting-icon] | Si applica a un'azione webhook che è in attesa di una richiesta in ingresso da un chiamante. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Per esaminare gli input e gli output per un passaggio specifico, selezionare il passaggio.

   ![Screenshot che Mostra gli input e gli output dell'azione "Invia un messaggio di posta elettronica" selezionato.](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Per esaminare ulteriormente gli input e gli output non elaborati per tale passaggio, selezionare **Mostra input non elaborati** o **Mostra output** non elaborati.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Abilita o Apri Application Insights dopo la distribuzione

Durante l'esecuzione del flusso di lavoro, l'app per la logica emette la telemetria insieme ad altri eventi. È possibile usare questi dati di telemetria per ottenere una migliore visibilità sull'esecuzione del flusso di lavoro e sul funzionamento del runtime di app per la logica in diversi modi. È possibile monitorare il flusso di lavoro usando [Application Insights](../azure-monitor/app/app-insights-overview.md), che fornisce dati di telemetria quasi in tempo reale (metriche attive). Questa funzionalità consente di esaminare più facilmente gli errori e i problemi di prestazioni quando si utilizzano questi dati per diagnosticare i problemi, impostare gli avvisi e creare grafici.

Se le impostazioni di creazione e distribuzione dell'app per la logica supportano l'uso di [Application Insights](../azure-monitor/app/app-insights-overview.md), è possibile abilitare facoltativamente la registrazione e la traccia di diagnostica per l'app per la logica. Questa operazione può essere eseguita quando si crea l'app per la logica nel portale di Azure o dopo la distribuzione. È necessario disporre di un'istanza di Application Insights, ma è possibile creare questa risorsa [in anticipo](../azure-monitor/app/create-workspace-resource.md), quando si crea l'app per la logica o dopo la distribuzione.

Per abilitare Application Insights in un'app per la logica distribuita o aprire il Dashboard Application Insights se è già abilitato, attenersi alla procedura seguente:

1. Nella portale di Azure individuare l'app per la logica distribuita.

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **Application Insights**.

1. Se Application Insights non è abilitato, nel riquadro **Application Insights** selezionare **attiva Application Insights**. Dopo aver aggiornato il riquadro, nella parte inferiore selezionare **applica**.

   Se Application Insights è abilitato, nel riquadro **Application Insights** selezionare **Visualizza dati Application Insights**.

Dopo l'apertura di Application Insights, è possibile esaminare varie metriche per l'app per la logica.

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Abilitare la cronologia di esecuzione per i flussi di lavoro senza stato

Per eseguire il debug di un flusso di lavoro senza stato in modo più semplice, è possibile abilitare la cronologia di esecuzione per il flusso di lavoro e quindi disabilitare la cronologia di esecuzione al termine dell'operazione. Seguire questa procedura per la portale di Azure o, se si lavora in Visual Studio Code, vedere [creare flussi di lavoro con stato e senza stato in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

1. Nella [portale di Azure](https://portal.azure.com)trovare e aprire la risorsa dell' **app per la logica (anteprima)** .

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **configurazione**.

1. Nella scheda **Impostazioni applicazione** selezionare **nuova impostazione applicazione**.

1. Nella casella **nome** del riquadro **Aggiungi/modifica impostazione applicazione** immettere il nome dell'opzione operazione: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Nella casella **valore** immettere il valore seguente: `WithStatelessRunHistory`

   Esempio:

   ![Screenshot che mostra la risorsa portale di Azure e app per la logica (anteprima) con il riquadro "configurazione" > "nuova impostazione applicazione" < "Aggiungi/modifica impostazione applicazione" e i flussi di lavoro. {yourWorkflowName}. OperationOptions "opzione impostata su" WithStatelessRunHistory ".](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Per completare questa attività, fare clic su **OK**. Nella barra degli strumenti del riquadro di **configurazione** selezionare **Salva**.

1. Per disabilitare la cronologia di esecuzione al termine, impostare la `Workflows.{yourWorkflowName}.OperationOptions` proprietà su `None` oppure eliminare la proprietà e il relativo valore.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Elimina elementi dalla finestra di progettazione

Per eliminare un elemento del flusso di lavoro dalla finestra di progettazione, eseguire una di queste operazioni:

* Selezionare l'elemento, aprire il menu di scelta rapida dell'elemento (MAIUSC + F10) e selezionare **Elimina**. Per confermare, scegliere **OK**.

* Selezionare l'elemento e premere il tasto CANC. Per confermare, scegliere **OK**.

* Selezionare l'elemento in modo che il riquadro dei dettagli venga aperto per l'elemento. Nell'angolo superiore destro del riquadro aprire il menu con i puntini di sospensione (**..**.) e selezionare **Elimina**. Per confermare, scegliere **OK**.

  ![Screenshot che mostra un elemento selezionato nella finestra di progettazione con il riquadro dettagli aperto più il pulsante con i puntini di sospensione selezionati e il comando "Elimina".](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Se il menu dei puntini di sospensione non è visibile, espandere la finestra del browser in modo da visualizzare il pulsante con i puntini di sospensione (**..**.) nell'angolo superiore destro.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Risolvere i problemi e gli errori

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Nuovi trigger e azioni mancanti dal selettore della finestra di progettazione per i flussi di lavoro creati in precedenza

L'anteprima di app per la logica di Azure supporta azioni predefinite per operazioni di funzioni di Azure, operazioni Liquid e operazioni XML, ad esempio la **convalida XML** e la **trasformazione XML**. Tuttavia, per le app per la logica create in precedenza, queste azioni potrebbero non essere visualizzate nella finestra di progettazione per poter selezionare se l'app per la logica usa una versione obsoleta del bundle di estensione `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Per risolvere il problema, seguire questa procedura per eliminare la versione obsoleta in modo che il bundle di estensione possa essere aggiornato automaticamente alla versione più recente.

> [!NOTE]
> Questa soluzione specifica è valida solo per le risorse di app per la **logica (anteprima)** create usando il portale di Azure, non le app per la logica create e distribuite usando Visual Studio Code e l'estensione app per la logica di Azure (anteprima). Vedere [trigger e azioni supportati mancanti dalla finestra di progettazione in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. Nel portale di Azure arrestare l'app per la logica.

   1. Selezionare **Panoramica** dal menu dell'app per la logica.

   1. Nella barra degli strumenti del riquadro **Panoramica** selezionare **Arresta**.

1. Nel menu dell'app per la logica, in **strumenti di sviluppo**, selezionare **strumenti avanzati**.

1. Nel riquadro **Advanced Tools (strumenti avanzati** ) selezionare **Go (Vai**), che consente di aprire l'ambiente Kudu per l'app per la logica.

1. Sulla barra degli strumenti Kudu aprire il menu della **console di debug** e selezionare **cmd**. 

   Viene visualizzata una finestra della console in cui è possibile passare alla cartella Bundle usando il prompt dei comandi. In alternativa, è possibile esplorare la struttura di directory visualizzata nella finestra della console.

1. Passare alla cartella seguente, che contiene le cartelle con versione per il bundle esistente:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Eliminare la cartella Version per il bundle esistente. Nella finestra della console è possibile eseguire questo comando dove si sostituisce `{bundle-version}` con la versione esistente:

   `rm -rf {bundle-version}`

   ad esempio `rm -rf 1.1.3`

   > [!TIP]
   > Se viene ricevuto un errore, ad esempio "autorizzazione negata" o "file in uso", aggiornare la pagina nel browser e ripetere i passaggi precedenti fino a quando la cartella non viene eliminata.

1. Nella portale di Azure tornare alla pagina **Panoramica** dell'app per la logica e selezionare **Riavvia**.

   Il portale ottiene e usa automaticamente il bundle più recente.

## <a name="next-steps"></a>Passaggi successivi

Vorremmo ricevere informazioni sulle tue esperienze con questa anteprima pubblica.

* Per bug o problemi, [creare i problemi in GitHub](https://github.com/Azure/logicapps/issues).
* Per domande, richieste, commenti e altri commenti, [usare questo modulo di feedback](https://aka.ms/lafeedback).
