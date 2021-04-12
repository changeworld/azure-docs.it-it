---
title: Risolvere i problemi di monitoraggio del desktop virtuale di Windows-Azure
description: Come risolvere i problemi relativi a monitoraggio di Azure per desktop virtuale di Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a104f4d33e1bd38e130101b34d3fd2021de27cd2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445483"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Risolvere i problemi di monitoraggio di Azure per desktop virtuale Windows

Questo articolo presenta i problemi noti e le soluzioni per i problemi comuni in monitoraggio di Azure per desktop virtuale di Windows.

## <a name="issues-with-configuration-and-setup"></a>Problemi relativi alla configurazione e all'installazione

Se la cartella di lavoro di configurazione non funziona correttamente per automatizzare l'installazione, è possibile usare queste risorse per configurare manualmente l'ambiente:

- Per abilitare manualmente la diagnostica o accedere all'area di lavoro Log Analytics, vedere [inviare diagnostica desktop virtuali Windows a log Analytics](diagnostics-log-analytics.md).
- Per installare manualmente l'estensione Log Analytics in un host sessione, vedere [log Analytics estensione macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md).
- Per configurare una nuova area di lavoro Log Analytics, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/logs/quick-create-workspace.md).
- Per aggiungere, rimuovere o modificare i contatori delle prestazioni, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/agents/data-sources-performance-counters.md).
- Per configurare i registri eventi di Windows per un'area di lavoro Log Analytics, vedere [raccogliere origini dati del registro eventi di Windows con log Analytics Agent](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>I dati non vengono visualizzati correttamente

Se i dati non vengono visualizzati correttamente, controllare le soluzioni comuni seguenti:

- Per prima cosa, verificare di aver configurato correttamente con la cartella di lavoro di configurazione, come descritto in [usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md). Se mancano contatori o eventi, i dati associati non verranno visualizzati nel portale di Azure.
- Controllare le autorizzazioni di accesso & contattare i proprietari delle risorse per richiedere autorizzazioni mancanti. chiunque Monitoring desktop virtuale di Windows richiede le autorizzazioni seguenti:
    - Accesso in lettura alle sottoscrizioni di Azure che contengono le risorse del desktop virtuale Windows
    - Accesso in lettura ai gruppi di risorse della sottoscrizione che contengono gli host sessione desktop virtuale Windows 
    - Accesso in lettura a qualsiasi area di lavoro Log Analytics in uso
- Potrebbe essere necessario aprire le porte in uscita nel firewall del server per consentire a monitoraggio di Azure e Log Analytics di inviare dati al portale. Per informazioni su come eseguire questa operazione, vedere gli articoli seguenti:
      - [Porte in uscita di monitoraggio di Azure](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics i requisiti del firewall](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Non vengono visualizzati dati dalle attività recenti? Potrebbe essere necessario attendere 15 minuti e aggiornare il feed. Monitoraggio di Azure prevede un periodo di latenza di 15 minuti per il popolamento dei dati di log. Per altre informazioni, vedere [tempo di inserimento dei dati di log in monitoraggio di Azure](../azure-monitor/logs/data-ingestion-time.md).

Se non sono presenti informazioni ma i dati non sono ancora visualizzati correttamente, potrebbe essersi verificato un problema nella query o nelle origini dati. Esaminare i [problemi noti e le limitazioni](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Si vuole personalizzare monitoraggio di Azure per desktop virtuale Windows

Monitoraggio di Azure per desktop virtuale Windows usa le cartelle di lavoro di monitoraggio di Azure. Le cartelle di lavoro di consentono di salvare una copia del modello di cartella di lavoro di desktop virtuale Windows e di creare personalizzazioni personalizzate.

Per impostazione predefinita, i modelli di cartella di lavoro personalizzati non adotteranno automaticamente gli aggiornamenti dal gruppo di prodotti. Per ulteriori informazioni, vedere la pagina relativa alla [risoluzione dei problemi relativi alle informazioni dettagliate basate sulla cartella di lavoro](../azure-monitor/insights/troubleshoot-workbooks.md) e alla [Panoramica delle cartelle di lavoro](../azure-monitor/visualize/workbooks-overview.md)

## <a name="i-cant-interpret-the-data"></a>Non è possibile interpretare i dati

Per altre informazioni sui termini di dati, vedere il [Glossario monitoraggio di Azure per desktop virtuale di Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>I dati necessari non sono disponibili

Se si desidera monitorare più contatori delle prestazioni o registri eventi di Windows, è possibile abilitarli per inviare informazioni di diagnostica all'area di lavoro di Log Analytics e monitorarli in **diagnostica host: browser host**. 

- Per aggiungere contatori delle prestazioni, vedere [configurazione dei contatori delle prestazioni](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Per aggiungere eventi di Windows, vedere [configurazione dei registri eventi di Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Non è possibile trovare un punto dati per facilitare la diagnosi di un problema? Invia commenti e suggerimenti

- Per informazioni su come lasciare commenti, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto per desktop virtuale di Windows](troubleshoot-set-up-overview.md).
- È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Limitazioni e problemi noti

Di seguito sono riportati i problemi e le limitazioni che sono consapevoli e che è necessario risolvere:

- È possibile monitorare un solo pool di host alla volta. 
- Per salvare le impostazioni preferite, è necessario salvare un modello personalizzato della cartella di lavoro. I modelli personalizzati non adotteranno automaticamente gli aggiornamenti dal gruppo di prodotti.
- Quando si caricano le selezioni, nella cartella di lavoro di configurazione a volte vengono visualizzati errori di "query non riuscita". Aggiornare la query, immettere nuovamente la selezione, se necessario, e l'errore dovrebbe risolversi automaticamente. 
- Alcuni messaggi di errore non sono formulati in modo intuitivo e non tutti i messaggi di errore sono descritti nella documentazione.
- Il contatore delle prestazioni Total Sessions può superare le sessioni con un numero ridotto e il totale delle sessioni potrebbe sembrare superiore al limite massimo delle sessioni.
- Il numero di sessioni disponibili non riflette i criteri di scalabilità nel pool host.   
- Si verificano tempi di connessione inaspettati o contraddizioni? Sebbene sia raro, un evento di completamento della connessione può essere mancante e può influito su alcuni oggetti visivi e metriche.
- Il tempo per la connessione include il tempo impiegato dagli utenti per immettere le proprie credenziali. Questo è correlato all'esperienza, ma in alcuni casi può mostrare picchi falsi. 
    

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere [usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione](azure-monitor.md).
- Per stimare, misurare e gestire i costi di archiviazione dei dati, vedere [stimare i costi di monitoraggio di Azure](azure-monitor-costs.md).
- Consulta il [Glossario](azure-monitor-glossary.md) per scoprire di più su termini e concetti correlati a monitoraggio di Azure per desktop virtuale di Windows.
