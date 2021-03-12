---
title: Panoramica di App per la logica di Azure (anteprima)
description: App per la logica di Azure Preview è una soluzione cloud per la creazione di flussi di lavoro automatizzati, a tenant singolo, con stato e senza stato che integrano app, dati, servizi e sistemi con codice minimo per scenari di livello aziendale.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 7120b6ff17657232c0e614f49b75bb24263712b7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636336"
---
# <a name="overview-azure-logic-apps-preview"></a>Panoramica: anteprima di app per la logica di Azure

> [!IMPORTANT]
> Questa funzionalità è in anteprima pubblica, viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Con l'anteprima di app per la logica di Azure puoi creare soluzioni di automazione e integrazione tra app, dati, servizi cloud e sistemi creando ed eseguendo app per la logica a tenant singolo con il nuovo tipo di risorsa app per la **logica (anteprima)** . Con questo tipo di app per la logica a tenant singolo, è in grado di compilare più flussi di lavoro con [ *stato* e senza  stato](#stateful-stateless) basati sul runtime di anteprima di app per la logica di Azure riprogettato, che offre portabilità, migliori prestazioni e flessibilità per la distribuzione e l'esecuzione in diversi ambienti host, tra cui non solo Azure, ma anche contenitori docker.

Come è possibile? Il runtime riprogettato usa il [modello di estendibilità di funzioni di Azure](../azure-functions/functions-bindings-register.md) ed è ospitato come estensione nel runtime di funzioni di Azure. Questa architettura significa che è possibile eseguire il tipo di app per la logica a tenant singolo ovunque sia in esecuzione funzioni di Azure. È possibile ospitare il runtime riprogettato in quasi tutte le topologie di rete e scegliere le dimensioni di calcolo disponibili per gestire il carico di lavoro necessario per i flussi di lavoro. Per altre informazioni, vedere [Introduzione a funzioni di Azure](../azure-functions/functions-overview.md) e [trigger e associazioni di funzioni di Azure](../azure-functions/functions-triggers-bindings.md).

È possibile creare la risorsa app per la **logica (anteprima)** [avviando la portale di Azure](create-stateful-stateless-workflows-azure-portal.md) o [creando un progetto in Visual Studio Code con l'estensione app per la logica di Azure (anteprima)](create-stateful-stateless-workflows-visual-studio-code.md). Inoltre, in Visual Studio Code, è possibile compilare *ed eseguire localmente* i flussi di lavoro nell'ambiente di sviluppo. Indipendentemente dal fatto che si usi il portale o Visual Studio Code, è possibile distribuire ed eseguire il tipo di app per la logica a tenant singolo negli stessi tipi di ambienti host.

In questa panoramica vengono illustrate le aree seguenti:

* [Differenze tra l'anteprima di app per la logica di Azure, l'ambiente multi-tenant di app per la logica di Azure e l'ambiente del servizio di integrazione](#preview-differences).

* [Differenze tra flussi di lavoro con stato e senza stato](#stateful-stateless), incluse le differenze di comportamento tra [flussi di lavoro con stato annidato e senza stato](#nested-behavior).

* [Funzionalità di questa versione di anteprima pubblica](#public-preview-contents).

* Funzionamento [del modello di determinazione dei prezzi](#pricing-model).

* [Funzionalità modificate, limitate, non disponibili o non supportate](#limited-unavailable-unsupported).

* [Limiti nell'anteprima delle app per la logica di Azure](#limits).

Per ulteriori informazioni, vedere gli argomenti seguenti:

* [App per la logica di Azure in esecuzione da qualsiasi luogo-esecuzione approfondita](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Problemi noti di anteprima pubblica di app per la logica (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Differenze tra le app per la logica di Azure e l'anteprima

Il runtime di anteprima di app per la logica di Azure usa l'estendibilità di [funzioni di Azure](../azure-functions/functions-overview.md) ed è ospitato come estensione nel runtime di funzioni di Azure. Questa architettura significa che è possibile eseguire il tipo di app per la logica a tenant singolo ovunque sia in esecuzione funzioni di Azure. È possibile ospitare il runtime di anteprima di app per la logica di Azure in quasi tutte le topologie di rete desiderate e scegliere le dimensioni di calcolo disponibili per gestire il carico di lavoro necessario per il flusso di lavoro. Per altre informazioni sull'estendibilità di funzioni di Azure, vedere [Webjobs SDK: creazione di binding di input e output personalizzati](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

Con questo nuovo approccio, il runtime di anteprima di app per la logica di Azure e i flussi di lavoro sono entrambi parte dell'app che è possibile raggruppare. Questa funzionalità consente di distribuire ed eseguire i flussi di lavoro semplicemente copiando gli artefatti nell'ambiente host e avviando l'app. Questo approccio offre inoltre un'esperienza più standardizzata per la creazione di pipeline di distribuzione per i progetti di flusso di lavoro per l'esecuzione di test e convalide necessari prima di distribuire le modifiche negli ambienti di produzione. Per altre informazioni, vedere [app per la logica di Azure in esecuzione in un ambiente di esecuzione approfondita](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

Nella tabella seguente vengono brevemente riepilogate le differenze nel modo in cui i flussi di lavoro condividono le risorse, in base all'ambiente in cui vengono eseguiti. Per le differenze nei limiti, vedere [limiti nell'anteprima di app per la logica di Azure](#limits).

| Ambiente | Condivisione e utilizzo delle risorse |
|-------------|----------------------------------|
| App per la logica di Azure (multi-tenant) | I flussi *di lavoro dei clienti in più tenant* condividono la stessa elaborazione (calcolo), archiviazione, rete e così via. |
| App per la logica di Azure (anteprima, tenant singolo) | I flussi *di lavoro nella stessa app per la logica e un singolo tenant* condividono la stessa elaborazione (calcolo), archiviazione, rete e così via. |
| Ambiente del servizio di integrazione (non disponibile in anteprima) | I flussi di lavoro nello *stesso ambiente* condividono la stessa elaborazione (calcolo), archiviazione, rete e così via. |
|||

Nel frattempo, è comunque possibile creare il tipo di app per la logica multi-tenant nel portale di Azure e in Visual Studio Code usando l'estensione multi-tenant di app per la logica di Azure. Sebbene le esperienze di sviluppo differiscano tra i tipi di app per la logica multi-tenant e a tenant singolo, la sottoscrizione di Azure può includere entrambi i tipi. È possibile visualizzare e accedere a tutte le app per la logica distribuite nella sottoscrizione di Azure, ma le app sono organizzate nelle rispettive categorie e sezioni.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Flussi di lavoro con stato e senza stato

Con il tipo di app per la logica a tenant singolo, è possibile creare questi tipi di flusso di lavoro all'interno della stessa app per la logica:

* *Con stato*

  Creare flussi di lavoro con stato quando è necessario tenere, rivedere o fare riferimento ai dati degli eventi precedenti. Questi flussi di lavoro salvano gli input e gli output per ogni azione e i relativi stati in un archivio esterno, che consente di esaminare i dettagli e la cronologia delle esecuzioni al termine di ogni esecuzione. I flussi di lavoro con stato garantiscono una resilienza elevata se si verificano interruzioni. Una volta ripristinati i servizi e i sistemi, è possibile ricostruire le esecuzioni interrotte dallo stato salvato e rieseguire i flussi di lavoro fino al completamento. I flussi di lavoro con stato possono continuare a funzionare per un massimo di un anno.

* *Senza stato*

  Creare flussi di lavoro senza stato quando non è necessario salvare, rivedere o fare riferimento ai dati degli eventi precedenti nell'archivio esterno per una revisione successiva. Questi flussi di lavoro salvano gli input e gli output per ogni azione e i relativi stati *solo in memoria*, anziché trasferire questi dati in una risorsa di archiviazione esterna. Di conseguenza, i flussi di lavoro senza stato hanno esecuzioni più brevi che in genere non durano più di 5 minuti, prestazioni più veloci con tempi di risposta più rapidi, velocità effettiva più elevata e costi di esecuzione ridotti perché i dettagli e la cronologia dell'esecuzione non vengono conservati nell'archiviazione esterna. Tuttavia, se si verificano interruzioni, le esecuzioni interrotte non vengono ripristinate automaticamente, quindi il chiamante deve inviare nuovamente le esecuzioni interrotte manualmente. Questi flussi di lavoro possono essere eseguiti solo in modo sincrono.

  Per semplificare il debug, è possibile abilitare la cronologia di esecuzione per un flusso di lavoro senza stato, che influisca sulle prestazioni e quindi disabilitare la cronologia di esecuzione al termine dell'operazione. Per altre informazioni, vedere [creare flussi di lavoro con stato e senza stato in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) o [creare flussi di lavoro con stato e senza stato nel portale di Azure](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > I flussi di lavoro senza stato attualmente supportano solo *azioni* per i [connettori gestiti](../connectors/apis-list.md#managed-api-connectors), distribuiti in Azure e non trigger. Per avviare il flusso di lavoro, selezionare la [richiesta predefinita, gli hub eventi o il trigger del bus di servizio](../connectors/apis-list.md#built-ins). Questi trigger vengono eseguiti in modalità nativa nel runtime di anteprima di app per la logica di Azure. Per ulteriori informazioni sui trigger, le azioni e i connettori limitati, non disponibili o non supportati, vedere [funzionalità modificate, limitate, non disponibili o non supportate](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Differenze di comportamento annidate tra flussi di lavoro con stato e senza stato

È possibile [rendere un flusso di lavoro chiamabile](../logic-apps/logic-apps-http-endpoint.md) da altri flussi di lavoro presenti nella stessa risorsa app per la **logica (anteprima)** usando il [trigger di richiesta](../connectors/connectors-native-reqres.md), il [trigger http webhook](../connectors/connectors-native-webhook.md)o i trigger del connettore gestito con il [tipo ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e possono ricevere richieste HTTPS.

Ecco i modelli di comportamento che possono seguire i flussi di lavoro annidati dopo che un flusso di lavoro padre chiama un flusso di lavoro figlio:

* Modello di polling asincrono

  L'elemento padre non attende una risposta alla chiamata iniziale, ma controlla costantemente la cronologia di esecuzione del figlio fino a quando l'esecuzione del figlio non viene completata. Per impostazione predefinita, i flussi di lavoro con stato seguono questo modello, ideale per i flussi di lavoro figlio con esecuzione prolungata che potrebbero superare i [limiti di timeout della richiesta](../logic-apps/logic-apps-limits-and-config.md).

* Modello sincrono ("Fire and Forget")

  Il figlio riconosce la chiamata restituendo immediatamente una `202 ACCEPTED` risposta e l'elemento padre continua a eseguire l'azione successiva senza attendere i risultati dell'elemento figlio. Al contrario, l'elemento padre riceve i risultati al termine dell'esecuzione dell'elemento figlio. I flussi di lavoro con stato figlio che non includono un'azione di risposta seguono sempre il modello sincrono. Per i flussi di lavoro con stato figlio, la cronologia di esecuzione è disponibile per la revisione.

  Per abilitare questo comportamento, nella definizione JSON del flusso di lavoro impostare la `operationOptions` proprietà su `DisableAsyncPattern` . Per ulteriori informazioni, vedere [trigger e tipi di azione-opzioni operazione](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Trigger e Wait

  Per un flusso di lavoro senza stato figlio, l'elemento padre attende una risposta che restituisce i risultati dell'elemento figlio. Questo modello funziona in modo analogo all'uso del [trigger http o dell'azione](../connectors/connectors-native-http.md) incorporata per chiamare un flusso di lavoro figlio. I flussi di lavoro senza stato figlio che non includono un'azione di risposta restituiscono immediatamente una `202 ACCEPTED` risposta, ma il padre attende il completamento del figlio prima di continuare con l'azione successiva. Questi comportamenti si applicano solo ai flussi di lavoro senza stato figlio.

Questa tabella specifica il comportamento del flusso di lavoro figlio a seconda che l'elemento padre e l'elemento figlio siano con stato, senza stato o come tipi di flussi di lavoro misti:

| Flusso di lavoro padre | Flusso di lavoro figlio | Comportamento figlio |
|-----------------|----------------|----------------|
| Con stato | Con stato | Impostazione asincrona o sincrona con `"operationOptions": "DisableAsyncPattern"` |
| Con stato | Senza stato | Trigger e Wait |
| Senza stato | Con stato | Sincrono |
| Senza stato | Senza stato | Trigger e Wait |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Funzionalità

L'anteprima di app per la logica di Azure include molte funzionalità correnti e aggiuntive, ad esempio:

* Crea app per la logica e i relativi flussi di lavoro da più di [400 connettori](/connectors/connector-reference/connector-reference-logicapps-connectors) per applicazioni SaaS (software-as-a-Service) e PaaS (Platform-as-a-Service) e per i servizi e i connettori per i sistemi locali.

  * Alcuni connettori gestiti sono ora disponibili come versioni predefinite, che vengono eseguite in modo analogo ai trigger e alle azioni predefiniti, ad esempio il trigger di richiesta e l'azione HTTP, eseguiti in modo nativo nel runtime di anteprima di app per la logica di Azure. Ad esempio, questi nuovi connettori predefiniti includono il bus di servizio di Azure, Hub eventi di Azure, SQL Server e MQ.

    > [!NOTE]
    > Per il connettore SQL Server incorporato, solo l'azione **Esegui query** può connettersi direttamente alle reti virtuali di Azure senza richiedere il [gateway dati locale](logic-apps-gateway-connection.md).

  * Creare connettori predefiniti per tutti i servizi necessari usando il [Framework di estendibilità della versione di anteprima](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Analogamente ai connettori predefiniti, ad esempio il bus di servizio di Azure e SQL Server, ma a differenza dei [connettori personalizzati](../connectors/apis-list.md#custom-apis-and-connectors) che non sono attualmente supportati per l'anteprima, questi connettori forniscono velocità effettiva più elevata, bassa latenza, connettività locale ed esecuzione nativa nello stesso processo del runtime di anteprima.

    La funzionalità di creazione è attualmente disponibile solo in Visual Studio Code, ma non è abilitata per impostazione predefinita. Per creare questi connettori, [cambiare il progetto dall'estensione basata su Bundle (Node.js) a NuGet basato su pacchetti (.NET)](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring). Per altre informazioni, vedere [app per la logica di Azure che esegue l'estensibilità dei connettori incorporati in qualsiasi](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)posizione.

  * È possibile usare le azioni B2B per operazioni liquide e operazioni XML senza un account di integrazione. Per utilizzare queste azioni, è necessario disporre di mappe liquide, mappe XML o XML Schema che è possibile caricare mediante le rispettive azioni nella portale di Azure o aggiungere alla cartella **elementi** del progetto Visual Studio Code utilizzando le rispettive cartelle **mappe** e **schemi** .

  * Creare e distribuire app per la logica che possono essere eseguite ovunque, perché il servizio app per la logica di Azure genera stringhe di connessione di firma di accesso condiviso che possono essere usate da queste app per la logica per l'invio di richieste all'endpoint di runtime della connessione cloud. Il servizio app per la logica Salva queste stringhe di connessione con altre impostazioni dell'applicazione in modo che sia possibile archiviare facilmente questi valori in Azure Key Vault durante la distribuzione in Azure.

    > [!NOTE]
    > Per impostazione predefinita, una risorsa di app per la **logica (anteprima)** ha l' [identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md) abilitata automaticamente per autenticare le connessioni in fase di esecuzione. Questa identità è diversa dalle credenziali di autenticazione o dalla stringa di connessione utilizzata per la creazione di una connessione. Se questa identità viene disabilitata, le connessioni non funzioneranno in fase di esecuzione. Per visualizzare questa impostazione, nel menu dell'app per la logica, in **Impostazioni**, selezionare **Identity (identità**).

* Crea app per la logica con flussi di lavoro senza stato che vengono eseguiti solo in memoria, in modo che vengano completati più rapidamente, Rispondi più velocemente, con una velocità effettiva più elevata e costi inferiori per l'esecuzione perché le cronologie di esecuzione e i dati tra le azioni non vengono mantenuti nell'archiviazione esterna Facoltativamente, è possibile abilitare la cronologia di esecuzione per semplificare il debug. Per altre informazioni, vedere [app](#stateful-stateless)per la logica con stato e senza stato.

* Eseguire localmente, testare ed eseguire il debug delle app per la logica e dei relativi flussi di lavoro nell'ambiente di sviluppo Visual Studio Code.

  Prima di eseguire e testare l'app per la logica, è possibile semplificare il debug aggiungendo e usando i punti di interruzione all'interno del **workflow.jssu** file per un flusso di lavoro. Tuttavia, i punti di interruzione sono supportati solo per le azioni in questo momento, non per i trigger. Per altre informazioni, vedere [creare flussi di lavoro con stato e senza stato in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Pubblicare o distribuire direttamente app per la logica e i relativi flussi di lavoro da Visual Studio Code a diversi ambienti host, ad esempio Azure e [contenitori Docker](/dotnet/core/docker/introduction).

* Abilitare la registrazione diagnostica e le funzionalità di traccia per l'app per la logica usando [Application Insights](../azure-monitor/app/app-insights-overview.md) quando supportato dalla sottoscrizione di Azure e dalle impostazioni dell'app per la logica.

* È possibile accedere alle funzionalità di rete, ad esempio la connessione e l'integrazione privata con le reti virtuali di Azure, in modo analogo alle funzioni di Azure quando si creano e si distribuiscono app per la logica usando il [piano Premium di funzioni](../azure-functions/functions-premium-plan.md) Per ulteriori informazioni, vedere gli argomenti seguenti:

  * [Opzioni di rete di Funzioni di Azure](../azure-functions/functions-networking-options.md)

  * [App per la logica di Azure in esecuzione ovunque e possibilità di rete con app per la logica di Azure Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Rigenerare le chiavi di accesso per le connessioni gestite usate dai singoli flussi di lavoro nella risorsa app per la logica a tenant singolo **(anteprima)** . Per questa attività, [seguire la stessa procedura per la risorsa app per la **logica** multi-tenant, ma a livello di singolo flusso di lavoro](logic-apps-securing-a-logic-app.md#regenerate-access-keys), non a livello di risorsa dell'app per la logica.

* Aggiungere rami paralleli nella finestra di progettazione a tenant singolo attenendosi alla stessa procedura della finestra di progettazione multi-tenant.

Per altre informazioni, vedere la pagina relativa alle [funzionalità modificate, limitate, non disponibili e non supportate](#limited-unavailable-unsupported) e alla [pagina problemi noti dell'anteprima pubblica di app per la logica in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modello di prezzi

Quando si crea il tipo di app per la logica a tenant singolo nel portale di Azure o si distribuisce da Visual Studio Code, è necessario scegliere un piano di hosting, ovvero [servizio app o Premium](../azure-functions/functions-scale.md), per l'app per la logica da usare. Questo piano determina il modello di determinazione dei prezzi applicabile all'esecuzione dell'app per la logica. Se si seleziona il piano di servizio app, è necessario anche scegliere un piano [tariffario](../app-service/overview-hosting-plans.md).

I flussi di lavoro con *stato* usano l' [archiviazione esterna](../azure-functions/storage-considerations.md#storage-account-requirements), quindi i [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) si applicano alle transazioni di archiviazione eseguite dal runtime di anteprima di app per la logica di Azure Ad esempio, le code vengono utilizzate per la pianificazione, mentre le tabelle e i BLOB vengono utilizzati per l'archiviazione degli Stati del flusso di lavoro.

> [!NOTE]
> Durante l'anteprima pubblica, l'esecuzione di app per la logica nel servizio app non comporta costi *aggiuntivi* oltre il piano selezionato.

Per ulteriori informazioni sui modelli di determinazione prezzi applicabili al tipo di risorsa a tenant singolo, esaminare gli argomenti seguenti:

* [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)
* [Aumentare le prestazioni di un'app nel servizio app di Azure](../app-service/manage-scale-up.md)
* [Dettagli prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/)
* [Dettagli prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/)
* [Dettagli prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Funzionalità modificate, limitate, non disponibili o non supportate

Nell'anteprima di app per la logica di Azure queste funzionalità sono state modificate o sono attualmente limitate, non disponibili o non supportate:

* **Supporto del sistema operativo**: attualmente, la finestra di progettazione in Visual Studio Code non funziona in un sistema operativo Linux, ma è comunque possibile distribuire app per la logica che usano il runtime di anteprima delle app per la logica in macchine virtuali basate su Linux. Per il momento è possibile compilare le app per la logica in Visual Studio Code in Windows o macOS e quindi distribuirle in una macchina virtuale basata su Linux.

* **Trigger e azioni**: i trigger e le azioni predefiniti vengono eseguiti in modo nativo nel runtime di anteprima di app per la logica di Azure, mentre i connettori gestiti vengono distribuiti in Azure. Alcuni trigger predefiniti non sono disponibili, ad esempio finestra temporale scorrevole e batch.

  Per avviare il flusso di lavoro, usare la [ricorrenza, la richiesta, il http, il webhook http, l'hub eventi o il trigger del bus di servizio predefiniti](../connectors/apis-list.md). Nella finestra di progettazione, i trigger e le azioni predefiniti vengono visualizzati nella scheda **incorporata** , mentre i trigger e le azioni del connettore gestito vengono visualizzati nella scheda **Azure** .

  > [!NOTE]
  > Per eseguire localmente in Visual Studio Code, i trigger e le azioni basati su webhook richiedono una configurazione aggiuntiva. Per altre informazioni, vedere [creare flussi di lavoro con stato e senza stato in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * Per i *flussi di lavoro* senza stato, la scheda **Azure** non viene visualizzata quando si seleziona un trigger perché è possibile selezionare solo le [ *azioni* del connettore gestito, non i trigger](../connectors/apis-list.md#managed-api-connectors). Sebbene sia possibile abilitare i connettori gestiti distribuiti da Azure per i flussi di lavoro senza stato, la finestra di progettazione non mostra alcun trigger del connettore gestito da aggiungere.

  * Per i *flussi di lavoro con stato*, ad eccezione dei trigger e delle azioni elencati di seguito, è possibile usare sia i trigger che le azioni del [connettore gestito](../connectors/apis-list.md#managed-api-connectors) .

  * Questi trigger e azioni sono stati modificati o sono attualmente limitati, non supportati o non disponibili:

    * I [ *trigger* del gateway dati locale](../connectors/apis-list.md#on-premises-connectors) non sono disponibili, ma *sono* disponibili azioni del gateway.

    * Azione incorporata, funzioni di Azure [-scegliere una funzione di Azure](logic-apps-azure-functions.md) è ora **operazioni di funzioni di Azure: chiamare una funzione di Azure**. Questa azione attualmente funziona solo per le funzioni create dal modello di **trigger http** .

      Nella portale di Azure è possibile selezionare una funzione trigger HTTP a cui si ha accesso creando una connessione tramite l'esperienza utente. Se si esamina la definizione JSON dell'azione della funzione nella visualizzazione codice o nel **workflow.js** file, l'azione fa riferimento alla funzione utilizzando un `connectionName` riferimento. Questa versione astrae le informazioni della funzione come una connessione, che è possibile trovare nel file **connections.js** del progetto, disponibile dopo la creazione di una connessione.

      > [!NOTE]
      > Nella versione a tenant singolo l'azione della funzione supporta solo l'autenticazione della stringa di query. App per la logica di Azure Preview ottiene la chiave predefinita dalla funzione quando si effettua la connessione, archivia tale chiave nelle impostazioni dell'app e usa la chiave per l'autenticazione quando si chiama la funzione.
      >
      > Come per la versione multi-tenant, se si rinnova questa chiave, ad esempio tramite l'esperienza funzioni di Azure nel portale, l'azione funzione non funziona più a causa della chiave non valida. Per risolvere il problema, è necessario ricreare la connessione alla funzione che si vuole chiamare o aggiornare le impostazioni dell'app con la nuova chiave.

    * L'azione predefinita, il codice [inline-Execute JavaScript code](logic-apps-add-run-inline-code.md) è ora **operazioni di codice inline: Esegui JavaScript** inline.

      * Le azioni per operazioni di codice inline non richiedono più un account di integrazione.

      * Per macOS e Linux, **le operazioni di codice inline** sono ora supportate quando si usa l'estensione app per la logica di Azure (anteprima) in Visual Studio Code.

      * Non è più necessario riavviare l'app per la logica se si apportano modifiche in un'azione per **operazioni di codice inline** .

      * **Le azioni per le operazioni di codice inline** hanno [limiti aggiornati](logic-apps-overview-preview.md#inline-code-limits).

    * Alcuni [trigger e azioni B2B predefiniti per gli account di integrazione](../connectors/apis-list.md#integration-account-connectors) non sono disponibili, ad esempio, le azioni di codifica e decodifica di **file flat** .

    * Azione incorporata, app per [la logica di Azure-scegliere un flusso di lavoro dell'app per la logica](logic-apps-http-endpoint.md) è ora **operazioni del flusso di lavoro: richiamare un flusso di lavoro in questa app flusso di lavoro**

* I [connettori personalizzati](../connectors/apis-list.md#custom-apis-and-connectors) non sono attualmente supportati per l'anteprima.

* **Disponibilità del piano di hosting**: se si crea il tipo di risorsa app per la logica a tenant singolo **(anteprima)** nell'portale di Azure o si esegue la distribuzione da Visual Studio Code, è possibile usare solo il piano di hosting del servizio app o Premium in Azure. I piani di hosting a consumo non sono disponibili e non sono supportati per la distribuzione di questo tipo di risorsa. È possibile eseguire la distribuzione da Visual Studio Code a un contenitore Docker, ma non a un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

* **Debug dei punti di interruzione in Visual Studio Code**: Sebbene sia possibile aggiungere e utilizzare i punti di interruzione all'interno del **workflow.jssu** file per un flusso di lavoro, i punti di interruzione sono supportati solo per le azioni in questo momento, non per i trigger. Per altre informazioni, vedere [creare flussi di lavoro con stato e senza stato in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* **Controllo zoom**: il controllo zoom non è attualmente disponibile nella finestra di progettazione.

* Cronologia di **attivazione e cronologia di esecuzione**: per il tipo di risorsa app per la **logica (anteprima)** , la cronologia dei trigger e la cronologia di esecuzione nel portale di Azure vengono visualizzati a livello di flusso di lavoro, non a livello di app per la logica Per trovare i dati cronologici, attenersi alla procedura seguente:

   * Per visualizzare la cronologia di esecuzione, aprire il flusso di lavoro nell'app per la logica. Nel menu flusso di lavoro, in **sviluppatore** selezionare **monitoraggio**.

   * Per esaminare la cronologia dei trigger, aprire il flusso di lavoro nell'app per la logica. Nel menu flusso di lavoro, in **sviluppatore** selezionare **cronologia trigger**.

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Consentire il traffico in scenari di rete e firewall rigorosi

Se l'ambiente presenta requisiti di rete rigidi o firewall che limitano il traffico, è necessario consentire l'accesso per qualsiasi connessione trigger o azione nei flussi di lavoro delle app per la logica.

Per trovare i nomi di dominio completi (FQDN) per queste connessioni, esaminare le sezioni corrispondenti in questi argomenti:

* [Autorizzazioni del firewall per app per la logica a tenant singolo-Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Autorizzazioni del firewall per app per la logica a tenant singolo-portale di Azure](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Limiti aggiornati

Sebbene molti limiti per le app per la logica di Azure siano identici ai [limiti per le app per la logica di Azure multi-tenant](logic-apps-limits-and-config.md), questi limiti sono stati modificati per l'anteprima di app per la logica di Azure.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Limiti di timeout HTTP

Per una singola chiamata in ingresso o in uscita, il limite di timeout è di 230 secondi (3,9 minuti) per i trigger e le azioni seguenti:

* Richiesta in uscita: trigger HTTP, azione HTTP
* Richiesta in ingresso: trigger di richiesta, trigger HTTP webhook, azione webhook HTTP

In confronto, di seguito sono riportati i limiti di timeout per questi trigger e azioni in altri ambienti in cui vengono eseguite le app per la logica e i relativi flussi di lavoro:

* App per la logica di Azure multi-tenant: 120 secondi (2 minuti)
* Ambiente del servizio di integrazione: 240 secondi (4 minuti)

Per ulteriori informazioni, vedere la pagina relativa ai [limiti http](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Connettori gestiti

I connettori gestiti sono limitati a 50 richieste al minuto per connessione. Per risolvere i problemi relativi alla limitazione del connettore, vedere [gestire i problemi di limitazione delle richieste (429-"numero eccessivo di richieste") nelle app per la logica di Azure](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Operazioni di codice inline (eseguire codice JavaScript)

Per una singola definizione di app per la logica, l'azione operazioni di codice inline, [**Esegui codice JavaScript**](logic-apps-add-run-inline-code.md), presenta questi limiti aggiornati:

* Il numero massimo di caratteri di codice aumenta da 1.024 a 100.000 caratteri.

* La durata massima per l'esecuzione del codice aumenta da cinque secondi a 15 secondi.

Per altre informazioni, vedere [limiti delle definizioni delle app](logic-apps-limits-and-config.md#definition-limits)per la logica.

## <a name="next-steps"></a>Passaggi successivi

* [Creare flussi di lavoro con stato e senza stato nel portale di Azure](create-stateful-stateless-workflows-azure-portal.md)
* [Creare flussi di lavoro con stato e senza stato in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Pagina dei problemi noti dell'anteprima pubblica di app per la logica in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Inoltre, vorremmo ricevere informazioni sulle esperienze con le app per la logica di Azure Preview.

* Per bug o problemi, [creare i problemi in GitHub](https://github.com/Azure/logicapps/issues).
* Per domande, richieste, commenti e altri commenti, [usare questo modulo di feedback](https://aka.ms/lafeedback).
