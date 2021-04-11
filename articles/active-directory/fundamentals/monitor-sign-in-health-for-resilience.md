---
title: Monitorare lo stato di accesso dell'applicazione per la resilienza in Azure Active Directory
description: Creazione di query e notifiche per monitorare l'integrità di accesso delle applicazioni.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c32940d5fc40249257d1d0bb38ef67c12dc2ef
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029684"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Monitoraggio dello stato di accesso dell'applicazione per la resilienza

Per aumentare la resilienza dell'infrastruttura, impostare il monitoraggio dell'integrità di accesso dell'applicazione per le applicazioni critiche in modo da ricevere un avviso se si verifica un evento imprevisto. Per semplificare questa operazione, è possibile configurare gli avvisi in base alla cartella di lavoro di integrità di accesso. 

Questa cartella di lavoro consente agli amministratori di monitorare le richieste di autenticazione per le applicazioni nel tenant. Offre le funzionalità principali seguenti:

* Configurare la cartella di lavoro per il monitoraggio di tutte le app o di singole app con dati quasi in tempo reale.

* Configurare gli avvisi per notificare quando cambiano i modelli di autenticazione in modo che sia possibile analizzare ed eseguire un'azione.

* Confrontare le tendenze in un periodo, ad esempio settimana per settimana, che corrisponde all'impostazione predefinita della cartella di lavoro.

> [!NOTE]
> Per visualizzare tutte le cartelle di lavoro disponibili e i prerequisiti per l'uso, vedere [come usare le cartelle di lavoro di monitoraggio di Azure per i report](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Durante un evento di effetto, possono verificarsi due operazioni:

* Il numero di accessi per un'applicazione può essere abbassato precipitosamente perché gli utenti non riescono ad accedere.

* Il numero di errori di accesso può aumentare. 

Questo articolo illustra in dettaglio come configurare la cartella di lavoro per l'integrità di accesso per monitorare le rotture degli accessi degli utenti.

## <a name="prerequisites"></a>Prerequisiti

* Un tenant di Azure AD.

* Un utente con ruolo di amministratore globale o amministratore della sicurezza per il tenant Azure AD.

* Un'area di lavoro Log Analytics nella sottoscrizione di Azure per inviare i log ai log di monitoraggio di Azure. 

   * Informazioni su come [creare un'area di lavoro log Analytics](../../azure-monitor/logs/quick-create-workspace.md)

* Log di Azure AD integrati con i log di monitoraggio di Azure

   * Informazioni su come [integrare Azure ad log di accesso con il flusso di monitoraggio di Azure.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="configure-the-app-sign-in-health-workbook"></a>Configurare la cartella di lavoro dell'integrità di accesso dell'app 

Per accedere alle cartelle di lavoro, aprire il **portale di Azure**, selezionare **Azure Active Directory**, quindi selezionare **cartelle di lavoro**.

Verranno visualizzate le cartelle di lavoro in utilizzo, accesso condizionale e risoluzione dei problemi. La cartella di lavoro dell'integrità di accesso dell'app viene visualizzata nella sezione Usage.

Quando si usa una cartella di lavoro, è possibile che venga visualizzata nella sezione cartelle di lavoro modificate di recente.

![Screenshot che mostra la raccolta di cartelle di lavoro nella portale di Azure.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


La cartella di lavoro di accesso all'app consente di visualizzare gli eventi che si verificano con gli accessi. 

Per impostazione predefinita, la cartella di lavoro presenta due grafici. Questi grafici confrontano ciò che accade alle app adesso, rispetto allo stesso periodo di una settimana fa. Le linee blu sono correnti e le linee arancioni corrispondono alla settimana precedente.

![Screenshot che mostra i grafici sull'integrità di accesso.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**Il primo grafico è l'utilizzo orario (numero di utenti riusciti)**. Il confronto tra il numero corrente di utenti riusciti e un periodo di utilizzo tipico consente di individuare un calo nell'utilizzo che può richiedere l'analisi. Un calo della velocità di utilizzo riuscita può consentire di rilevare problemi di prestazioni e utilizzo che non possono essere rilevati dalla percentuale di errori. Se, ad esempio, gli utenti non riescono a raggiungere l'applicazione per tentare di eseguire l'accesso, non si verificheranno errori, ma solo un calo nell'utilizzo. Una query di esempio per questi dati è reperibile nella sezione seguente.

**Il secondo grafico è frequenza di errori orari**. Un picco nella frequenza degli errori può indicare un problema con i meccanismi di autenticazione. La frequenza degli errori può essere misurata solo se gli utenti possono tentare di eseguire l'autenticazione. Se gli utenti non riescono ad accedere per eseguire il tentativo, gli errori non verranno visualizzati.

È possibile configurare un avviso che notifica a un gruppo specifico quando la frequenza di utilizzo o di errore supera una soglia specificata. Una query di esempio per questi dati è reperibile nella sezione seguente.

## <a name="configure-the-query-and-alerts"></a>Configurare la query e gli avvisi

Le regole di avviso vengono create in monitoraggio di Azure ed è possibile eseguire automaticamente query salvate o ricerche nei log personalizzate a intervalli regolari.

Usare le istruzioni seguenti per creare avvisi di posta elettronica in base alle query riflesse nei grafici. Gli script di esempio seguenti invieranno una notifica tramite posta elettronica quando

* l'utilizzo riuscito scende del 90% dalla stessa ora di due giorni fa, come nel grafico di utilizzo orario della sezione precedente. 

* il tasso di errore aumenta del 90% dalla stessa ora di due giorni fa, come nel grafico della frequenza degli errori oraria nella sezione precedente. 

 Per configurare la query sottostante e impostare gli avvisi, completare i passaggi seguenti. La query di esempio verrà utilizzata come base per la configurazione. Una spiegazione della struttura della query viene visualizzata alla fine di questa sezione.

Per altre informazioni su come creare, visualizzare e gestire gli avvisi del log con monitoraggio di Azure, vedere [gestire gli avvisi del log](../../azure-monitor/alerts/alerts-log.md).

1. Nella cartella di lavoro selezionare **modifica**, quindi selezionare l' **icona della query** appena sopra il lato destro del grafico.   

   [![Screenshot che mostra la cartella di lavoro modifica.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Viene aperto il log di query.

   [![Screenshot che mostra il log di query.](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Copiare uno degli script di esempio per una nuova query kusto.  
   * [Query kusto per aumentare la frequenza degli errori](#kusto-query-for-increase-in-failure-rate)
   * [Query kusto per la riduzione dell'utilizzo](#kusto-query-for-drop-in-usage)

3. Incollare la query nella finestra e selezionare **Esegui**. Assicurarsi che venga visualizzato il messaggio completato nell'immagine seguente e che i risultati siano inferiori a tale messaggio.

   [![Screenshot che mostra i risultati della query di esecuzione.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Evidenziare la query e selezionare + **nuova regola di avviso**. 
 
   [![Screenshot che mostra la schermata nuova regola di avviso.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Configurare le condizioni di avviso. Nella sezione condizione selezionare il collegamento **quando la ricerca log personalizzata media è maggiore del numero definito dalla logica**. Nel riquadro Configura logica del segnale scorrere fino alla logica di avviso

   [![Screenshot che mostra la schermata Configura avvisi.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Valore soglia**: 0. Questo valore genera un avviso per tutti i risultati.

   * **Periodo di valutazione (in minuti)**: 2880. Questo valore esamina un'ora del tempo

   * **Frequenza (in minuti)**: 60. Questo valore imposta il periodo di valutazione su una volta all'ora nell'ora precedente.

   * Selezionare **Fine**.

6. Nella sezione **azioni** configurare le impostazioni seguenti:  

    [![Screenshot che mostra la pagina Crea regola di avviso.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * In **azioni** scegliere **Seleziona gruppo di azioni** e aggiungere il gruppo a cui si desidera ricevere una notifica degli avvisi.

   * In **Personalizza azioni** selezionare gli **avvisi di posta elettronica**.

   * Aggiungere una **riga dell'oggetto**.

7. In **Dettagli regola di avviso** configurare le impostazioni seguenti:

   * Aggiungere un nome descrittivo e una descrizione.

   * Selezionare il **gruppo di risorse** a cui aggiungere l'avviso.

   * Selezionare la **gravità** predefinita dell'avviso.

   * Selezionare **Abilita regola di avviso al momento della creazione** se si desidera che venga visualizzata immediatamente, altrimenti selezionare **Elimina avvisi**.

8. Selezionare **Crea regola di avviso**.

9. Selezionare **Salva**, immettere un nome per la query, **Salva come query con una categoria di avviso**. Quindi selezionare di nuovo **Salva** .  

   [![Screenshot che mostra il pulsante Salva query.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>Perfeziona le query e gli avvisi

Modificare le query e gli avvisi per ottenere la massima efficacia.

* Assicurarsi di testare gli avvisi.

* Modificare la sensibilità e la frequenza degli avvisi in modo da ricevere notifiche importanti. Gli amministratori possono divenire desensibili agli avvisi se ottengono troppi elementi e non hanno alcuna importanza. 

* Assicurarsi che l'indirizzo di posta elettronica da cui provengono gli avvisi nei client di posta elettronica dell'amministratore venga aggiunto all'elenco dei mittenti autorizzati. In caso contrario, le notifiche potrebbero non essere inviate a causa di un filtro antispam sul client di posta elettronica. 

* La query degli avvisi in monitoraggio di Azure può includere solo i risultati delle ultime 48 ore. Si [tratta di una limitazione corrente in base alla progettazione](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="sample-scripts"></a>Script di esempio

### <a name="kusto-query-for-increase-in-failure-rate"></a>Query kusto per aumentare la frequenza degli errori

   Il rapporto nella parte inferiore può essere regolato in base alle esigenze e rappresenta la percentuale di variazione del traffico nell'ultima ora rispetto alla stessa ora di ieri. 0,5 indica una differenza del 50% nel traffico.

```kusto

let today = SigninLogs
| where TimeGenerated > ago(1h) // Query failure rate in the last hour 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate
| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
let yesterday = SigninLogs
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")
// Optionally filter by a specific application
//| where AppDisplayName == **APP NAME**
| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday
| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)
| sort by TimeGenerated desc
| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday
| project TimeGenerated, failureRate, failureRateYesterday
// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>Query kusto per la riduzione dell'utilizzo

Nella query seguente viene confrontato il traffico nell'ultima ora alla stessa ora di ieri.
Si escludono sabato, domenica e lunedì, perché è previsto che in questi giorni la variabilità del traffico sia molto elevata nello stesso momento del giorno precedente. 

Il rapporto nella parte inferiore può essere regolato in base alle esigenze e rappresenta la percentuale di variazione del traffico nell'ultima ora rispetto alla stessa ora di ieri. 0,5 indica una differenza del 50% nel traffico.

*È necessario modificare questi valori per adattarli al modello operativo aziendale*.

```Kusto
 let today = SigninLogs // Query traffic in the last hour
| where TimeGenerated > ago(1h)
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour
| sort by TimeGenerated desc
| serialize rn = row_number();
let yesterday = SigninLogs // Query traffic at the same hour yesterday
| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday
| project TimeGenerated, AppDisplayName, UserPrincipalName
// Optionally filter by AppDisplayName to scope query to a single application
//| where AppDisplayName contains "Office 365 Exchange Online"
| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)
| sort by TimeGenerated desc
| serialize rn = row_number();
today
| join // Join data from today and yesterday together
(
yesterday
)
on rn
// Calculate the difference in number of users in the last hour compared to the same time yesterday
| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)
| extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday
// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.
| extend day = dayofweek(now())
| where day != time(6.00:00:00) // exclude Sat
| where day != time(0.00:00:00) // exclude Sun
| where day != time(1.00:00:00) // exclude Mon
| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>Creare processi per gestire gli avvisi

Dopo aver impostato la query e gli avvisi, creare processi aziendali per gestire gli avvisi.

* Chi eseguirà il monitoraggio della cartella di lavoro e quando?

* Quando viene generato un avviso, chi analizzerà?

* Quali sono le esigenze di comunicazione? Chi creerà le comunicazioni e chi li riceverà?

* Se si verifica un'interruzione, quali processi aziendali è necessario attivare?

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sulle cartelle di lavoro](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
