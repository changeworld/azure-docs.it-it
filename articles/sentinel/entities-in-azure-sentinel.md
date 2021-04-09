---
title: Usare le entità per classificare e analizzare i dati in Sentinel di Azure | Microsoft Docs
description: Assegnare le classificazioni di entità (utenti, nomi host, indirizzi IP) a elementi di dati in Sentinel di Azure e usarli per confrontare, analizzare e correlare i dati da più origini.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456304"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Classificare e analizzare i dati usando le entità in Sentinel di Azure

## <a name="what-are-entities"></a>Che cosa sono le entità?

Quando gli avvisi vengono inviati o generati da Sentinel di Azure, contengono elementi di dati che Sentinel può riconoscere e classificare in categorie come **entità**. Quando Azure Sentinel comprende il tipo di entità rappresentata da un particolare elemento di dati, conosce le domande più appropriate e può quindi confrontare informazioni dettagliate su tale elemento nell'intera gamma di origini dati e tenerne traccia e farvi riferimento nell'intera esperienza Sentinel, ovvero analisi, analisi, correzione, caccia e così via. Alcuni esempi comuni di entità sono utenti, host, file, processi, indirizzi IP e URL.

### <a name="entity-identifiers"></a>Identificatori di entità

Azure Sentinel supporta un'ampia gamma di tipi di entità. Ogni tipo ha attributi univoci, inclusi quelli che possono essere usati per identificare un'entità specifica. Questi attributi sono rappresentati come campi nell'entità e sono denominati **identificatori**. Vedere l'elenco completo delle entità supportate e i relativi identificatori di seguito.

#### <a name="strong-and-weak-identifiers"></a>Identificatori forti e vulnerabili

Come indicato sopra, per ogni tipo di entità sono presenti campi o set di campi che possono identificarlo. Questi campi o set di campi possono essere definiti **identificatori sicuri** se possono identificare in modo univoco un'entità senza ambiguità o come **identificatori deboli** se possono identificare un'entità in alcune circostanze, ma non è garantito che identifichi in modo univoco un'entità in tutti i casi. In molti casi, tuttavia, è possibile combinare una selezione di identificatori vulnerabili per produrre un identificatore sicuro.

Ad esempio, gli account utente possono essere identificati come entità **account** in più modi: usando un singolo **identificatore forte** come un identificatore numerico di un account Azure ad (il campo **GUID** ) o il relativo **nome dell'entità utente (UPN)** o, in alternativa, usare una combinazione di **identificatori vulnerabili** come il **nome** e i campi **NTDOMAIN** . Origini dati diverse possono identificare lo stesso utente in modi diversi. Ogni volta che la sentinella di Azure rileva due entità che possono riconoscere come la stessa entità in base ai relativi identificatori, unisce le due entità in una singola entità, in modo che possa essere gestita correttamente e in modo coerente.

Se, tuttavia, uno dei provider di risorse crea un avviso in cui un'entità non è sufficientemente identificata, ad esempio usando un solo **identificatore debole** come un nome utente senza il contesto del nome di dominio, l'entità utente non può essere unita ad altre istanze dello stesso account utente. Le altre istanze verranno identificate come entità separate e le due entità rimarranno separate anziché unificate.

Per ridurre al minimo il rischio che si verifichi questo problema, è necessario verificare che tutti i provider di avvisi identifichino correttamente le entità negli avvisi che producono. Inoltre, la sincronizzazione delle entità account utente con Azure Active Directory può creare una directory unificata, che sarà in grado di unire entità account utente.

#### <a name="supported-entities"></a>Entità supportate

In Sentinel di Azure sono attualmente identificati i tipi di entità seguenti:

- Account utente
- Host
- Indirizzo IP
- Malware
- File
- Processo
- Applicazione cloud
- Nome di dominio
- Risorsa di Azure
- Hash file
- Chiave del Registro di sistema
- Valore del Registro di sistema
- Gruppo di protezione
- URL
- Dispositivo IoT
- Mailbox
- Cluster di posta
- Messaggio di posta elettronica
- Posta elettronica di invio

È possibile visualizzare gli identificatori delle entità e altre informazioni rilevanti nel [riferimento alle entità](entities-reference.md).

## <a name="entity-mapping"></a>Mapping di entità

In che modo Azure Sentinel riconosce una porzione di dati in un avviso come identificazione di un'entità?

Verrà ora esaminato il modo in cui l'elaborazione dei dati viene eseguita in Sentinel di Azure. I dati vengono inseriti da diverse origini tramite [connettori](connect-data-sources.md), sia da servizio a servizio, basati su agenti, che tramite un servizio syslog e un server d'avanzamento. I dati vengono archiviati in tabelle nell'area di lavoro Log Analytics. Queste tabelle vengono quindi sottoposte a query a intervalli regolari pianificati in base alle regole di analisi definite e abilitate. Una delle molte azioni eseguite da queste regole di analisi è il mapping dei campi dati nelle tabelle alle entità riconosciute da Azure Sentinel. In base ai mapping definiti nelle regole di analisi, Azure Sentinel prenderà i campi dei risultati restituiti dalla query, li rileverà dagli identificatori specificati per ogni tipo di entità e li applicherà al tipo di entità identificato da tali identificatori.

Qual è il punto di tutto questo?

Quando Sentinel di Azure è in grado di identificare le entità negli avvisi da diversi tipi di origini dati e, in particolare, se questa operazione può essere eseguita usando identificatori sicuri comuni a ogni origine dati o a un terzo schema, può essere facilmente correlata tra tutti gli avvisi e le origini dati. Queste correlazioni consentono di creare un archivio completo di informazioni e informazioni dettagliate sulle entità, offrendo una solida base per le operazioni di sicurezza.

Informazioni su come eseguire il [mapping dei campi dati alle entità](map-data-fields-to-entities.md).

Informazioni sugli [identificatori che identificano fortemente un'entità](entities-reference.md).

## <a name="entity-pages"></a>Pagine entità

Quando si verifica un'entità (attualmente limitata a utenti e host) in una ricerca, un avviso o un'indagine, è possibile selezionare l'entità e una **pagina di entità**, un foglio dati completo di informazioni utili su tale entità. I tipi di informazioni che si troveranno in questa pagina includono fact di base sull'entità, una sequenza temporale di eventi rilevanti correlati a questa entità e informazioni dettagliate sul comportamento dell'entità.

Le pagine entità sono costituite da tre parti:

- Il pannello a sinistra contiene le informazioni di identificazione dell'entità, raccolte da origini dati come Azure Active Directory, monitoraggio di Azure, Centro sicurezza di Azure e Microsoft Defender.

- Il pannello centrale mostra una sequenza temporale grafica e testuale degli eventi rilevanti correlati all'entità, ad esempio avvisi, segnalibri e attività. Le attività sono aggregazioni di eventi rilevanti da Log Analytics. Le query che rilevano tali attività vengono sviluppate dai team di ricerca della sicurezza Microsoft.

- Il pannello di destra presenta informazioni comportamentali sull'entità. Queste informazioni consentono di identificare rapidamente le anomalie e le minacce per la sicurezza. Le informazioni dettagliate sono sviluppate dai team di ricerca della sicurezza Microsoft e si basano sui modelli di rilevamento delle anomalie.

### <a name="the-timeline"></a>Sequenza temporale

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Sequenza temporale pagine entità":::

La sequenza temporale è una parte importante del contributo della pagina di entità all'analisi del comportamento in Sentinel di Azure. Viene presentata una storia sugli eventi correlati a entità, che consente di comprendere l'attività dell'entità in un intervallo di tempo specifico.

È possibile scegliere l' **intervallo di tempo** tra le diverse opzioni predefinite (ad esempio, le *ultime 24 ore*) o impostarlo su qualsiasi intervallo di tempo definito personalizzato. Inoltre, è possibile impostare i filtri che limitano le informazioni nella sequenza temporale a tipi specifici di eventi o avvisi.

Nella sequenza temporale sono inclusi i tipi di elementi seguenti:

- Avvisi: tutti gli avvisi in cui l'entità viene definita come **entità mappata**. Si noti che, se l'organizzazione ha creato [avvisi personalizzati con le regole di analisi](./tutorial-detect-threats-custom.md), è necessario assicurarsi che il mapping delle entità delle regole venga eseguito correttamente.

- Segnalibri: tutti i segnalibri che includono l'entità specifica visualizzata nella pagina.

- Attività: aggregazione di eventi rilevanti correlati all'entità.

### <a name="entity-insights"></a>Informazioni dettagliate sulle entità

Entity Insights è una query definita dai ricercatori della sicurezza Microsoft per aiutare gli analisti a esaminare in modo più efficiente ed efficace. Le informazioni dettagliate vengono presentate come parte della pagina entità e forniscono informazioni di sicurezza importanti su host e utenti, sotto forma di dati tabulari e grafici. Con le informazioni significa che non devi deviare per Log Analytics. Le informazioni dettagliate includono i dati relativi ad accessi, aggiunte di gruppo, eventi anomali e altro ancora e includono algoritmi di Machine Learning avanzati per rilevare un comportamento anomalo.

Le informazioni dettagliate sono basate sulle origini dati seguenti:

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (offrono dati di Sentinel di Azure)
- Heartbeat (agente di monitoraggio di Azure)
- CommonSecurityLog (Sentinel di Azure)

### <a name="how-to-use-entity-pages"></a>Come utilizzare le pagine delle entità

Le pagine di entità sono progettate per essere parte di più scenari di utilizzo ed è possibile accedervi dalla gestione degli eventi imprevisti, dal grafico di analisi, dai segnalibri o direttamente dalla pagina di ricerca di entità in **analisi del comportamento delle entità** nel menu principale di Sentinel di Azure.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Casi di utilizzo della pagina di entità":::

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare le entità in Sentinel di Azure. Per informazioni pratiche sull'implementazione e per usare le informazioni acquisite, vedere gli articoli seguenti:

- [Abilitare l'analisi del comportamento delle entità](./enable-entity-behavior-analytics.md) in Sentinel di Azure.
- Cerca le [minacce per la sicurezza](./hunting.md).
