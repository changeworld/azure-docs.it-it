---
title: Panoramica di App per la logica di Azure (anteprima)
description: App per la logica di Azure Preview è una soluzione cloud per la creazione di flussi di lavoro automatizzati, a tenant singolo, con stato e senza stato che integrano app, dati, servizi e sistemi con codice minimo per scenari a livello aziendale.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764820"
---
# <a name="overview-azure-logic-apps-preview"></a>Panoramica: App per la logica di Azure Anteprima

> [!IMPORTANT]
> Questa funzionalità è in anteprima pubblica, viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Con App per la logica di Azure Preview è possibile creare soluzioni di automazione e integrazione per app, dati, servizi cloud e sistemi creando ed eseguendo app per la logica a tenant singolo con il nuovo tipo di risorsa App per la logica **(anteprima).** L'uso di questo tipo di [   ](#stateful-stateless) app per la logica a tenant singolo consente di creare più flussi di lavoro con e senza stato basati sul runtime di App per la logica di Azure Preview riprogettato, che offre portabilità, prestazioni migliori e flessibilità per la distribuzione e l'esecuzione in vari ambienti di hosting, tra cui non solo Azure, ma anche contenitori Docker.

Come è possibile? Il runtime riprogettato usa Funzioni di Azure modello [di estendibilità](../azure-functions/functions-bindings-register.md) ed è ospitato come estensione nel runtime Funzioni di Azure runtime. Questa architettura significa che è possibile eseguire il tipo di app per la logica a tenant singolo ovunque Funzioni di Azure esecuzione. È possibile ospitare il runtime riprogettato in quasi tutte le topologie di rete e scegliere qualsiasi dimensione di calcolo disponibile per gestire il carico di lavoro necessario richiesto dai flussi di lavoro. Per altre informazioni, vedere [Introduction to Funzioni di Azure](../azure-functions/functions-overview.md) and Funzioni di Azure [triggers and bindings](../azure-functions/functions-triggers-bindings.md)(Introduzione ai trigger e alle associazioni).

È possibile creare la risorsa App per la logica **(anteprima)** a partire dal [portale di Azure](create-stateful-stateless-workflows-azure-portal.md) o creando un progetto in Visual Studio Code con l'estensione [App per la logica di Azure (anteprima).](create-stateful-stateless-workflows-visual-studio-code.md) Inoltre, in Visual Studio Code è possibile compilare ed *eseguire* in locale i flussi di lavoro nell'ambiente di sviluppo. Sia che si usi il portale o Visual Studio Code, è possibile distribuire ed eseguire il tipo di app per la logica a tenant singolo negli stessi tipi di ambienti di hosting.

Questa panoramica illustra le aree seguenti:

* [Differenze tra App per la logica di Azure Preview, l'App per la logica di Azure multi-tenant e l'ambiente del servizio di integrazione](#preview-differences).

* [Differenze tra flussi di lavoro con](#stateful-stateless)stato e senza stato, incluse le differenze di comportamento tra flussi di lavoro annidati con stato [e senza stato.](#nested-behavior)

* [Funzionalità di questa anteprima pubblica.](#public-preview-contents)

* [Funzionamento del modello tariffario](#pricing-model).

* [Funzionalità modificate, limitate, non disponibili o non supportate.](#limited-unavailable-unsupported)

* [Limiti in App per la logica di Azure Anteprima](#limits).

Per altre informazioni, vedere gli altri argomenti seguenti:

* [App per la logica di Azure esecuzione ovunque - Approfondimento sul runtime](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Problemi noti dell'anteprima pubblica delle app per la logica (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>In che modo App per la logica di Azure'anteprima?

Il runtime App per la logica di Azure Preview [usa](../azure-functions/functions-overview.md) Funzioni di Azure estendibilità ed è ospitato come estensione nel runtime Funzioni di Azure runtime. Questa architettura significa che è possibile eseguire il tipo di app per la logica a tenant singolo ovunque Funzioni di Azure in esecuzione. È possibile ospitare il runtime App per la logica di Azure Preview in quasi tutte le topologia di rete desiderate e scegliere qualsiasi dimensione di calcolo disponibile per gestire il carico di lavoro necessario necessario per il flusso di lavoro. Per altre informazioni sull Funzioni di Azure estendibilità, vedere [WebJobs SDK: Creazione di associazioni di input e output personalizzate.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

Con questo nuovo approccio, il runtime App per la logica di Azure Preview e i flussi di lavoro fanno entrambi parte dell'app che è possibile creare insieme. Questa funzionalità consente di distribuire ed eseguire i flussi di lavoro semplicemente copiando gli elementi nell'ambiente di hosting e avviando l'app. Questo approccio offre anche un'esperienza più standardizzata per la creazione di pipeline di distribuzione nei progetti del flusso di lavoro per l'esecuzione dei test e delle convalide necessari prima di distribuire le modifiche negli ambienti di produzione. Per altre informazioni, vedere App per la logica di Azure [Running Anywhere - Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

La tabella seguente riepiloga brevemente le differenze nel modo in cui i flussi di lavoro condividono le risorse, in base all'ambiente in cui vengono eseguiti. Per le differenze nei limiti, vedere [Limiti in App per la logica di Azure Preview](#limits).

| Environment | Condivisione e utilizzo delle risorse |
|-------------|----------------------------------|
| App per la logica di Azure (multi-tenant) | I *flussi di lavoro dei clienti in più tenant* condividono la stessa elaborazione (calcolo), archiviazione, rete e così via. |
| App per la logica di Azure (anteprima, tenant singolo) | I *flussi di lavoro nella stessa app per la logica e in un singolo tenant* condividono la stessa elaborazione (calcolo), archiviazione, rete e così via. |
| Ambiente del servizio di integrazione (non disponibile in anteprima) | I flussi di *lavoro nello stesso ambiente* condividono la stessa elaborazione (calcolo), archiviazione, rete e così via. |
|||

Nel frattempo, è comunque possibile creare il tipo di app per la logica multi-tenant nel portale di Azure e nel Visual Studio Code usando l'estensione App per la logica di Azure multi-tenant. Anche se le esperienze di sviluppo differiscono tra i tipi di app per la logica multi-tenant e single-tenant, la sottoscrizione di Azure può includere entrambi i tipi. È possibile visualizzare e accedere a tutte le app per la logica distribuite nella sottoscrizione di Azure, ma le app sono organizzate in categorie e sezioni specifiche.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Flussi di lavoro con stato e senza stato

Con il tipo di app per la logica a tenant singolo, è possibile creare questi tipi di flusso di lavoro all'interno della stessa app per la logica:

* *Stateful*

  Creare flussi di lavoro con stato quando è necessario mantenere, rivedere o fare riferimento ai dati degli eventi precedenti. Questi flussi di lavoro salvano gli input e gli output per ogni azione e i relativi stati nell'archiviazione esterna, che consente di esaminare i dettagli e la cronologia di esecuzione al termine di ogni esecuzione. I flussi di lavoro con stato offrono resilienza elevata in caso di interruzioni. Dopo il ripristino dei servizi e dei sistemi, è possibile ricostruire le esecuzioni interrotte dallo stato salvato ed eseguire nuovamente i flussi di lavoro fino al completamento. I flussi di lavoro con stato possono continuare a essere in esecuzione per un massimo di un anno.

* *Senza stato*

  Creare flussi di lavoro senza stato quando non è necessario salvare, rivedere o fare riferimento ai dati degli eventi precedenti nell'archiviazione esterna per una revisione successiva. Questi flussi di lavoro salvano gli input e gli output per ogni azione e i relativi stati solo *in* memoria, anziché trasferire questi dati nell'archiviazione esterna. Di conseguenza, i flussi di lavoro senza stato hanno esecuzioni più brevi che in genere non sono più lunghe di 5 minuti, prestazioni più veloci con tempi di risposta più rapidi, velocità effettiva più elevata e costi di esecuzione ridotti perché i dettagli e la cronologia dell'esecuzione non vengono mantenuti nell'archiviazione esterna. Tuttavia, se si verificano interruzioni, le esecuzioni interrotte non vengono ripristinate automaticamente, quindi il chiamante deve inviare di nuovo manualmente le esecuzioni interrotte. Questi flussi di lavoro possono essere eseguiti solo in modo sincrono.

  Per semplificare il debug, è possibile abilitare la cronologia di esecuzione per un flusso di lavoro senza stato, con un certo impatto sulle prestazioni, e quindi disabilitare la cronologia di esecuzione al termine. Per altre informazioni, vedere Creare flussi di lavoro con e senza stato [in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) o Creare flussi di lavoro con e senza stato [nel portale di Azure](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > I flussi di lavoro senza stato attualmente *supportano* solo [azioni](../connectors/managed.md)per i connettori gestiti, distribuiti in Azure e non per i trigger. Per avviare il flusso di lavoro, selezionare il [trigger predefinito Richiesta, Hub eventi o Bus di servizio.](../connectors/built-in.md) Questi trigger vengono eseguiti in modo nativo nel runtime App per la logica di Azure Preview. Per altre informazioni su trigger, azioni e connettori limitati, non disponibili o non supportati, vedere Funzionalità modificate, limitate, non disponibili [o non supportate.](#limited-unavailable-unsupported)

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Differenze di comportamento annidate tra flussi di lavoro con e senza stato

È [](../logic-apps/logic-apps-http-endpoint.md) possibile rendere un flusso di lavoro chiamabile da altri flussi di lavoro presenti nella stessa risorsa dell'app per la logica **(anteprima)** usando il trigger di richiesta, il [trigger](../connectors/connectors-native-reqres.md) [webhook HTTP](../connectors/connectors-native-webhook.md)o i trigger del connettore gestito di tipo [ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) che possono ricevere richieste HTTPS.

Ecco i modelli di comportamento che i flussi di lavoro annidati possono seguire dopo che un flusso di lavoro padre chiama un flusso di lavoro figlio:

* Modello di polling asincrono

  L'elemento padre non attende una risposta alla chiamata iniziale, ma controlla continuamente la cronologia di esecuzione dell'elemento figlio fino al termine dell'esecuzione dell'elemento figlio. Per impostazione predefinita, i flussi di lavoro con stato seguono questo modello, ideale per i flussi di lavoro figlio a esecuzione lunga che potrebbero superare i [limiti di timeout delle richieste.](../logic-apps/logic-apps-limits-and-config.md)

* Modello sincrono ("fire and forget")

  L'elemento figlio riconosce la chiamata restituisce immediatamente una risposta e l'elemento padre continua con l'azione successiva senza attendere i `202 ACCEPTED` risultati dell'elemento figlio. L'elemento padre riceve invece i risultati al termine dell'esecuzione dell'elemento figlio. I flussi di lavoro figlio con stato che non includono un'azione Response seguono sempre il modello sincrono. Per i flussi di lavoro figlio con stato, la cronologia di esecuzione è disponibile per la revisione.

  Per abilitare questo comportamento, nella definizione JSON del flusso di lavoro impostare la `operationOptions` proprietà su `DisableAsyncPattern` . Per altre informazioni, vedere [Trigger e tipi di azione - Opzioni dell'operazione.](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

* Trigger e attesa

  Per un flusso di lavoro figlio senza stato, l'elemento padre attende una risposta che restituisce i risultati dall'elemento figlio. Questo modello funziona in modo analogo all'uso del [trigger o dell'azione HTTP](../connectors/connectors-native-http.md) predefinito per chiamare un flusso di lavoro figlio. I flussi di lavoro figlio senza stato che non includono un'azione Response restituiscono immediatamente una risposta, ma l'elemento padre attende il completamento dell'elemento figlio prima di continuare con `202 ACCEPTED` l'azione successiva. Questi comportamenti si applicano solo ai flussi di lavoro figlio senza stato.

Questa tabella specifica il comportamento del flusso di lavoro figlio in base al fatto che l'elemento padre e l'elemento figlio siano tipi di flusso di lavoro con stato, senza stato o misti:

| Flusso di lavoro padre | Flusso di lavoro figlio | Comportamento figlio |
|-----------------|----------------|----------------|
| Con stato | Con stato | Asincrono o sincrono con `"operationOptions": "DisableAsyncPattern"` impostazione |
| Con stato | Senza stato | Trigger e attesa |
| Senza stato | Con stato | Sincrono |
| Senza stato | Senza stato | Trigger e attesa |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Funzionalità

App per la logica di Azure Preview include molte funzionalità correnti e aggiuntive, ad esempio:

* Creare app per la logica e i relativi flussi di lavoro da più di [400](/connectors/connector-reference/connector-reference-logicapps-connectors) connettori per app e servizi SaaS (Software-as-a-Service) e servizi PaaS (Platform-as-a-Service) e connettori per sistemi locali.

  * Alcuni connettori gestiti sono ora disponibili come versioni incorporate, che vengono eseguite in modo analogo ai trigger e alle azioni predefiniti, ad esempio il trigger di richiesta e l'azione HTTP, che vengono eseguiti in modo nativo nel runtime di App per la logica di Azure Preview. Ad esempio, questi nuovi connettori predefiniti includono bus di servizio di Azure, Hub eventi di Azure, SQL Server e MQ.

    > [!NOTE]
    > Per il connettore di SQL Server predefinito, solo l'azione Esegui query può connettersi direttamente alle reti virtuali di Azure senza richiedere il [gateway dati locale.](logic-apps-gateway-connection.md) 

  * Creare connettori predefiniti per qualsiasi servizio necessario usando il framework di [estendibilità della versione di anteprima.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) Analogamente ai connettori predefiniti, ad esempio bus di servizio di Azure e SQL Server, [](../connectors/apis-list.md#custom-apis-and-connectors) ma a differenza dei connettori personalizzati attualmente non supportati per l'anteprima, questi connettori offrono velocità effettiva superiore, bassa latenza, connettività locale ed eseguiti in modo nativo nello stesso processo del runtime di anteprima.

    La funzionalità di creazione è attualmente disponibile solo in Visual Studio Code, ma non è abilitata per impostazione predefinita. Per creare questi connettori, passare dal progetto basato su bundle di estensioni [(Node.js) a NuGet basato su pacchetti (.NET).](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring) Per altre informazioni, vedere [App per la logica di Azure running anywhere - Built-in connector extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * È possibile usare le azioni B2B per Liquid Operations e OPERAZIONI XML senza un account di integrazione. Per usare queste azioni, è necessario disporre di mappe Liquid, mappe XML o XML Schema che è possibile caricare tramite le rispettive azioni nel portale di Azure o aggiungere alla cartella **Artifacts** del progetto Visual Studio Code usando le rispettive cartelle **Maps** e **Schemas.**

  * Creare e distribuire app per la logica che possono essere eseguite ovunque perché il servizio App per la logica di Azure genera stringhe di connessione di firma di accesso condiviso che queste app per la logica possono usare per inviare richieste all'endpoint di runtime della connessione cloud. Il servizio App per la logica salva queste stringhe di connessione con altre impostazioni dell'applicazione in modo che sia possibile archiviare facilmente questi valori Azure Key Vault quando si esegue la distribuzione in Azure.

    > [!NOTE]
    > Per impostazione predefinita, per una risorsa [](../logic-apps/create-managed-service-identity.md) app per la logica **(anteprima)** l'identità gestita assegnata dal sistema viene abilitata automaticamente per autenticare le connessioni in fase di esecuzione. Questa identità è diversa dalle credenziali di autenticazione o dalla stringa di connessione usate quando si crea una connessione. Se si disabilita questa identità, le connessioni non funzioneranno in fase di esecuzione. Per visualizzare questa impostazione, nel menu dell'app per la logica in **Impostazioni** selezionare **Identità.**

* Creare app per la logica con flussi di lavoro senza stato che vengono eseguiti solo in memoria in modo che siano completate più rapidamente, risponsino più velocemente, hanno una velocità effettiva più elevata e costi inferiori per l'esecuzione perché le cronologie di esecuzione e i dati tra le azioni non vengono mantenuti nell'archiviazione esterna. Facoltativamente, è possibile abilitare la cronologia di esecuzione per semplificare il debug. Per altre informazioni, vedere Confronto tra app per la logica con stato [e senza stato.](#stateful-stateless)

* Eseguire in locale, testare ed eseguire il debug delle app per la logica e dei relativi flussi di lavoro nell Visual Studio Code di sviluppo.

  Prima di eseguire e testare l'app per la logica, è possibile semplificare il debug aggiungendo e usando punti di interruzione all'interno delworkflow.js **nel** file per un flusso di lavoro. Tuttavia, i punti di interruzione sono supportati solo per le azioni in questo momento, non per i trigger. Per altre informazioni, vedere [Creare flussi di lavoro con e senza](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)stato in Visual Studio Code .

* Pubblicare o distribuire direttamente app per la logica e i relativi flussi Visual Studio Code in vari ambienti di hosting, ad esempio azure e [contenitori Docker.](/dotnet/core/docker/introduction)

* Abilitare la registrazione diagnostica e le funzionalità di traccia per l'app per la logica [usando](../azure-monitor/app/app-insights-overview.md) Application Insights quando sono supportate dalle impostazioni della sottoscrizione di Azure e dell'app per la logica.

* Accedere alle funzionalità di rete, ad esempio connettersi e integrarsi privatamente con le reti virtuali di Azure, in modo simile a Funzioni di Azure quando si creano e distribuiscono le app per la logica usando il piano [Funzioni di Azure Premium.](../azure-functions/functions-premium-plan.md) Per altre informazioni, vedere questi argomenti:

  * [Opzioni di rete di Funzioni di Azure](../azure-functions/functions-networking-options.md)

  * [App per la logica di Azure running anywhere - Networking possibilities with App per la logica di Azure Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Rigenerare le chiavi di accesso per le connessioni gestite usate dai singoli flussi di lavoro nella risorsa **app per la logica a** tenant singolo (anteprima). Per questa attività, seguire la stessa procedura per la risorsa App per la logica [multi-tenant,  ](logic-apps-securing-a-logic-app.md#regenerate-access-keys)ma a livello di singolo flusso di lavoro, non a livello di risorsa dell'app per la logica.

* Aggiungere rami paralleli nella finestra di progettazione a tenant singolo seguendo la stessa procedura della finestra di progettazione multi-tenant.

Per altre informazioni, vedere [Funzionalità modificate, limitate, non](#limited-unavailable-unsupported) disponibili e non supportate e la pagina Problemi noti dell'anteprima pubblica di App per la [logica in GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modello di prezzi

Quando si crea il tipo di app per la logica a tenant singolo nel portale di Azure o si esegue la distribuzione da Visual Studio Code, è necessario scegliere un piano di hosting, servizio app o [Premium,](../azure-functions/functions-scale.md)per l'uso da parte dell'app per la logica. Questo piano determina il modello tariffario applicabile all'esecuzione dell'app per la logica. Se si seleziona il piano di servizio app, è necessario scegliere anche un [piano tariffario.](../app-service/overview-hosting-plans.md)

*I flussi* [](../azure-functions/storage-considerations.md#storage-account-requirements)di lavoro con stato usano l'archiviazione esterna, Archiviazione di Azure [prezzi](https://azure.microsoft.com/pricing/details/storage/) si applicano alle transazioni di archiviazione eseguite App per la logica di Azure runtime di anteprima. Ad esempio, le code vengono usate per la pianificazione, mentre le tabelle e i BLOB vengono usati per l'archiviazione degli stati del flusso di lavoro.

> [!NOTE]
> Durante l'anteprima pubblica, l'esecuzione di  app per la logica nel servizio app non comporta costi aggiuntivi per il piano selezionato.

Per altre informazioni sui modelli tariffario applicabili al tipo di risorsa a tenant singolo, vedere questi argomenti:

* [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)
* [Aumentare le prestazioni di un'app nel servizio app di Azure](../app-service/manage-scale-up.md)
* [Funzioni di Azure prezzi](https://azure.microsoft.com/pricing/details/functions/)
* [Dettagli sui prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/)
* [Archiviazione di Azure prezzi](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Funzionalità modificate, limitate, non disponibili o non supportate

In App per la logica di Azure anteprima, queste funzionalità sono state modificate o sono attualmente limitate, non disponibili o non supportate:

* **Supporto del sistema** operativo: attualmente la finestra di progettazione in Visual Studio Code non funziona nel sistema operativo Linux, ma è comunque possibile distribuire app per la logica che usano il runtime di anteprima di App per la logica in macchine virtuali basate su Linux. Per il momento, è possibile compilare le app per la logica in Visual Studio Code in Windows o macOS e quindi distribuirsi in una macchina virtuale basata su Linux.

* **Trigger e azioni:** i trigger e le azioni predefiniti vengono eseguiti in modo nativo nel runtime di App per la logica di Azure Preview, mentre i connettori gestiti vengono distribuiti in Azure. Alcuni trigger predefiniti non sono disponibili, ad esempio Finestra temporale scorrevole e Batch.

  Per avviare il flusso di lavoro, usare il trigger predefinito [Ricorrenza, Richiesta, HTTP, Webhook HTTP, Hub](../connectors/apis-list.md)eventi o Bus di servizio. Nella finestra di progettazione i trigger e  le azioni predefiniti vengono visualizzati nella scheda Predefinita, mentre i trigger e le azioni del connettore gestito vengono visualizzati nella **scheda Azure.**

  > [!NOTE]
  > Per l'esecuzione in Visual Studio Code, i trigger e le azioni basati su webhook richiedono un'installazione aggiuntiva. Per altre informazioni, vedere [Creare flussi di lavoro con e senza](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)stato in Visual Studio Code .

  * Per *i flussi di lavoro* senza stato, la scheda **Azure** non viene visualizzata quando si seleziona un trigger perché è possibile selezionare solo le azioni del connettore [gestito, non attiva](../connectors/managed.md). Anche se è possibile abilitare i connettori gestiti distribuiti in Azure per i flussi di lavoro senza stato, la finestra di progettazione non mostra alcun trigger del connettore gestito da aggiungere.

  * Per *i flussi di* lavoro con stato, diversi da trigger e azioni elencati di seguito come non disponibili, sono disponibili per l'uso sia i [trigger](../connectors/managed.md) del connettore gestito che le azioni.

  * Questi trigger e azioni sono stati modificati o sono attualmente limitati, non supportati o non disponibili:

    * [I trigger del *gateway* dati locale](../connectors/managed.md#on-premises-connectors) non sono disponibili, ma le azioni del gateway *sono* disponibili.

    * L'azione predefinita, [Funzioni di Azure- Scegliere](logic-apps-azure-functions.md) una funzione di Azure è ora Operazioni della funzione di **Azure - Chiamare una funzione di Azure.** Questa azione attualmente funziona solo per le funzioni create dal modello **di trigger HTTP.**

      Nella finestra portale di Azure è possibile selezionare una funzione trigger HTTP a cui si ha accesso creando una connessione tramite l'esperienza utente. Se si esamina la definizione JSON dell'azione della funzione nella vista codice o **workflow.jsnel** file, l'azione fa riferimento alla funzione usando un `connectionName` riferimento. Questa versione astrae le informazioni della funzione come connessione, che è possibile trovare nelconnections.jsdel progetto nel **file,** disponibile dopo la creazione di una connessione.

      > [!NOTE]
      > Nella versione a tenant singolo l'azione della funzione supporta solo l'autenticazione della stringa di query. App per la logica di Azure Preview ottiene la chiave predefinita dalla funzione quando si effettua la connessione, archivia la chiave nelle impostazioni dell'app e usa la chiave per l'autenticazione quando si chiama la funzione.
      >
      > Come per la versione multi-tenant, se si rinnova questa chiave, ad esempio tramite l'esperienza Funzioni di Azure nel portale, l'azione della funzione non funziona più a causa della chiave non valida. Per risolvere questo problema, è necessario ricreare la connessione alla funzione che si vuole chiamare o aggiornare le impostazioni dell'app con la nuova chiave.

    * L'azione predefinita Codice [inline -](logic-apps-add-run-inline-code.md) Esegui codice JavaScript è ora Operazioni codice **inline - Esegui JavaScript inline.**

      * Le azioni operazioni codice inline non richiedono più un account di integrazione.

      * Per macOS e Linux, le operazioni di codice **inline** sono ora supportate quando si usa l'estensione App per la logica di Azure (anteprima) in Visual Studio Code.

      * Non è più necessario riavviare l'app per la logica se si apportano modifiche in **un'azione Operazioni codice inline.**

      * **Le azioni operazioni codice inline** hanno [aggiornato i limiti](logic-apps-overview-preview.md#inline-code-limits).

    * Alcuni [trigger e azioni B2B](../connectors/managed.md#integration-account-connectors) predefiniti per gli account di  integrazione non sono disponibili, ad esempio le azioni di codifica e decodifica del file flat.

    * L'azione predefinita, [App per la logica di Azure-](logic-apps-http-endpoint.md) Scegliere un flusso di lavoro dell'app per la logica è ora Operazioni del flusso di lavoro - Richiamare un flusso di lavoro **in questa app flusso di lavoro.**

* [I connettori personalizzati](../connectors/apis-list.md#custom-apis-and-connectors) non sono attualmente supportati per l'anteprima.

* Disponibilità del piano di **hosting:** se si crea il tipo di risorsa App per la logica a tenant singolo **(anteprima)** nel portale di Azure o si esegue la distribuzione da Visual Studio Code, è possibile usare solo il piano di hosting Premium o servizio app in Azure. I piani di hosting a consumo non sono disponibili e non sono supportati per la distribuzione di questo tipo di risorsa. È possibile eseguire la distribuzione Visual Studio Code in un contenitore Docker, ma non in un ambiente del servizio di [integrazione (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

* Debug dei punti **di interruzione** in Visual Studio Code: sebbene sia possibile aggiungere e usare punti di interruzione all'interno del **workflow.js** nel file per un flusso di lavoro, i punti di interruzione sono supportati solo per le azioni in questo momento, non per i trigger. Per altre informazioni, vedere [Creare flussi di lavoro con e senza](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)stato in Visual Studio Code .

* **Controllo zoom:** il controllo zoom non è attualmente disponibile nella finestra di progettazione.

* **Cronologia dei** trigger e cronologia di esecuzione: per il tipo di risorsa App per la logica **(anteprima),** la cronologia dei trigger e la cronologia di esecuzione nel portale di Azure vengono visualizzate a livello di flusso di lavoro, non a livello di app per la logica. Per trovare questi dati cronologici, seguire questa procedura:

   * Per visualizzare la cronologia di esecuzione, aprire il flusso di lavoro nell'app per la logica. Nel menu del flusso di lavoro, in **Sviluppatore,** selezionare **Monitoraggio.**

   * Per esaminare la cronologia dei trigger, aprire il flusso di lavoro nell'app per la logica. Nel menu del flusso di lavoro, in **Developer (Sviluppatore),** **selezionare Trigger Histories (Cronologie trigger).**

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Consentire il traffico in scenari di rete e firewall rigorosi

Se l'ambiente ha requisiti di rete rigorosi o firewall che limitano il traffico, è necessario consentire l'accesso per qualsiasi connessione trigger o azione nei flussi di lavoro dell'app per la logica.

Per trovare i nomi di dominio completi (FQDN) per queste connessioni, vedere le sezioni corrispondenti in questi argomenti:

* [Autorizzazioni del firewall per app per la logica a tenant singolo - Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Autorizzazioni del firewall per app per la logica a tenant singolo - portale di Azure](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Limiti aggiornati

Anche se molti limiti per App per la logica di Azure Preview rimangono gli stessi dei limiti per i App per la logica di Azure [multi-tenant,](logic-apps-limits-and-config.md)questi limiti sono stati modificati per App per la logica di Azure Preview.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Limiti di timeout HTTP

Per una singola chiamata in ingresso o in uscita, il limite di timeout è di 230 secondi (3,9 minuti) per i trigger e le azioni seguenti:

* Richiesta in uscita: trigger HTTP, azione HTTP
* Richiesta in ingresso: trigger di richiesta, trigger webhook HTTP, azione webhook HTTP

Di seguito sono invece visualizzati i limiti di timeout per questi trigger e azioni in altri ambienti in cui vengono eseguite le app per la logica e i relativi flussi di lavoro:

* Multi-tenant App per la logica di Azure: 120 secondi (2 minuti)
* Ambiente del servizio di integrazione: 240 secondi (4 minuti)

Per altre informazioni, vedere [Limiti HTTP.](logic-apps-limits-and-config.md#http-limits)

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Connettori gestiti

I connettori gestiti sono limitati a 50 richieste al minuto per ogni connessione. Per risolvere i problemi di limitazione dei connettori, vedere Gestire i problemi di limitazione [(429 - Errore "Troppe richieste") in App per la logica di Azure](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Operazioni sul codice inline (eseguire codice JavaScript)

Per una singola definizione di app per la logica, l'azione Operazioni codice inline, [**Esegui codice JavaScript,**](logic-apps-add-run-inline-code.md)presenta i limiti aggiornati seguenti:

* Il numero massimo di caratteri di codice aumenta da 1.024 a 100.000 caratteri.

* La durata massima per l'esecuzione del codice aumenta da cinque secondi a 15 secondi.

Per altre informazioni, vedere Limiti [di definizione dell'app per la logica.](logic-apps-limits-and-config.md#definition-limits)

## <a name="next-steps"></a>Passaggi successivi

* [Creare flussi di lavoro con e senza stato nel portale di Azure](create-stateful-stateless-workflows-azure-portal.md)
* [Creare flussi di lavoro con e senza stato in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Pagina dei problemi noti dell'anteprima pubblica di App per la logica in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Inoltre, l'utente può udirne le esperienze con App per la logica di Azure Preview.

* Per bug o problemi, [creare i problemi in GitHub.](https://github.com/Azure/logicapps/issues)
* Per domande, richieste, commenti e altri commenti, [usare questo modulo di feedback.](https://aka.ms/lafeedback)
