---
title: Connettori per App per la logica di Azure
description: Panoramica dei connettori per la creazione di flussi di lavoro automatizzati con App per la logica di Azure. Informazioni sul funzionamento dei diversi tipi di connettori, trigger e azioni.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771354"
---
# <a name="connectors-for-azure-logic-apps"></a>Connettori per App per la logica di Azure

In App per la logica di Azure, *i connettori* consentono di accedere rapidamente a eventi, dati e azioni da altre app, servizi, sistemi, protocolli e piattaforme. Spesso è possibile eseguire queste attività senza codice aggiuntivo. È anche possibile usare i connettori per creare flussi di lavoro di App per la logica che usano queste informazioni in ambienti basati sul cloud, locali o ibridi.

Sono disponibili centinaia di connettori per App per la logica. Di conseguenza, questa documentazione è incentrata su alcuni dei connettori più diffusi e comunemente usati per App per la logica. Per informazioni complete sui connettori tra App per la logica, Microsoft Power Automate e Microsoft Power Apps, vedere la documentazione [sui connettori.](/connectors) 

Per informazioni sui prezzi, vedere Modello tariffario di App per [la logica](../logic-apps/logic-apps-pricing.md)e Dettagli prezzi di App per [la logica.](https://azure.microsoft.com/pricing/details/logic-apps/)

Per integrare l'app per la logica con un servizio o un'API che non dispone di un connettore, è possibile chiamare il servizio tramite un protocollo, ad esempio HTTP, o creare un [connettore personalizzato.](#custom-apis-and-connectors)

## <a name="what-are-connectors"></a>Che cosa sono i connettori?

In App per la logica di Azure i connettori forniscono *trigger* e *azioni* che consentono di eseguire attività nel flusso di lavoro dell'app per la logica. Ogni trigger e azione ha proprietà che è possibile configurare. Alcuni trigger e azioni richiedono la creazione e la configurazione di connessioni [in](#connection-configuration) modo che il flusso di lavoro possa accedere a un servizio o a un sistema specifico.

### <a name="triggers"></a>Trigger

Un *trigger* è sempre il primo passaggio di qualsiasi flusso di lavoro, che specifica l'evento che avvia il flusso di lavoro. Esistono diversi tipi di trigger:

- *I trigger di polling* controllano regolarmente un servizio o un sistema specifico in base a una pianificazione specificata per verificare la presenza di nuovi dati o di un evento specifico. Se sono disponibili nuovi dati o si verifica l'evento specifico, questi trigger creano ed eseguono una nuova istanza del flusso di lavoro. Questa nuova istanza può quindi usare i dati passati come input.
- *I trigger push* sono in ascolto di nuovi dati o di un evento, senza polling. Quando sono disponibili nuovi dati o quando si verifica l'evento, questi trigger creano ed eseguono una nuova istanza dell'app per la logica. Questa nuova istanza può quindi usare i dati passati come input.

Ad esempio, potrebbe essere necessario compilare un flusso di lavoro che esegue un'operazione quando un file viene caricato nel server FTP. È possibile aggiungere il trigger del connettore FTP denominato **Quando un file viene** aggiunto o modificato come primo passaggio del flusso di lavoro. È quindi possibile specificare una pianificazione per controllare regolarmente la presenza di eventi di caricamento.

Un trigger passa anche tutti gli input e altri dati necessari nel flusso di lavoro in cui le azioni successive possono fare riferimento a questi dati e usarli in tutto il flusso di lavoro. Ad esempio, è possibile usare il trigger di Office 365 Outlook denominato **All'arrivo di un nuovo** messaggio di posta elettronica. È possibile configurare questo trigger per passare il contenuto di ogni nuovo messaggio di posta elettronica, ad esempio il mittente, la riga dell'oggetto, il corpo, gli allegati e così via. È quindi possibile elaborare le informazioni nell'app per la logica usando le azioni.

### <a name="actions"></a>Azioni

*Un'azione* è un'operazione che segue il trigger ed esegue un tipo di attività nel flusso di lavoro. È possibile usare più azioni nell'app per la logica. Ad esempio, potrebbe essere presente un trigger SQL che rileva i nuovi dati dei clienti in un database SQL. Il flusso di lavoro può includere una prima azione SQL che ottiene i dati del cliente, seguita da un'altra azione che non è necessariamente un'azione SQL, che elabora i dati.

## <a name="connector-categories"></a>Categorie di connettori

In App per la logica sono in genere disponibili versioni del connettore predefinite o gestite di trigger e azioni. In entrambe le versioni è disponibile un numero ridotto di trigger e azioni. Le versioni specifiche variano a seconda che si crei un'app per la logica multi-tenant o un'app per la logica a tenant singolo, attualmente disponibile solo in Anteprima app per la [logica](../logic-apps/logic-apps-overview-preview.md).

[I trigger e le azioni predefiniti](built-in.md) vengono eseguiti in modo nativo nel runtime di App per la logica, non richiedono la creazione di connessioni ed eseguono questi tipi di attività:

- [Eseguire codice nei flussi di lavoro](built-in.md#run-code-from-workflows).
- [Organizzare e controllare i dati](built-in.md#control-workflow).
- [Gestire o modificare i dati](built-in.md#manage-or-manipulate-data).

[I connettori](managed.md) gestiti vengono distribuiti, ospitati e gestiti da Microsoft. Questi connettori forniscono trigger e azioni per servizi cloud, sistemi locali o entrambi. Queste includono:

- [Connettori locali che consentono](managed.md#on-premises-connectors) di accedere ai dati e alle risorse nei sistemi locali.
- [Connettori aziendali,](managed.md#enterprise-connectors)che sono versioni di alcuni connettori di App per la logica che forniscono l'accesso ai sistemi aziendali.
- [Connettori di account di](managed.md#integration-account-connectors)integrazione che supportano scenari di comunicazione business-to-business (B2B).
- [ambiente del servizio di integrazione (ISE)](managed.md#ise-connectors) che sono un piccolo gruppo di [connettori gestiti disponibili solo per gli ISE.](#ise-and-connectors)

## <a name="connection-configuration"></a>Configurazione della connessione

La maggior parte dei connettori richiede prima di tutto di creare una connessione al servizio o al sistema di destinazione prima di poter usare i relativi trigger o azioni nell'app per la logica.  Per creare una connessione, è necessario autenticare l'identità con le credenziali dell'account e talvolta con altre informazioni di connessione. Ad esempio, prima che il flusso di lavoro possa accedere e usare l'account di posta elettronica di Office 365 Outlook in un'app per la logica, è necessario autorizzare una connessione a tale account.

Per i connettori che usano OAuth Azure Active Directory (Azure AD), ad esempio Office 365, Salesforce o GitHub, è [](../security/fundamentals/encryption-overview.md) necessario accedere al servizio in cui il token di accesso viene crittografato e archiviato in modo sicuro in un segreto di Azure. Altri connettori, ad esempio FTP e SQL, richiedono una connessione con i dettagli di configurazione, ad esempio l'indirizzo del server, il nome utente e la password. Anche questi dettagli di configurazione della connessione [vengono crittografati e archiviati in modo sicuro in Azure.](../security/fundamentals/encryption-overview.md)

Le connessioni stabilite possono accedere al servizio o al sistema di destinazione per tutto il tempo che il servizio o il sistema consente. Per i servizi che usano Azure AD connessioni OAuth, ad esempio Office 365 e Dynamics, App per la logica aggiorna i token di accesso per un periodo illimitato. Altri servizi potrebbero avere limiti per quanto tempo il servizio App per la logica può usare un token senza aggiornamento. In genere, alcune azioni, ad esempio la modifica della password, invalidano tutti i token di accesso.

Anche se si creano connessioni dall'interno di un flusso di lavoro, le connessioni sono risorse di Azure separate con le proprie definizioni di risorse. Per esaminare queste definizioni delle risorse di connessione, [scaricare l'app per la logica da Azure in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md). Questo metodo è il modo più semplice per creare un modello di app per la logica valido e con parametri che sia principalmente pronto per la distribuzione.

> [!TIP]
> Se l'organizzazione non consente di accedere a risorse specifiche [](../logic-apps/block-connections-connectors.md) tramite connettori di App per la logica, è possibile bloccare la possibilità di creare tali connessioni [usando](../governance/policy/overview.md)Criteri di Azure .

## <a name="recurrence-behavior"></a>Comportamento della ricorrenza

I trigger predefiniti ricorrenti, ad esempio il [trigger](../connectors/connectors-native-recurrence.md)Ricorrenza, vengono eseguiti in modo nativo in App per la logica di Azure e differiscono dai trigger ricorrenti basati sulla connessione, ad esempio il trigger del connettore Office 365 Outlook, in cui è necessario creare prima una connessione.

Per entrambi i tipi di trigger, se una ricorrenza non specifica una data e un'ora di inizio specifiche, la prima ricorrenza viene eseguita immediatamente quando si salva o si distribuisce l'app per la logica, nonostante la configurazione della ricorrenza del trigger. Per evitare questo comportamento, specificare una data e un'ora di inizio per l'esecuzione della prima ricorrenza.

### <a name="recurrence-for-built-in-triggers"></a>Ricorrenza per i trigger predefiniti

I trigger predefiniti ricorrenti seguono la pianificazione impostata, incluso qualsiasi fuso orario specificato. Tuttavia, se una ricorrenza non specifica altre opzioni di pianificazione avanzate, ad esempio orari specifici per l'esecuzione di ricorrenze future, tali ricorrenze si basano sull'ultima esecuzione del trigger. Di conseguenza, gli orari di inizio per queste ricorrenze potrebbero derivare a causa di fattori come la latenza durante le chiamate di archiviazione. Per informazioni sulla risoluzione dei problemi, vedere la sezione Problemi [di ricorrenza.](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>Ricorrenza per i trigger basati sulla connessione

Nei trigger ricorrenti basati sulla connessione, ad esempio Office 365 Outlook, la pianificazione non è l'unico driver che controlla l'esecuzione. Il fuso orario determina solo l'ora di inizio iniziale. Le esecuzioni successive dipendono dalla pianificazione della ricorrenza, dall'ultima esecuzione del trigger e da altri fattori che potrebbero causare la deriva dei tempi di esecuzione o produrre un comportamento imprevisto. Queste includono:

- Indica se il trigger accede a un server con più dati, che il trigger tenta immediatamente di recuperare.
- Eventuali errori o tentativi del trigger.
- Latenza durante le chiamate di archiviazione.
- Non mantenere la pianificazione specificata all'inizio e alla fine dell'ora legale.
- Altri fattori che possono influire sul momento in cui si verifica la successiva esecuzione.

Per informazioni sulla risoluzione dei problemi, [vedere la sezione Problemi di ricorrenza.](#recurrence-issues) 

### <a name="recurrence-issues"></a>Problemi di ricorrenza

Per assicurarsi che il flusso di lavoro venga eseguito all'ora di inizio specificata e non perdi una ricorrenza, soprattutto quando la frequenza è in giorni o più, provare le soluzioni seguenti.

Quando l'ora legale ha effetto, modificare manualmente la ricorrenza in modo che il flusso di lavoro continui a essere eseguito all'ora prevista. In caso contrario, l'ora di inizio viene spostata avanti di un'ora all'inizio dell'ora legale e di un'ora indietro al termine dell'ora legale. Per altre informazioni ed esempi, vedere [Ricorrenza per l'ora](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)legale e l'ora solare.

Se si usa un trigger **Ricorrenza,** specificare un fuso orario, una data e un'ora di inizio. Configurare inoltre orari specifici per l'esecuzione di ricorrenze successive nelle proprietà In queste ore e In **questi** minuti, che sono disponibili solo per le frequenze **giorno** **e** settimana.  Tuttavia, alcune finestre di tempo potrebbero comunque causare problemi quando il tempo cambia. 

È consigliabile [ **usare un** trigger](../connectors/connectors-native-sliding-window.md) finestra temporale scorrevole anziché un trigger Ricorrenza per evitare ricorrenze perse. 

## <a name="custom-apis-and-connectors"></a>API e connettori personalizzati

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API personalizzate](../logic-apps/logic-apps-create-api-app.md). 

È anche possibile [creare connettori personalizzati](../logic-apps/custom-connector-overview.md) per qualsiasi API basata su REST o SOAP, che rendono le API disponibili a qualsiasi app per la logica nella sottoscrizione di Azure. 

Per rendere pubblici i connettori o le app per le API personalizzate da utilizzare in Azure, è possibile [inviare i connettori per la certificazione Microsoft](/connectors/custom-connectors/submit-certification).

## <a name="ise-and-connectors"></a>ISE e connettori

Per i flussi di lavoro che necessitano dell'accesso diretto alle risorse in una rete virtuale di Azure, è possibile creare un ambiente del servizio di integrazione [dedicato in](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) cui è possibile compilare, distribuire ed eseguire i flussi di lavoro in risorse dedicate. Per altre informazioni sulla creazione di ISE, vedere [Connettersi alle reti virtuali di Azure da App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

I connettori personalizzati creati all'interno di un ise non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente alle origini dati locali connesse a una rete virtuale di Azure che ospita l'ISE. Pertanto, le app per la logica in un ise non necessitano molto probabilmente del gateway dati durante la comunicazione con tali risorse. Se si dispone di connettori personalizzati creati all'esterno di un ISE che richiedono il gateway dati locale, le app per la logica in un ISE possono usare tali connettori.

In Progettazione app per la logica, quando si esplorano i connettori che si desidera usare per le app per la logica in un ise, viene visualizzata un'etichetta **CORE** su trigger e azioni predefiniti, mentre l'etichetta **ISE** viene visualizzata in alcuni connettori.

:::row:::
    :::column:::
        ![Connettore CORE di esempio](./media/apis-list/example-core-connector.png)
        \
        \
        **nucleo**
        \
        \
        I trigger e le azioni predefiniti con questa etichetta vengono eseguiti nello stesso ISE delle app per la logica.
    :::column-end:::
    :::column:::
        ![Connettore ISE di esempio](./media/apis-list/example-ise-connector.png)
        \
        \
        **Ise**
        \
        \
        I connettori gestiti con questa etichetta vengono eseguiti nello stesso ISE delle app per la logica. Se si dispone di un sistema locale connesso a una rete virtuale di Azure, un ISE consente alle app per la logica di accedere direttamente al sistema senza il [gateway dati locale.](../logic-apps/logic-apps-gateway-connection.md) È invece possibile usare il **connettore ISE** del sistema, se disponibile, un'azione HTTP o un [connettore personalizzato.](connectors-overview.md#custom-apis-and-connectors) Per i sistemi locali che non dispongono di connettori **ISE,** usare il gateway dati locale. Per esaminare i connettori ISE disponibili, vedere [Connettori ISE](#ise-and-connectors).
    :::column-end:::
    :::column:::
        ![Connettore multi-tenant di esempio](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Nessuna etichetta     \
        \
        Tutti gli altri connettori senza l'etichetta **CORE** o **ISE,** che è possibile continuare a usare, vengono eseguiti nel servizio app per la logica multi-tenant globale.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Problemi noti

Di seguito sono riportati i problemi noti per i connettori di App per la logica.

#### <a name="error-badgateway-client-request-id-guid"></a>Errore: BadGateway. ID richiesta client: '{GUID}'

Questo errore deriva dall'aggiornamento dei tag in un'app per la logica in cui una o più connessioni non supportano l'autenticazione OAuth Azure Active Directory (Azure AD), ad esempio SFTP ad SQL, interrompendo tali connessioni. Per evitare questo comportamento, evitare di aggiornare tali tag.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare API personalizzate che è possibile chiamare da App per la logica](/logic-apps/logic-apps-create-api-app)
