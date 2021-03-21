---
title: Risolvere i problemi di monitoraggio desktop virtuale di Windows-Azure
description: Come risolvere i problemi relativi a monitoraggio di Azure per desktop virtuale di Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c335c1cf7e5319b812345714dbdc6b87ddc4e81b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709173"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Risolvere i problemi di monitoraggio di Azure per desktop virtuale Windows (anteprima)

>[!IMPORTANT]
>Monitoraggio di Azure per desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo presenta i problemi noti e le soluzioni per i problemi comuni in monitoraggio di Azure per desktop virtuale Windows (anteprima).

## <a name="issues-with-configuration-and-setup"></a>Problemi relativi alla configurazione e all'installazione

Se la cartella di lavoro di configurazione non funziona correttamente per automatizzare l'installazione, è possibile usare queste risorse per configurare manualmente l'ambiente:

- Per abilitare manualmente la diagnostica o accedere all'area di lavoro Log Analytics, vedere [inviare diagnostica desktop virtuali Windows a log Analytics](diagnostics-log-analytics.md).
- Per installare manualmente l'estensione Log Analytics in un host, vedere [log Analytics estensione macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md).
- Per configurare una nuova area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/logs/quick-create-workspace.md).
- Per aggiungere o rimuovere i contatori delle prestazioni, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/agents/data-sources-performance-counters.md).
- Per configurare gli eventi per un'area di lavoro Log Analytics, vedere [raccogliere origini dati del registro eventi di Windows con log Analytics Agent](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>I dati non vengono visualizzati correttamente

Se i dati non vengono visualizzati correttamente, controllare la configurazione, le autorizzazioni e verificare che gli indirizzi IP necessari siano sbloccati. 

- Assicurarsi prima di tutto che siano stati compilati tutti i campi nella cartella di lavoro di configurazione, come descritto in [usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md). Se mancano contatori o eventi, i dati associati non verranno visualizzati nel portale di Azure.

- Controllare le autorizzazioni di accesso & contattare i proprietari delle risorse per richiedere autorizzazioni mancanti. chiunque Monitoring desktop virtuale di Windows richiede le autorizzazioni seguenti:

    - Accesso in lettura alle sottoscrizioni di Azure che contengono le risorse del desktop virtuale Windows
    - Accesso in lettura ai gruppi di risorse della sottoscrizione che contengono gli host sessione desktop virtuale Windows 
    - Accesso in lettura all'area di lavoro Log Analytics

- Potrebbe essere necessario aprire le porte in uscita nel firewall del server per consentire a monitoraggio di Azure di inviare dati al portale. vedere [porte in uscita](../azure-monitor/app/ip-addresses.md). 

- Non vengono visualizzati dati dalle attività recenti? Potrebbe essere necessario attendere 15 minuti e aggiornare il feed. Monitoraggio di Azure prevede un periodo di latenza di 15 minuti per il popolamento dei dati di log. Per altre informazioni, vedere [tempo di inserimento dei dati di log in monitoraggio di Azure](../azure-monitor/logs/data-ingestion-time.md).

Se non sono presenti informazioni ma i dati non sono ancora visualizzati correttamente, potrebbe essersi verificato un problema nella query o nelle origini dati. Esaminare i problemi noti e le limitazioni. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Si vuole personalizzare monitoraggio di Azure per desktop virtuale Windows

Monitoraggio di Azure per desktop virtuale Windows usa le cartelle di lavoro di monitoraggio di Azure. Le cartelle di lavoro di consentono di salvare una copia del modello di cartella di lavoro di desktop virtuale Windows e di creare personalizzazioni personalizzate.

Per impostazione predefinita, i modelli di cartella di lavoro personalizzati non adotteranno automaticamente gli aggiornamenti dal gruppo di prodotti. Per ulteriori informazioni, vedere la pagina relativa alla [risoluzione dei problemi relativi alle informazioni dettagliate basate sulla cartella di lavoro](../azure-monitor/insights/troubleshoot-workbooks.md) e alla [Panoramica delle cartelle di lavoro](../azure-monitor/visualize/workbooks-overview.md)

## <a name="i-cant-interpret-the-data"></a>Non è possibile interpretare i dati

Per altre informazioni sui termini di dati, vedere il [Glossario monitoraggio di Azure per desktop virtuale di Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>I dati necessari non sono disponibili

Se si desidera monitorare più contatori delle prestazioni o eventi, è possibile consentirne l'invio all'area di lavoro Log Analytics e monitorarli nella diagnostica host: browser host. 

- Per aggiungere contatori delle prestazioni, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Per aggiungere eventi di Windows, vedere [configurazione dei registri eventi di Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Non è possibile trovare un punto dati per facilitare la diagnosi di un problema? Invia commenti e suggerimenti

- Per informazioni su come lasciare commenti, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto per desktop virtuale di Windows](troubleshoot-set-up-overview.md).
- È anche possibile lasciare commenti e suggerimenti per desktop virtuale Windows nell' [Hub di feedback per desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) o nel [Forum UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues-and-limitations"></a>Limitazioni e problemi noti

Si tratta di problemi e limitazioni a cui si è attualmente consapevoli e che si sta risolvendo:

- È possibile monitorare un solo pool di host alla volta. 

- Per salvare le impostazioni preferite, è necessario salvare un modello personalizzato della cartella di lavoro. I modelli personalizzati non adotteranno automaticamente gli aggiornamenti dal gruppo di prodotti.

- Alcuni messaggi di errore non vengono formulati in modo intuitivo e non tutti i messaggi di errore sono descritti nella documentazione di.

- Il contatore delle prestazioni Total Sessions può superare le sessioni con un numero ridotto e il totale delle sessioni potrebbe sembrare superiore al limite massimo delle sessioni.

- Il numero di sessioni disponibili non riflette i criteri di scalabilità nel pool host. 
    
- Sebbene sia raro, un evento di completamento della connessione può essere mancante e questo può influito su alcuni oggetti visivi come le connessioni nel tempo e sullo stato di connessione dell'utente.  
    
- La cartella di lavoro configurazione supporta solo la configurazione di host all'interno della stessa area del gruppo di risorse. 

- Il tempo per la connessione include il tempo impiegato dagli utenti per immettere le proprie credenziali. Questo è correlato all'esperienza, ma in alcuni casi può mostrare picchi falsi. 
    

## <a name="next-steps"></a>Passaggi successivi

Se non si è certi di come interpretare i dati o si desidera ottenere altre informazioni sui termini comuni, consultare il [Glossario di monitoraggio di Azure per desktop virtuali Windows](azure-monitor-glossary.md). Per informazioni su come configurare e usare monitoraggio di Azure per desktop virtuale Windows, vedere [usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md).