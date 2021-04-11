---
title: Inviare i report di backup di Azure
description: Creazione di attività automatiche per ricevere report periodici tramite posta elettronica
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: d3ec76fa1a23657578979e65c0e54bb428722244
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561339"
---
# <a name="email-azure-backup-reports"></a>Inviare i report di backup di Azure

Utilizzando la funzionalità **report di posta elettronica** disponibile nei report di backup, è possibile creare attività automatizzate per ricevere report periodici tramite posta elettronica. Questa funzionalità funziona distribuendo un'app per la logica nell'ambiente Azure che esegue query sui dati dalle aree di lavoro selezionate della Log Analytics (LA), in base agli input forniti. [Scopri di più sulle app per la logica e sui relativi prezzi](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Introduzione

Per configurare le attività di posta elettronica tramite i report di backup, seguire questa procedura:

1.  Passare a **Backup Center**  >  **backup Reports** e fare clic sulla scheda **report posta elettronica** .
2.  Creare un'attività specificando le seguenti informazioni:
    * **Dettagli attività** : il nome dell'app per la logica da creare e la sottoscrizione, il gruppo di risorse e la posizione in cui deve essere creata. Si noti che l'app per la logica può eseguire query sui dati in più sottoscrizioni, gruppi di risorse e posizioni (come selezionato nella sezione filtri dei report), ma viene creato nel contesto di una singola sottoscrizione, gruppo di risorse e località.
    * **Dati da esportare** : la scheda che si desidera esportare. È possibile creare una singola app attività per ogni scheda o inviare tramite posta elettronica tutte le schede usando una singola attività, selezionando l'opzione **tutte le schede** .
    * **Opzioni di posta elettronica**: frequenza di posta elettronica, ID di posta elettronica del destinatario e oggetto del messaggio di posta elettronica.

    ![Scheda posta elettronica](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Dopo aver fatto clic su **Invia** e **conferma**, viene creata l'app per la logica. L'app per la logica e le connessioni API associate vengono create con il tag **UsedByBackupReports: true** per facilitarne l'individuabilità. È necessario eseguire un unico passaggio di autorizzazione per l'esecuzione corretta dell'app per la logica, come descritto nella sezione seguente.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autorizzare le connessioni ai log di monitoraggio di Azure e Office 365

L'app per la logica usa il connettore [azuremonitorlogs](/connectors/azuremonitorlogs/) per eseguire una query sulle aree di lavoro della la e usa il connettore [Outlook di Office365](/connectors/office365connector/) per inviare messaggi di posta elettronica. Sarà necessario eseguire un'autorizzazione unica per questi due connettori. 
 
Per eseguire l'autorizzazione, attenersi alla procedura seguente:

1.  Passare a **app** per la logica nella portale di Azure.
2.  Cercare il nome dell'app per la logica creata e passare alla risorsa.

    ![App per la logica](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Fare clic sulla voce di menu **connessioni API** .

    ![Connessioni API](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Vengono visualizzate due connessioni con il formato `<location>-azuremonitorlogs` e `<location>-office365` , ovvero _eastus-azuremonitorlogs_ e _eastus-Office365_.
5.  Passare a ognuna di queste connessioni e selezionare la voce di menu **Modifica connessione API** . Nella schermata visualizzata selezionare **autorizza** e salvare la connessione al termine dell'autorizzazione.

    ![Autorizzare connessione](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Per verificare se l'app per la logica funziona dopo l'autorizzazione, è possibile tornare all'app per la logica, aprire **Panoramica** e selezionare **Esegui trigger** nel riquadro superiore per verificare se un messaggio di posta elettronica viene generato correttamente.

## <a name="contents-of-the-email"></a>Contenuto del messaggio di posta elettronica

* Tutti i grafici e i grafici visualizzati nel portale sono disponibili come contenuto inline nel messaggio di posta elettronica.
* Le griglie visualizzate nel portale sono disponibili come allegati *. csv nel messaggio di posta elettronica.
* I dati visualizzati nel messaggio di posta elettronica utilizzano tutti i filtri a livello di report selezionati dall'utente nel report, al momento della creazione dell'attività di posta elettronica.
* I filtri a livello di tabulazione, ad esempio il **nome dell'istanza di backup**, il nome dei **criteri** e così via, non vengono applicati. L'unica eccezione è la griglia **ottimizzazioni di conservazione** nella scheda **ottimizza** , in cui vengono applicati i filtri per la conservazione **giornaliera**, **settimanale**, **mensile** e **annuale** della relying party.
* L'intervallo di tempo e il tipo di aggregazione (per i grafici) si basano sulla selezione dell'intervallo di tempo dell'utente nei report. Se, ad esempio, la selezione dell'intervallo di tempo è di 60 giorni (conversione al tipo di aggregazione settimanale) e la frequenza di posta elettronica è giornaliera, il destinatario riceverà un messaggio di posta elettronica ogni giorno con i grafici che estendono i dati rilevati durante l'ultimo periodo di 60 giorni, con dati aggregati a livello settimanale.

## <a name="troubleshooting-issues"></a>Risoluzione dei problemi

Se non si ricevono messaggi di posta elettronica come previsto anche dopo aver completato la distribuzione dell'app per la logica, è possibile seguire questa procedura per risolvere i problemi di configurazione:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Scenario 1: ricezione di un messaggio di posta elettronica o di un messaggio di errore non riuscito

* Questo problema potrebbe verificarsi perché il connettore API Outlook non è autorizzato. Per autorizzare la connessione, seguire la procedura di autorizzazione indicata in precedenza.

* Questo problema può verificarsi anche se è stato specificato un destinatario di posta elettronica non corretto durante la creazione dell'app per la logica. Per verificare che il destinatario di posta elettronica sia stato specificato correttamente, è possibile passare all'app per la logica nella portale di Azure, aprire la finestra di progettazione dell'app per la logica e selezionare il passaggio di posta elettronica per verificare se vengono usati gli ID di posta elettronica corretti.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Scenario 2: ricezione di un messaggio di errore che indica che non è stato possibile eseguire l'app per la logica fino al completamento

Per risolvere il problema:
1.  Passare all'app per la logica nella portale di Azure.
2.  Nella parte inferiore della schermata **Panoramica** verrà visualizzata la sezione **Cronologia esecuzioni** . È possibile aprire l'esecuzione più recente e visualizzare i passaggi del flusso di lavoro non riusciti. Di seguito sono riportate alcune possibili cause:
    * Il **connettore dei log di monitoraggio di Azure non è stato autorizzato**: per risolvere il problema, seguire la procedura di autorizzazione indicata in precedenza.
    * **Errore nella query la**: nel caso in cui l'app per la logica sia stata personalizzata con le proprie query, un errore in una delle query la potrebbe causare l'esito negativo dell'app per la logica. È possibile selezionare il passaggio pertinente e visualizzare l'errore che causa la mancata esecuzione della query.

Se i problemi permangono, contattare il supporto tecnico Microsoft.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sui report di backup](./configure-reports.md)