---
title: Creare regole di analisi personalizzate per rilevare le minacce con Azure Sentinel| Microsoft Docs
description: Usare questa esercitazione per informazioni su come creare regole di analisi personalizzate per rilevare le minacce alla sicurezza con Azure Sentinel. Sfruttare i vantaggi del raggruppamento di eventi, del raggruppamento degli avvisi e dell'arricchimento degli avvisi e comprendere AUTO DISABLED.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 180a5edd00b6085ffd91568471ca763f5e4e9711
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814857"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Esercitazione: Creare regole di analisi personalizzate per rilevare le minacce

Dopo aver connesso [](quickstart-onboard.md) le origini dati a Azure Sentinel, è possibile creare regole di analisi personalizzate per individuare le minacce e i comportamenti anomali presenti nell'ambiente. Queste regole consentono di cercare eventi o set di eventi specifici nell'ambiente, avvisare l'utente quando vengono raggiunte determinate soglie o condizioni, generare eventi imprevisti per il soc per la trivazione e l'analisi e rispondere alle minacce con processi automatizzati di rilevamento e correzione. 

Questa esercitazione consente di creare regole personalizzate per rilevare le minacce con Azure Sentinel.

Al termine dell'esercitazione, sarà possibile eseguire le operazioni seguenti:
> [!div class="checklist"]
> * Creare regole di analisi
> * Definire la modalità di elaborazione di eventi e avvisi
> * Definire la modalità di generazione di avvisi ed eventi imprevisti
> * Scegliere risposte automatiche alle minacce per le regole

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Creare una regola di analisi personalizzata con una query pianificata

1. Dal menu Azure Sentinel di spostamento selezionare **Analytics.**

1. Nella barra delle azioni nella parte superiore selezionare **+Crea e** selezionare **Regola di query pianificata.** Verrà aperta la creazione **guidata delle regole di Analisi**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Creare una query pianificata" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Creazione guidata regola di Analisi - Scheda Generale

- Specificare un nome **univoco e** una **Descrizione.** 

- Nel campo **Tattiche** è possibile scegliere tra le categorie di attacchi in base alle quali classificare la regola. Si basano sulle tattiche del framework [MITRE ATT&CK.](https://attack.mitre.org/)

- Impostare la **gravità dell'avviso** in base alle esigenze. 

- Quando si crea la regola, il **relativo stato** è **Abilitato** per impostazione predefinita, ovvero verrà eseguito immediatamente dopo aver completato la creazione. Se non si vuole eseguirla immediatamente, selezionare Disabilitato e la regola verrà aggiunta alla scheda **Regole** attive ed è possibile abilitarla da qui quando è necessaria. 

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Iniziare a creare una regola di analisi personalizzata":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definire la logica di query della regola e configurare le impostazioni

Nella scheda **Imposta logica regola** è possibile scrivere una query direttamente nel campo **Query** regola oppure creare la query in Log Analytics e quindi copiarla e incollarla qui.

- Le query sono scritte nel linguaggio di query Kusto (KQL). Per altre informazioni sui [](/azure/data-explorer/kusto/query/)concetti e sulle query KQL, vedere questa guida di [riferimento rapido.](/azure/data-explorer/kql-quick-reference) [](/azure/data-explorer/kusto/concepts/)

- L'esempio illustrato in questo screenshot esegue una query *nella tabella SecurityEvent* per visualizzare un tipo di eventi [di accesso di Windows non riusciti.](/windows/security/threat-protection/auditing/event-4625)

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Configurare la logica e le impostazioni delle regole di query" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Ecco un'altra query di esempio, che avvisa quando viene creato un numero anomalo di risorse in [Attività di Azure.](../azure-monitor/essentials/activity-log.md)

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Procedure consigliate per le query delle regole
    > - La lunghezza della query deve essere compresa tra 1 e 10.000 caratteri e non può contenere " `search *` " o `union *` " ". È possibile usare [funzioni definite dall'utente per](/azure/data-explorer/kusto/query/functions/user-defined-functions) superare la limitazione della lunghezza delle query.
    >
    > - L'uso di funzioni ADX per creare Esplora dati di Azure query all'interno della finestra di query di Log Analytics **non è supportato.**
    >
    > - Quando si usa la funzione in una query, se si proiettano le colonne come campi usando " " e la colonna non esiste, la **`bag_unpack`** query avrà esito `project field1` negativo. Per proteggersi da questo problema, è necessario proiettare la colonna nel modo seguente:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Arricchimento degli avvisi

> [!IMPORTANT]
> Le funzionalità di arricchimento degli avvisi sono attualmente disponibili in **ANTEPRIMA.** Per altre condizioni legali applicabili alle funzionalità di Azure in versione beta, in anteprima o altrimenti non ancora rilasciate nella disponibilità [generale, vedere](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Le condizioni per l'utilizzo supplementari per le Microsoft Azure preview.
    
- Usare la **sezione Entity mapping** configuration (Configurazione mapping entità) per eseguire il mapping dei parametri dai risultati della query Azure Sentinel entità riconosciute. Le entità arricchiscono l'output delle regole (avvisi ed eventi imprevisti) con informazioni essenziali che funge da blocchi predefiniti di qualsiasi processo di indagine e azioni correttive che seguono. Sono anche i criteri in base ai quali è possibile raggruppare gli avvisi in eventi imprevisti nella **scheda Impostazioni eventi imprevisti.**

    Altre informazioni sulle [entità in Azure Sentinel](entities-in-azure-sentinel.md).

    Per istruzioni complete sul mapping di entità, oltre a informazioni importanti sulla [compatibilità con le versioni precedenti, vedere](map-data-fields-to-entities.md) Eseguire il mapping dei campi dati alle entità in [Azure Sentinel.](map-data-fields-to-entities.md#notes-on-the-new-version)

- Usare la **sezione Configurazione** dettagli personalizzati per estrarre gli elementi dei dati degli eventi dalla query ed estrarli negli avvisi generati da questa regola, offrendo una visibilità immediata del contenuto degli eventi negli avvisi e negli eventi imprevisti.

    Altre informazioni su come visualizzare i dettagli personalizzati negli avvisi e vedere le [istruzioni complete.](surface-custom-details-in-alerts.md)

### <a name="query-scheduling-and-alert-threshold"></a>Pianificazione delle query e soglia di avviso

- Nella **sezione Pianificazione query** impostare i parametri seguenti:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Impostare la pianificazione delle query e il raggruppamento di eventi" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Impostare **Esegui query ogni** per controllare la frequenza con cui viene eseguita la query, con una frequenza di 5 minuti o raramente una volta ogni 14 giorni.

    - Impostare **Dati** di ricerca dall'ultimo per determinare il periodo di tempo dei dati coperti dalla query, ad esempio è possibile eseguire una query degli ultimi 10 minuti di dati o delle ultime 6 ore di dati. Il valore massimo è 14 giorni.

        > [!NOTE]
        > **Intervalli di query e periodo di lookback**
        >
        >  Queste due impostazioni sono indipendenti l'una dall'altra, fino a un certo punto. È possibile eseguire una query a intervalli brevi che coprono un periodo di tempo più lungo dell'intervallo (in effetti con query sovrapposte), ma non è possibile eseguire una query a un intervallo che supera il periodo di copertura. In caso contrario, si verificano gap nella copertura complessiva delle query.
        >
        > **Ritardo di inserimento**
        >
        > Per verificare la **latenza** che può verificarsi tra la generazione di un evento nell'origine e l'inserimento in Azure Sentinel e per garantire  una copertura completa senza duplicazione dei dati, Azure Sentinel esegue le regole di analisi pianificate con un ritardo di cinque minuti rispetto all'ora pianificata.
        >
        > Per una spiegazione tecnica dettagliata del motivo per cui questo ritardo è necessario e di come risolve questo problema, vedere l'eccellente post di blog di Ron Marsiano sull'argomento["](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)Gestione del ritardo di inserimento Azure Sentinel regole di avviso pianificate ".

- Usare la **sezione Soglia di** avviso per definire il livello di riservatezza della regola. Ad esempio, impostare Genera avviso quando  il numero di risultati della query è maggiore di e immettere il numero 1000 se si vuole che la regola generi un avviso solo se la **query** restituisce più di 1000 risultati ogni volta che viene eseguita. Si tratta di un campo obbligatorio, quindi se non si vuole impostare una soglia, ad esempio se si vuole che l'avviso registri ogni evento, immettere 0 nel campo numero.
    
### <a name="results-simulation"></a>Simulazione dei risultati

**Nell'area** Simulazione risultati, sul lato destro  della procedura guidata, selezionare Test con i dati correnti e Azure Sentinel mostrerà un grafico dei risultati (eventi di log) che la query avrebbe generato nelle ultime 50 volte che sarebbe stata eseguita, in base alla pianificazione attualmente definita. Se si modifica la query, selezionare di nuovo Test con **i dati correnti** per aggiornare il grafico. Il grafico mostra il numero di risultati nel periodo di tempo definito, determinato dalle impostazioni nella sezione **Pianificazione query.**
  
Ecco l'aspetto della simulazione dei risultati per la query nello screenshot precedente. Il lato sinistro è la visualizzazione predefinita e il lato destro è quello visualizzato quando si passa il mouse su un punto nel tempo sul grafico.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Screenshot della simulazione dei risultati":::

Se la query attiva troppi avvisi o avvisi troppo frequenti, è  possibile provare  [](#query-scheduling-and-alert-threshold) a usare le impostazioni nelle sezioni Pianificazione query e Soglia avviso e selezionare di nuovo **Test** con i dati correnti.

### <a name="event-grouping-and-rule-suppression"></a>Raggruppamento di eventi ed eliminazione delle regole

> [!IMPORTANT]
> Il raggruppamento di eventi è attualmente disponibile in **ANTEPRIMA.** Per altre note legali applicabili alle funzionalità di Azure in versione beta, in anteprima o altrimenti non ancora rilasciate nella disponibilità [generale, vedere](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Condizioni supplementari per l'utilizzo per le anteprime di Microsoft Azure.
    
- In **Raggruppamento di eventi** scegliere uno dei due modi per gestire il raggruppamento di **eventi** in **avvisi:** 

    - **Raggruppare tutti gli eventi in un singolo avviso** (impostazione predefinita). La regola genera un singolo avviso ogni volta che viene eseguita, purché la query restituisca più risultati rispetto alla soglia **di avviso specificata sopra.** L'avviso include un riepilogo di tutti gli eventi restituiti nei risultati. 

    - **Attiva un avviso per ogni evento**. La regola genera un avviso univoco per ogni evento restituito dalla query. Ciò è utile se si vuole che gli eventi siano visualizzati singolarmente o se si vuole raggrupparli in base a determinati parametri, ad esempio per utente, nome host o altro. È possibile definire questi parametri nella query.
    
        Attualmente, il numero di avvisi che una regola può generare è limitato a 20. Se in una determinata regola **il** raggruppamento di eventi è impostato su Attiva un avviso per ogni evento e la query della regola restituisce più di 20 eventi, ognuno dei primi 19 eventi genererà un avviso univoco e il 20° avviso riepiloga l'intero set di eventi restituiti. In altre parole, il 20° avviso è ciò che sarebbe stato generato nell'opzione Raggruppa tutti gli **eventi in un singolo** avviso.

    > [!NOTE]
    > Qual è la differenza tra **eventi** e **avvisi?**
    >
    > - Un **evento** è una descrizione di una singola occorrenza di un'azione. Ad esempio, una singola voce in un file di log può essere conteggiato come evento. In questo contesto un evento fa riferimento a un singolo risultato restituito da una query in una regola di analisi.
    >
    > - Un **avviso** è una raccolta di eventi che, nel loro insieme, sono significativi dal punto di vista della sicurezza. Un avviso può contenere un singolo evento se l'evento ha implicazioni significative sulla sicurezza, ad esempio un accesso amministrativo da un paese esterno al di fuori dell'orario di ufficio.
    >
    > - A proposito, cosa sono **gli eventi imprevisti?** Azure Sentinel logica interna di crea eventi **imprevisti** da **avvisi** o gruppi di avvisi. La coda degli eventi imprevisti è il punto focale del lavoro degli analisti SOC: analisi, analisi e correzione.
    > 
    > Azure Sentinel gli eventi non elaborati da alcune origini dati e gli avvisi già elaborati da altre. È importante notare quale si sta occupando in qualsiasi momento.

- Nella sezione **Eliminazione** è possibile attivare l'impostazione Interrompi esecuzione **query** dopo la generazione dell'avviso se, dopo aver visualizzato un avviso, si vuole sospendere l'operazione di questa regola per un periodo di tempo che supera l'intervallo di query.  Se si attiva questa opzione, è necessario impostare Interrompi **esecuzione query per** per il periodo di tempo in cui l'esecuzione della query deve essere interrotta, fino a 24 ore.

## <a name="configure-the-incident-creation-settings"></a>Configurare le impostazioni di creazione dell'evento imprevisto

Nella scheda **Impostazioni eventi** imprevisti è possibile scegliere se e come Azure Sentinel gli avvisi in eventi imprevisti utilizzabili. Se questa scheda viene lasciata sola, Azure Sentinel un singolo evento imprevisto separato da ogni avviso. È possibile scegliere di non creare eventi imprevisti o raggruppare più avvisi in un singolo evento imprevisto modificando le impostazioni in questa scheda.

> [!IMPORTANT]
> La scheda delle impostazioni dell'evento imprevisto è attualmente disponibile in **ANTEPRIMA.** Per altre condizioni legali applicabili alle funzionalità di Azure in versione beta, in anteprima o altrimenti non ancora rilasciate nella disponibilità [generale, vedere](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Le condizioni per l'utilizzo supplementari per le Microsoft Azure preview.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definire le impostazioni di creazione dell'evento imprevisto e raggruppamento di avvisi":::

### <a name="incident-settings"></a>Impostazioni degli eventi imprevisti

Nella sezione **Impostazioni**  evento imprevisto l'opzione Crea eventi imprevisti da avvisi attivati da questa regola di analisi è impostata per impostazione predefinita su **Abilitato,** ovvero Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso attivato dalla regola.
    
- Se non si vuole che questa regola comporta la creazione di eventi imprevisti (ad esempio, se questa regola è solo per raccogliere informazioni per l'analisi successiva), impostare questa opzione su **Disabilitato.**

- Se si vuole creare un singolo evento imprevisto da un gruppo di avvisi, anziché uno per ogni singolo avviso, vedere la sezione successiva.

### <a name="alert-grouping"></a>Raggruppamento di avvisi

Nella  sezione Raggruppamento avvisi, se si vuole che un singolo evento imprevisto sia generato da un gruppo di un massimo di 150 avvisi simili o ricorrenti (vedere la nota), impostare Raggruppa gli avvisi correlati, attivati da questa regola di **analisi,** in Eventi imprevisti **su** Abilitato e impostare i parametri seguenti.

- **Limita il gruppo agli avvisi creati nell'intervallo** di tempo selezionato: determinare l'intervallo di tempo entro il quale gli avvisi simili o ricorrenti verranno raggruppati. Tutti gli avvisi corrispondenti entro questo intervallo di tempo genereranno collettivamente un evento imprevisto o un set di eventi imprevisti (a seconda delle impostazioni di raggruppamento riportate di seguito). Gli avvisi al di fuori di questo intervallo di tempo genereranno un evento imprevisto separato o un set di eventi imprevisti.

- **Raggruppa gli avvisi attivati da questa regola di analisi in un** singolo evento imprevisto in base a : scegliere la base in base alla quale gli avvisi verranno raggruppati:

    - **Raggruppare** gli avvisi in un singolo evento imprevisto se tutte le entità corrispondono: gli avvisi vengono raggruppati se condividono valori identici per ogni entità mappata (definita nella scheda Imposta logica regola sopra). Questa è l'impostazione consigliata.

    - **Raggruppa tutti gli avvisi attivati da** questa regola in un singolo evento imprevisto: tutti gli avvisi generati da questa regola vengono raggruppati anche se non condividono valori identici.

    - **Raggruppa gli** avvisi in un singolo evento imprevisto se le entità selezionate corrispondono: gli avvisi vengono raggruppati se condividono valori identici per alcune delle entità mappate (che è possibile selezionare dall'elenco a discesa). È possibile usare questa impostazione se, ad esempio, si vogliono creare eventi imprevisti separati in base agli indirizzi IP di origine o di destinazione.

- **Riaperre** eventi imprevisti corrispondenti chiusi: se un evento imprevisto è stato risolto e chiuso e successivamente viene generato un altro avviso che  dovrebbe appartenere a tale evento imprevisto, impostare questa impostazione su **Abilitato** se si vuole riaperre l'evento imprevisto chiuso e lasciare disabilitato se si vuole che l'avviso crei un nuovo evento imprevisto.
    
    > [!NOTE]
    > **È possibile raggruppare fino a 150** avvisi in un singolo evento imprevisto. Se più di 150 avvisi vengono generati da una regola che li raggruppa in un singolo evento imprevisto, verrà generato un nuovo evento imprevisto con gli stessi dettagli dell'evento imprevisto originale e gli avvisi in eccesso verranno raggruppati nel nuovo evento imprevisto.

## <a name="set-automated-responses-and-create-the-rule"></a>Impostare risposte automatiche e creare la regola

1. Nella scheda **Risposte automatiche** selezionare i playbook da eseguire automaticamente quando viene generato un avviso dalla regola personalizzata. Per altre informazioni sulla creazione e l'automazione dei playbook, vedere [Rispondere alle minacce](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Definire le impostazioni di risposta automatica":::

1. Selezionare **Rivedi e crea** per esaminare tutte le impostazioni per la nuova regola di avviso. Quando viene visualizzato il messaggio "Convalida superata", selezionare **Crea per** inizializzare la regola di avviso.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Esaminare tutte le impostazioni e creare la regola":::

## <a name="view-the-rule-and-its-output"></a>Visualizzare la regola e il relativo output
  
- È possibile trovare la regola personalizzata appena creata (di tipo "Pianificato") nella tabella nella scheda **Regole** attive nella schermata **di Analisi** principale. Da questo elenco è possibile abilitare, disabilitare o eliminare ogni regola.

- Per visualizzare i risultati delle regole di  avviso create, passare alla pagina Eventi imprevisti, in cui è possibile esaminare [gli](tutorial-investigate-cases.md)eventi imprevisti e correggere le minacce.

> [!NOTE]
> Gli avvisi generati in Azure Sentinel sono disponibili [tramite Microsoft Graph Sicurezza](/graph/security-concept-overview). Per altre informazioni, vedere la documentazione [Microsoft Graph avvisi di sicurezza.](/graph/api/resources/security-api-overview)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="issue-no-events-appear-in-query-results"></a>Problema: non vengono visualizzati eventi nei risultati della query

Se  il raggruppamento di eventi è impostato in modo da attivare un avviso per ogni evento **,** in determinati scenari, quando si visualizzano i risultati della query in un secondo momento (ad esempio quando si esegue il pivot negli avvisi di un evento imprevisto), è possibile che non venga visualizzato alcun risultato della query. Questo avviene perché la connessione dell'evento all'avviso viene eseguita tramite l'hashing delle informazioni dell'evento specifico e l'inclusione dell'hash nella query. Se i risultati della query sono stati modificati dopo la generazione dell'avviso, l'hash non sarà più valido e non verrà visualizzato alcun risultato. 

Per visualizzare gli eventi, rimuovere manualmente la riga con l'hash dalla query della regola ed eseguire la query.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problema: non è stato possibile eseguire una regola pianificata o viene visualizzata l'opzione AUTO DISABLED aggiunta al nome

È raro che l'esecuzione di una regola di query pianificata non riesca, ma può verificarsi. Azure Sentinel classifica gli errori in anticipo come temporanei o permanenti, in base al tipo specifico di errore e alle circostanze che lo hanno causato.

#### <a name="transient-failure"></a>Errore temporaneo

Un errore temporaneo si verifica a causa di una circostanza temporanea che presto tornerà alla normalità, a quel punto l'esecuzione della regola avrà esito positivo. Ecco alcuni esempi di errori Azure Sentinel classificati come temporanei:

- L'esecuzione di una query della regola richiede troppo tempo e si timeout.
- Problemi di connettività tra origini dati e Log Analytics o tra Log Analytics e Azure Sentinel.
- Qualsiasi altro errore nuovo e sconosciuto viene considerato temporaneo.

In caso di errore temporaneo, Azure Sentinel continua a tentare di eseguire di nuovo la regola dopo intervalli predeterminati e in continua crescita, fino a un certo punto. Successivamente, la regola verrà eseguita di nuovo solo alla successiva ora pianificata. Una regola non verrà mai disabilitata automaticamente a causa di un errore temporaneo.

#### <a name="permanent-failure---rule-auto-disabled"></a>Errore permanente : regola disabilitata automaticamente

Un errore permanente si verifica a causa di una modifica delle condizioni che consentono l'esecuzione della regola, che senza l'intervento umano non tornerà allo stato precedente. Di seguito sono riportati alcuni esempi di errori classificati come permanenti:

- L'area di lavoro di destinazione (in cui è stata eseguita la query della regola) è stata eliminata.
- La tabella di destinazione (in cui è stata eseguita la query della regola) è stata eliminata.
- Azure Sentinel stato rimosso dall'area di lavoro di destinazione.
- Una funzione usata dalla query della regola non è più valida. è stato modificato o rimosso.
- Le autorizzazioni per una delle origini dati della query della regola sono state modificate.
- Una delle origini dati della query della regola è stata eliminata o disconnessa.

**In caso di un numero predeterminato di errori permanenti consecutivi dello stesso tipo e della stessa regola,** Azure Sentinel interrompe il tentativo di eseguire la regola ed esegue anche la procedura seguente:

- Disabilita la regola.
- Aggiunge le **parole "AUTO DISABLED"** all'inizio del nome della regola.
- Aggiunge il motivo dell'errore (e la disabilitazione) alla descrizione della regola.

È possibile determinare facilmente la presenza di eventuali regole disabilitate automaticamente ordinando l'elenco di regole in base al nome. Le regole disabilitate automaticamente si trovaranno nella parte superiore o nella parte superiore dell'elenco.

I responsabili SOC devono assicurarsi di controllare regolarmente l'elenco delle regole per la presenza di regole disabilitate automaticamente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel.

- Informazioni su come [analizzare gli eventi imprevisti in Azure Sentinel](tutorial-investigate-cases.md).
- Informazioni sulle [entità in Azure Sentinel](entities-in-azure-sentinel.md).
- Informazioni su come [configurare risposte di minacce automatizzate in Azure Sentinel](tutorial-respond-threats-playbook.md).
