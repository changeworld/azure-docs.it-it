---
title: Creare regole di analisi personalizzate per rilevare le minacce con Azure Sentinel | Microsoft Docs
description: Usare questa esercitazione per imparare a creare regole di analisi personalizzate per rilevare le minacce alla sicurezza con Azure Sentinel. Sfruttare i vantaggi offerti dal raggruppamento di eventi, dal raggruppamento di avvisi e dall'arricchimento degli avvisi, oltre a comprendere automaticamente DISABILITAto.
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
ms.openlocfilehash: 6f0a94daef8c5db820a17fe8cb50eda616bcf260
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453936"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Esercitazione: creare regole di analisi personalizzate per rilevare le minacce

Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, è possibile creare regole di analisi personalizzate che consentono di individuare le minacce e i comportamenti anomali presenti nell'ambiente. Queste regole eseguono la ricerca di eventi o set di eventi specifici nell'ambiente, generano un avviso quando vengono raggiunte determinate soglie o condizioni di evento, generano eventi imprevisti per il SOC per la valutazione e l'analisi e rispondono alle minacce con processi di monitoraggio e aggiornamento automatici. 

Questa esercitazione consente di creare regole personalizzate per rilevare le minacce con Azure Sentinel.

Al termine dell'esercitazione, sarà possibile effettuare le operazioni seguenti:
> [!div class="checklist"]
> * Creare regole di analisi
> * Definire la modalità di elaborazione degli eventi e degli avvisi
> * Definire la modalità di generazione degli avvisi e degli eventi imprevisti
> * Scegliere le risposte automatiche alle minacce per le regole

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Creare una regola di analisi personalizzata con una query pianificata

1. Dal menu di navigazione di Azure Sentinel selezionare **Analytics**.

1. Nella barra delle azioni nella parte superiore selezionare **+ Crea** e selezionare **regola di query pianificata**. Verrà visualizzata la **creazione guidata regola di analisi**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Crea query pianificata" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Creazione guidata regola di analisi-scheda generale

- Consente di specificare un **nome** univoco e una **Descrizione**. 

- Nel campo **tattiche** è possibile scegliere tra le categorie di attacchi per la classificazione della regola. Si basano sulle tattiche della [Mitre ATT&](https://attack.mitre.org/) il Framework CK.

- Impostare la **gravità** dell'avviso in base alle esigenze. 

- Quando si crea la regola, il relativo **stato** è **abilitato** per impostazione predefinita, il che significa che verrà eseguito immediatamente dopo aver completato la creazione. Se non si vuole che venga eseguita immediatamente, selezionare **disabilitato** e la regola verrà aggiunta alla scheda **regole attive** ed è possibile abilitarla da tale posizione quando necessario.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Avviare la creazione di una regola di analisi personalizzata":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definire la logica della query della regola e configurare le impostazioni

Nella scheda **impostare la logica della regola** è possibile scrivere una query direttamente nel campo della **query della regola** oppure creare la query in log Analytics, quindi copiarla e incollarla qui.

- Le query sono scritte nel linguaggio di query Kusto (KQL). Scopri di più sui [concetti](/azure/data-explorer/kusto/concepts/) e sulle [query](/azure/data-explorer/kusto/query/)KQL e scopri questa [Guida di riferimento rapido](/azure/data-explorer/kql-quick-reference).

- Nell'esempio illustrato in questa schermata viene eseguita una query sulla tabella *SecurityEvent* per visualizzare un tipo di [eventi di accesso di Windows non riusciti](/windows/security/threat-protection/auditing/event-4625).

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Configurare la logica e le impostazioni della regola di query" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Ecco un'altra query di esempio, che avvisa l'utente quando viene creato un numero anomalo di risorse nell' [attività di Azure](../azure-monitor/platform/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Procedure consigliate per le query sulle regole
    > - La lunghezza della query deve avere una lunghezza compresa tra 1 e 10.000 caratteri e non può contenere " `search *` " o " `union *` ".
    >
    > - L'uso di funzioni ADX per creare query di Esplora dati di Azure nella finestra di query Log Analytics **non è supportato**.
    >
    > - Quando si utilizza la **`bag_unpack`** funzione in una query, se si proiettano le colonne come campi utilizzando " `project field1` " e la colonna non esiste, la query avrà esito negativo. Per evitare che ciò accada, è necessario proiettare la colonna come indicato di seguito:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Arricchimento avvisi

> [!IMPORTANT]
> Le funzionalità di arricchimento degli avvisi sono attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.
    
- Usare la sezione configurazione **mapping entità** per eseguire il mapping dei parametri dai risultati della query a entità riconosciute da Azure Sentinel. Le entità arricchiscono l'output delle regole (avvisi ed eventi imprevisti) con informazioni essenziali che fungono da blocchi predefiniti di tutti i processi di analisi e azioni correttive che seguono. Si tratta anche dei criteri in base ai quali è possibile raggruppare gli avvisi in eventi imprevisti nella scheda **Impostazioni evento imprevisto** .

    Altre informazioni sulle [entità in Sentinel di Azure](entities-in-azure-sentinel.md).

    Vedere eseguire il mapping [dei campi dati alle entità in Sentinel di Azure](map-data-fields-to-entities.md) per istruzioni complete sul mapping delle entità, oltre a informazioni importanti sulla compatibilità con le [versioni precedenti](map-data-fields-to-entities.md#notes-on-the-new-version).

- Usare la sezione configurazione dei **dettagli personalizzati** per estrarre gli elementi di dati degli eventi dalla query ed esporli negli avvisi generati da questa regola, offrendo la visibilità immediata del contenuto degli eventi negli avvisi e negli eventi imprevisti.

    Altre informazioni sull'emersione di dettagli personalizzati negli avvisi e vedere le [istruzioni complete](surface-custom-details-in-alerts.md).

### <a name="query-scheduling-and-alert-threshold"></a>Pianificazione di query e soglia di avviso

- Nella sezione **pianificazione query** impostare i parametri seguenti:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Impostare la pianificazione di query e il raggruppamento di eventi" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Impostare **Esegui query ogni** per controllare la frequenza con cui viene eseguita la query, con una frequenza pari a ogni 5 minuti o raramente come ogni 14 giorni.

    - Impostare i **dati di ricerca dagli ultimi** per determinare il periodo di tempo dei dati analizzati dalla query, ad esempio, può eseguire una query sugli ultimi 10 minuti di dati o le ultime 6 ore di dati. Il valore massimo è 14 giorni.

        > [!NOTE]
        > **Intervalli di query e periodo lookback**
        >
        >  Queste due impostazioni sono indipendenti l'una dall'altra, fino a un punto. È possibile eseguire una query a un breve intervallo che copre un periodo di tempo più lungo dell'intervallo (in effetti con query sovrapposte), ma non è possibile eseguire una query a un intervallo che supera il periodo di copertura. in caso contrario, si disporrà di gap nel code coverage complessivo delle query.
        >
        > **Ritardo di inserimento**
        >
        > Per tenere conto della **latenza** che può verificarsi tra la generazione di un evento nell'origine e l'inserimento in Sentinel di Azure e per garantire la copertura completa senza duplicazione dei dati, Azure Sentinel esegue le regole di analisi pianificate in un **ritardo di cinque minuti** dall'orario pianificato.
        >
        > Per una spiegazione tecnica dettagliata del motivo per cui questo ritardo è necessario e del modo in cui risolve questo problema, vedere il post di Blog eccellente di Ron Marsiano sull'argomento "[gestione del ritardo di inserimento nelle regole di avviso pianificate di Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)".

- Utilizzare la sezione **soglia avvisi** per definire il livello di riservatezza della regola. Ad esempio, impostare **Genera avviso quando il numero di risultati della query** su **è maggiore di** e immettere il numero 1000 se si desidera che la regola generi un avviso solo se la query restituisce più di 1000 risultati ogni volta che viene eseguito. Si tratta di un campo obbligatorio, quindi se non si vuole impostare una soglia, ovvero se si vuole che l'avviso registri ogni evento, immettere 0 nel campo numerico.
    
### <a name="results-simulation"></a>Simulazione dei risultati

Nell'area **simulazione risultati** , sul lato destro della procedura guidata, selezionare **test con dati correnti** e Azure Sentinel visualizzerà un grafico dei risultati (eventi del log) che la query avrebbe generato nelle ultime 50 volte in cui verrebbe eseguita, in base alla pianificazione attualmente definita. Se si modifica la query, selezionare di nuovo **test con dati correnti** per aggiornare il grafico. Il grafico mostra il numero di risultati nel periodo di tempo definito, determinato dalle impostazioni nella sezione **pianificazione query** .
  
Di seguito è illustrata la simulazione dei risultati per la query nella schermata precedente. Il lato sinistro è la visualizzazione predefinita e il lato destro è quello visualizzato quando si passa il mouse su un punto nel tempo sul grafico.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Schermate di simulazione dei risultati":::

Se si nota che la query attiverà troppi avvisi o troppo frequenti, è possibile provare le impostazioni nelle [sezioni](#query-scheduling-and-alert-threshold) **pianificazione query** e **soglia di avviso** e selezionare **di nuovo test con dati correnti** .

### <a name="event-grouping-and-rule-suppression"></a>Raggruppamento di eventi e eliminazione di regole

> [!IMPORTANT]
> Il raggruppamento di eventi è attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.
    
- In **raggruppamento eventi** scegliere uno dei due modi per gestire il raggruppamento degli **eventi** negli **avvisi**: 

    - **Raggruppare tutti gli eventi in un singolo avviso** (impostazione predefinita). La regola genera un singolo avviso ogni volta che viene eseguito, purché la query restituisca più risultati rispetto alla **soglia di avviso** specificata sopra. L'avviso include un riepilogo di tutti gli eventi restituiti nei risultati. 

    - **Attiva un avviso per ogni evento**. La regola genera un avviso univoco per ogni evento restituito dalla query. Questa opzione è utile se si desidera che gli eventi vengano visualizzati singolarmente o se si desidera raggrupparli in base a determinati parametri, in base all'utente, al nome host o a un altro elemento. È possibile definire questi parametri nella query.
    
        Attualmente, il numero di avvisi che una regola può generare è limitato a 20. Se in una determinata regola, il **raggruppamento di eventi** è impostato in modo da **attivare un avviso per ogni evento** e la query della regola restituisce più di 20 eventi, ognuno dei primi 19 eventi genererà un avviso univoco e il ventesimo avviso riepiloga l'intero set di eventi restituiti. In altre parole, il ventesimo avviso è quello che verrebbe generato sotto l'opzione **raggruppa tutti gli eventi in un singolo avviso** .

    > [!NOTE]
    > Qual è la differenza tra **gli eventi** e gli **avvisi**?
    >
    > - Un **evento** è una descrizione di una singola occorrenza di un'azione. Ad esempio, una singola voce in un file di log può contare come un evento. In questo contesto un evento fa riferimento a un singolo risultato restituito da una query in una regola di analisi.
    >
    > - Un **avviso** è una raccolta di eventi che, presi insieme, sono significativi dal punto di vista della sicurezza. Un avviso può contenere un solo evento se l'evento presenta implicazioni di sicurezza significative, ad esempio un account di accesso amministrativo da un paese esterno all'orario di ufficio.
    >
    > - Che cosa sono gli **eventi imprevisti**? La logica interna di Sentinel di Azure consente di creare **eventi imprevisti** da **avvisi** o gruppi di avvisi. La coda degli eventi imprevisti è il punto focale della valutazione del lavoro degli analisti SOC, dell'analisi e della correzione.
    > 
    > Azure Sentinel inserisce gli eventi non elaborati da alcune origini dati e gli avvisi già elaborati da altri. È importante tenere presente quale è la gestione in qualsiasi momento.

- Nella sezione **eliminazione** , è possibile attivare l'impostazione **Interrompi esecuzione query dopo la generazione** dell'  avviso se, dopo aver ricevuto un avviso, si desidera sospendere il funzionamento di questa regola per un periodo di tempo superiore all'intervallo di query. Se si attiva questa impostazione, è necessario impostare **Interrompi esecuzione query per** sull'intervallo di tempo durante il quale la query deve arrestarsi, fino a 24 ore.

## <a name="configure-the-incident-creation-settings"></a>Configurare le impostazioni per la creazione degli eventi imprevisti

Nella scheda **Impostazioni evento imprevisto** è possibile scegliere se e in che modo Azure Sentinel attiva gli avvisi in eventi imprevisti. Se questa scheda viene lasciata da sola, Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso. È possibile scegliere di non creare eventi imprevisti o di raggruppare più avvisi in un singolo evento imprevisto, modificando le impostazioni in questa scheda.

> [!IMPORTANT]
> La scheda Impostazioni evento imprevisto è attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definire le impostazioni per la creazione degli eventi imprevisti e il raggruppamento degli avvisi":::

### <a name="incident-settings"></a>Impostazioni degli eventi imprevisti

Nella sezione **delle impostazioni degli eventi imprevisti** , la **creazione di eventi imprevisti da avvisi attivati da questa regola di analisi** viene impostata per impostazione predefinita su **abilitato**, ovvero Azure Sentinel creerà un singolo evento imprevisto separato da ogni avviso attivato dalla regola.
    
- Se non si vuole che questa regola provochi la creazione di eventi imprevisti (ad esempio, se questa regola è solo per raccogliere informazioni per l'analisi successiva), impostare questa impostazione su **disabilitato**.

- Se si desidera creare un singolo evento imprevisto da un gruppo di avvisi, anziché uno per ogni singolo avviso, vedere la sezione successiva.

### <a name="alert-grouping"></a>Raggruppamento avvisi

Nella sezione **raggruppamento avvisi** , se si desidera che venga generato un singolo evento imprevisto da un gruppo di un massimo di 150 avvisi simili o ricorrenti (vedere la nota), impostare gli **avvisi relativi ai gruppi, attivati da questa regola di analisi, in eventi imprevisti** per **abilitati** e impostare i parametri seguenti.

- **Limitare il gruppo agli avvisi creati nell'intervallo di tempo selezionato**: determinare l'intervallo di tempo entro il quale verranno raggruppati gli avvisi simili o ricorrenti. Tutti gli avvisi corrispondenti entro questo intervallo di tempo genereranno collettivamente un evento imprevisto o un set di eventi imprevisti (a seconda delle impostazioni di raggruppamento seguenti). Gli avvisi esterni a questo intervallo di tempo genereranno un evento imprevisto o un set di eventi imprevisti distinti.

- **Avvisi di gruppo generati da questa regola di analisi in un singolo evento imprevisto da**: scegliere la base in cui verranno raggruppati gli avvisi:

    - **Raggruppare gli avvisi in un singolo evento imprevisto se tutte le entità corrispondono: gli** avvisi vengono raggruppati se condividono valori identici per ognuna delle entità mappate (definite nella scheda logica della regola di impostazione precedente). Questa è l'impostazione consigliata.

    - **Raggruppa tutti gli avvisi attivati da questa regola in un singolo evento imprevisto**: tutti gli avvisi generati da questa regola vengono raggruppati anche se non condividono valori identici.

    - **Raggruppa gli avvisi in un singolo evento imprevisto se le entità selezionate corrispondono: gli** avvisi vengono raggruppati se condividono valori identici per alcune delle entità mappate, che è possibile selezionare dall'elenco a discesa. Questa impostazione può essere utile se, ad esempio, si desidera creare eventi imprevisti distinti in base agli indirizzi IP di origine o di destinazione.

- **Riapri eventi imprevisti corrispondenti chiusi**: se un evento imprevisto è stato risolto e chiuso e successivamente viene generato un altro avviso che deve appartenere a tale evento imprevisto, impostare questa impostazione su **abilitato** se si desidera che l'evento imprevisto chiuso venga riaperto e lasciare **disabilitato** se si desidera che l'avviso crei un nuovo evento imprevisto.
    
    > [!NOTE]
    > È possibile raggruppare **fino a 150 avvisi** in un singolo evento imprevisto. Se vengono generati più di 150 avvisi da una regola che li raggruppa in un singolo evento imprevisto, verrà generato un nuovo evento imprevisto con gli stessi dettagli dell'evento imprevisto e gli avvisi in eccesso verranno raggruppati nel nuovo evento imprevisto.

## <a name="set-automated-responses-and-create-the-rule"></a>Impostare le risposte automatiche e creare la regola

1. Nella scheda **risposte automatiche** selezionare gli schemi che si desidera eseguire automaticamente quando viene generato un avviso dalla regola personalizzata. Per altre informazioni sulla creazione e l'automazione dei PlayBook, vedere [rispondere alle minacce](tutorial-respond-threats-playbook.md).

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Definire le impostazioni della risposta automatica":::

1. Selezionare **Verifica e crea** per esaminare tutte le impostazioni per la nuova regola di avviso. Quando viene visualizzato il messaggio "convalida superata", selezionare **Crea** per inizializzare la regola di avviso.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Esaminare tutte le impostazioni e creare la regola":::

## <a name="view-the-rule-and-its-output"></a>Visualizzare la regola e il relativo output
  
- È possibile trovare la regola personalizzata appena creata (di tipo "pianificato") nella tabella nella scheda **regole attive** della schermata principale di **analisi** . Da questo elenco è possibile abilitare, disabilitare o eliminare ogni regola.

- Per visualizzare i risultati delle regole di avviso create, passare alla pagina **eventi imprevisti** , in cui è possibile valutare, [esaminare gli eventi imprevisti](tutorial-investigate-cases.md)e correggere le minacce.

> [!NOTE]
> Gli avvisi generati in Sentinel di Azure sono disponibili tramite [Microsoft Graph sicurezza](/graph/security-concept-overview). Per ulteriori informazioni, vedere la [documentazione relativa agli avvisi di sicurezza Microsoft Graph](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="issue-no-events-appear-in-query-results"></a>Problema: nessun evento viene visualizzato nei risultati della query

Se il **raggruppamento di eventi** è impostato in modo da **attivare un avviso per ogni evento**, in determinati scenari, quando si visualizzano i risultati della query in un secondo momento, ad esempio quando si esegue la trasformazione tramite pivot verso gli avvisi di un evento imprevisto, è possibile che non venga visualizzato alcun risultato della query. Questo perché la connessione dell'evento all'avviso viene eseguita tramite l'hashing delle informazioni dell'evento specifico e l'inclusione dell'hash nella query. Se i risultati della query sono stati modificati dopo la generazione dell'avviso, l'hash non sarà più valido e non verrà visualizzato alcun risultato. 

Per visualizzare gli eventi, rimuovere manualmente la riga con l'hash dalla query della regola ed eseguire la query.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problema: una regola pianificata non è stata eseguita o viene visualizzata con il nome DISABILITAto automatico aggiunto al nome

Si tratta di un caso raro in cui non è possibile eseguire una regola di query pianificata, ma ciò può verificarsi. Azure Sentinel classifica gli errori in primo piano come temporanei o permanenti in base al tipo specifico di errore e alle circostanze che lo hanno generato.

#### <a name="transient-failure"></a>Errore temporaneo

Un errore temporaneo si verifica a causa di una circostanza che è temporanea e tornerà presto alla normalità, a quel punto l'esecuzione della regola avrà esito positivo. Di seguito sono riportati alcuni esempi di errori classificati da Azure Sentinel come temporanei:

- Una query di regola richiede troppo tempo per l'esecuzione e il timeout.
- Problemi di connettività tra origini dati e Log Analytics o tra Log Analytics e Sentinel di Azure.
- Eventuali altri errori nuovi e sconosciuti sono considerati temporanei.

In caso di errore temporaneo, Azure Sentinel continua a provare a eseguire nuovamente la regola dopo gli intervalli predeterminati e in continua crescita, fino a un punto. Successivamente, la regola verrà eseguita di nuovo solo al successivo orario pianificato. Una regola non verrà mai disabilitata automaticamente a causa di un errore temporaneo.

#### <a name="permanent-failure---rule-auto-disabled"></a>Errore permanente: la regola è disabilitata automaticamente

Si verifica un errore permanente a causa di una modifica nelle condizioni che consentono l'esecuzione della regola, che senza intervento umano non tornerà allo stato precedente. Di seguito sono riportati alcuni esempi di errori classificati come permanenti:

- L'area di lavoro di destinazione (su cui è stata eseguita la query della regola) è stata eliminata.
- La tabella di destinazione in cui è stata eseguita la query della regola è stata eliminata.
- Azure Sentinel è stato rimosso dall'area di lavoro di destinazione.
- Una funzione utilizzata dalla query della regola non è più valida. è stato modificato o rimosso.
- Le autorizzazioni per una delle origini dati della query della regola sono state modificate.
- Una delle origini dati della query della regola è stata eliminata o disconnessa.

**Nel caso di un numero predeterminato di errori permanenti consecutivi, dello stesso tipo e della stessa regola,** Azure Sentinel smette di provare a eseguire la regola e, inoltre, esegue i passaggi seguenti:

- Disabilita la regola.
- Aggiunge le parole **"auto disabled"** all'inizio del nome della regola.
- Aggiunge la causa dell'errore (e la disabilitazione) alla descrizione della regola.

È possibile determinare facilmente la presenza di eventuali regole disabilitate automaticamente, ordinando l'elenco di regole in base al nome. Le regole disabilitate automaticamente si troveranno nella parte superiore dell'elenco.

I responsabili SOC devono assicurarsi di controllare regolarmente l'elenco di regole per la presenza di regole disabilitate automaticamente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel.

- Informazioni su come [analizzare gli eventi imprevisti in Sentinel di Azure](tutorial-investigate-cases.md).
- Informazioni sulle [entità in Sentinel di Azure](entities-in-azure-sentinel.md).
- Informazioni su come [configurare le risposte automatiche alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).
