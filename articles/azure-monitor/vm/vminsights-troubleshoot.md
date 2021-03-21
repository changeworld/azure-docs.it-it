---
title: Risolvere i problemi di VM Insights
description: Risolvere i problemi di installazione di VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555656"
---
# <a name="troubleshoot-vm-insights"></a>Risolvere i problemi di VM Insights
Questo articolo fornisce informazioni sulla risoluzione dei problemi in caso di problemi di abilitazione o uso di VM Insights.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Non è possibile abilitare la VM Insights in un computer
Quando si carica una macchina virtuale di Azure dalla portale di Azure, si verificano i passaggi seguenti:

- Se è stata selezionata questa opzione, viene creata un'area di lavoro Log Analytics predefinita.
- Se l'agente è già installato, l'agente di Log Analytics viene installato nelle macchine virtuali di Azure usando un'estensione della macchina virtuale.
- Dependency Agent viene installato nelle macchine virtuali di Azure usando un'estensione, se è stato determinato che è obbligatorio.
  
Durante il processo di onboarding, ognuno di questi passaggi viene verificato e lo stato di notifica viene visualizzato nel portale. La configurazione dell'area di lavoro e l'installazione dell'agente richiedono in genere 5-10 minuti. Per la visualizzazione dei dati nel portale saranno necessari altri 5-10 minuti.

Se viene visualizzato un messaggio che consente di caricare la macchina virtuale dopo aver eseguito il processo di onboarding, attendere fino a 30 minuti per il completamento del processo. Se il problema persiste, vedere le sezioni seguenti per le possibili cause.

### <a name="is-the-virtual-machine-running"></a>La macchina virtuale è in esecuzione?
 Se la macchina virtuale è stata spenta per un periodo di tempo, è attualmente disattivata o è stata attivata solo di recente, non sarà possibile visualizzare i dati per un po' fino a quando non arrivano i dati.

### <a name="is-the-operating-system-supported"></a>Il sistema operativo è supportato?
Se il sistema operativo non è presente nell'elenco dei [sistemi operativi supportati](vminsights-enable-overview.md#supported-operating-systems) , l'estensione non verrà installata e verrà visualizzato il messaggio che indica che è in attesa l'arrivo dei dati.

### <a name="did-the-extension-install-properly"></a>L'estensione è stata installata correttamente?
Se viene ancora visualizzato un messaggio che indica che la macchina virtuale deve essere caricata, potrebbe significare che una o entrambe le estensioni non sono state installate correttamente. Controllare la pagina delle **estensioni** della macchina virtuale nel portale di Azure per verificare che siano elencate le estensioni seguenti.

| Sistema operativo | Agenti | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Se non vengono visualizzate entrambe le estensioni per il sistema operativo nell'elenco delle estensioni installate, è necessario che siano installate. Se le estensioni sono elencate ma il relativo stato non viene visualizzato come *provisioning riuscito*, l'estensione deve essere rimossa e reinstallata.

### <a name="do-you-have-connectivity-issues"></a>Si verificano problemi di connettività?
Per i computer Windows, è possibile usare lo strumento  *TestCloudConnectivity* per identificare il problema di connettività. Questo strumento viene installato per impostazione predefinita con l'agente nella cartella *%systemroot%\Programmi\Microsoft Files\Microsoft Monitoring Agent\Agent*. Eseguire lo strumento da un prompt dei comandi con privilegi elevati. Restituisce i risultati ed evidenzia la posizione in cui il test ha esito negativo. 

![Strumento TestCloudConnectivity](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Ulteriori informazioni sulla risoluzione dei problemi degli agenti

Per la risoluzione dei problemi relativi all'agente di Log Analytics, vedere gli articoli seguenti:

- [Come risolvere i problemi relativi all'agente di Log Analytics per Windows](../agents/agent-windows-troubleshoot.md)
- [Come risolvere i problemi relativi all'agente di Log Analytics per Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>Visualizzazione prestazioni senza dati
Se gli agenti sembrano essere installati correttamente, ma i dati non vengono visualizzati nella vista prestazioni, vedere le sezioni seguenti per le possibili cause.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>L'area di lavoro Log Analytics ha raggiunto il limite di dati?
Controllare le [prenotazioni di capacità e i prezzi per l'inserimento dei dati](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>La macchina virtuale invia i dati di log e delle prestazioni ai log di monitoraggio di Azure?

Aprire Log Analytics dai **log** nel menu monitoraggio di Azure nel portale di Azure. Eseguire la query seguente per il computer:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Se non vengono visualizzati dati, è possibile che si verifichino problemi con l'agente. Per informazioni sulla risoluzione dei problemi relativi agli agenti, vedere la sezione precedente.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>La macchina virtuale non viene visualizzata nella visualizzazione mappa

### <a name="is-the-dependency-agent-installed"></a>L'agente di dipendenza è installato?
 Utilizzare le informazioni nelle sezioni precedenti per determinare se Dependency Agent è installato e funziona correttamente.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Il livello gratuito Log Analytics?
Il [log Analytics livello gratuito](https://azure.microsoft.com/pricing/details/monitor/) è un piano tariffario legacy che consente fino a cinque macchine mapping dei servizi univoche. Tutti i computer successivi non verranno visualizzati in Mapping dei servizi, anche se i primi cinque non inviano più dati.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>La macchina virtuale invia i dati di log e delle prestazioni ai log di monitoraggio di Azure?
Usare la query di log nella [visualizzazione prestazioni non contiene alcuna sezione dati](#performance-view-has-no-data) per determinare se i dati vengono raccolti per la macchina virtuale. Se non vengono raccolti dati, usare lo strumento TestCloudConnectivity descritto in precedenza per determinare se sono presenti problemi di connettività.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>La macchina virtuale viene visualizzata nella vista mappa ma contiene dati mancanti
Se la macchina virtuale si trova nella vista mappa, l'agente di dipendenza viene installato e in esecuzione, ma il driver del kernel non è stato caricato. Controllare il file di log nei percorsi seguenti:

| Sistema operativo | Log | 
|:---|:---|
| Windows | C:\Programmi\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

Le ultime righe del file dovrebbero indicare il motivo per cui il kernel non è stato caricato. Ad esempio, il kernel potrebbe non essere supportato in Linux se è stato aggiornato.
## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sull'onboarding degli agenti di VM Insights, vedere [Enable VM Insights Overview](vminsights-enable-overview.md).
