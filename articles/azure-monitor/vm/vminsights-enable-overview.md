---
title: Abilitare la Panoramica di VM Insights
description: Informazioni su come distribuire e configurare VM Insights. Individuare i requisiti di sistema.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: bb2e12082b80c397eec27409b1177379a92fdd7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634159"
---
# <a name="enable-vm-insights-overview"></a>Abilitare la Panoramica di VM Insights

Questo articolo offre una panoramica delle opzioni disponibili per abilitare VM Insights per monitorare l'integrità e le prestazioni dei seguenti elementi:

- Macchine virtuali di Azure 
- Set di scalabilità di macchine virtuali di Azure
- Macchine virtuali ibride connesse ad Azure Arc
- Macchine virtuali locali
- Macchine virtuali ospitate in un altro ambiente cloud.  

Per configurare VM Insights:

* Abilitare una singola macchina virtuale di Azure, un set di scalabilità di macchine virtuali di Azure o un computer Azure Arc selezionando **Insights** direttamente dal menu nel portale di Azure.
* Abilitare più macchine virtuali di Azure, macchine virtuali di Azure o macchine Azure ARC usando criteri di Azure. Questo metodo garantisce che nelle VM nuove e esistenti e nei set di scalabilità siano installate e configurate correttamente le dipendenze necessarie. Le macchine virtuali non conformi e i set di scalabilità vengono segnalati, pertanto è possibile decidere se abilitarli e risolverli.
* Abilita più macchine virtuali di Azure, macchine virtuali di Azure Arc, set di scalabilità di macchine virtuali di Azure o macchine di Azure Arc in una sottoscrizione o un gruppo di risorse specifico usando PowerShell.
* Abilitare VM Insights per monitorare le macchine virtuali o i computer fisici ospitati nella rete aziendale o in un altro ambiente cloud.

## <a name="supported-machines"></a>Computer supportati
VM Insights supporta i computer seguenti:

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchina virtuale ibrida connessa ad Azure Arc


## <a name="supported-azure-arc-machines"></a>Computer Azure Arc supportati
VM Insights è disponibile per i server abilitati per Azure Arc in aree in cui è disponibile il servizio estensione ARC. È necessario eseguire la versione 0,9 o successiva dell'agente Arc.

| Origine connessa | Supportato | Descrizione |
|:--|:--|:--|
| Agenti di Windows | Sì | Insieme all' [agente log Analytics per Windows](../agents/log-analytics-agent.md), gli agenti Windows necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](../agents/agents-overview.md#supported-operating-systems). |
| Agenti Linux | Sì | Insieme all' [agente log Analytics per Linux](../agents/log-analytics-agent.md), gli agenti Linux necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No | |

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

VM Insights supporta qualsiasi sistema operativo che supporta l'agente di Log Analytics e l'agente di dipendenza. Per un elenco completo, vedere [Panoramica degli agenti di monitoraggio di Azure ](../agents/agents-overview.md#supported-operating-systems) .

> [!IMPORTANT]
> La funzionalità di integrità Guest di VM Insights offre un supporto più limitato del sistema operativo mentre è in anteprima pubblica. Per un elenco dettagliato, vedere [abilitare l'integrità Guest di VM Insights (anteprima)](../vm/vminsights-health-enable.md) .

### <a name="linux-considerations"></a>Considerazioni su Linux
Vedere l'elenco seguente di considerazioni sul supporto Linux di Dependency Agent che supporta VM Insights:

- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio Estensione indirizzo fisico (Physical Address Extension, PAE) e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione *2.6.16.21-0.8-xen*, ad esempio, non è supportato.
- I kernel personalizzati, incluse le ricompilazioni dei kernel standard, non sono supportati.
- Per le distribuzioni Debian diverse dalla versione 9,4, la funzionalità mappa non è supportata e la funzionalità prestazioni è disponibile solo dal menu monitoraggio di Azure. Non è disponibile direttamente dal riquadro sinistro della macchina virtuale di Azure.
- Il kernel CentOSPlus è supportato.

Per le vulnerabilità di Spectre e Meltdown è necessario applicare patch al kernel Linux. Per ulteriori informazioni, consultare il fornitore della distribuzione Linux. Eseguire il comando seguente per verificare la disponibilità se Spectre/Meltdown è stato mitigato:

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

L'output per questo comando sarà simile al seguente e specificherà se un computer è vulnerabile a entrambi i problemi. Se questi file non sono presenti, viene applicata la patch per il computer.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Per VM Insights è necessaria un'area di lavoro Log Analytics. Per informazioni dettagliate e i requisiti di questa area di lavoro, vedere [configurare log Analytics area di lavoro per VM Insights](vminsights-configure-workspace.md) .
## <a name="agents"></a>Agenti
VM Insights richiede l'installazione dei due agenti seguenti in ogni macchina virtuale o set di scalabilità di macchine virtuali da monitorare. Per eseguire l'onboarding della risorsa, installare questi agenti e connetterli all'area di lavoro.  Vedere [requisiti di rete](../agents/log-analytics-agent.md#network-requirements) per i requisiti di rete per questi agenti.

- [Agente log Analytics](../agents/log-analytics-agent.md). Raccoglie gli eventi e i dati sulle prestazioni dalla macchina virtuale o dal set di scalabilità di macchine virtuali e li recapita all'area di lavoro Log Analytics. I metodi di distribuzione per l'agente di Log Analytics nelle risorse di Azure usano l'estensione della macchina virtuale per [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md).
- Dependency Agent. Raccoglie i dati individuati sui processi in esecuzione nella macchina virtuale e sulle dipendenze del processo esterno, che vengono usate dalla [funzionalità mappa in VM Insights](../vm/vminsights-maps.md). Dependency Agent si basa sull'agente di Log Analytics per recapitare i dati a monitoraggio di Azure. I metodi di distribuzione per Dependency Agent nelle risorse di Azure usano l'estensione della macchina virtuale per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> L'agente di Log Analytics è lo stesso agente usato da System Center Operations Manager. VM Insights consente di monitorare gli agenti monitorati anche da Operations Manager se sono connessi direttamente e si installa l'agente di dipendenza su di essi. Gli agenti connessi a monitoraggio di Azure tramite una [connessione del gruppo di gestione](../tform/../agents/om-agents.md) non possono essere monitorati da VM Insights.

Di seguito sono riportati diversi metodi per la distribuzione di questi agenti. 

| Metodo | Descrizione |
|:---|:---|
| [Azure portal](../vm/vminsights-enable-portal.md) | Installare entrambi gli agenti in un'unica macchina virtuale, un set di scalabilità di macchine virtuali o macchine virtuali ibride connesse ad Azure Arc. |
| [Modelli di Gestione risorse](../vm/vminsights-enable-resource-manager.md) | Installare entrambi gli agenti usando uno dei metodi supportati per distribuire un modello di Gestione risorse, ad esempio l'interfaccia della riga di comando e PowerShell. |
| [Criteri di Azure](../vm/vminsights-enable-policy.md) | Assegnare ad Azure Policy Initiative per installare automaticamente gli agenti quando viene creata una macchina virtuale o un set di scalabilità di macchine virtuali. |
| [Installazione manuale](../vm/vminsights-enable-hybrid.md) | Installare gli agenti nel sistema operativo guest nei computer ospitati all'esterno di Azure, inclusi nel Data Center o in altri ambienti cloud. |


## <a name="network-requirements"></a>Requisiti di rete

- Per informazioni sui requisiti di rete per l'agente di Log Analytics, vedere [requisiti di rete](../agents/log-analytics-agent.md#network-requirements) .
- Dependency Agent richiede una connessione dalla macchina virtuale all'indirizzo 169.254.169.254. Si tratta dell'endpoint del servizio metadati di Azure. Verificare che le impostazioni del firewall consentano le connessioni a questo endpoint.


## <a name="management-packs"></a>Management Pack
Quando un'area di lavoro Log Analytics è configurata per VM Insights, due Management Pack vengono trasmessi a tutti i computer Windows connessi a tale area di lavoro. I Management Pack sono denominati *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* e *Microsoft. IntelligencePacks. VMInsights* e vengono scritti in *%Programmi%\Microsoft Monitoring Agent\Agent\Health Service state\management Packs Packs*. 

L'origine dati utilizzata dal Management Pack *ApplicationDependencyMonitor* è **% Program%programmi%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. L'origine dati utilizzata dal *VMInsights* Management Pack è *% Program%programmi%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per migliorare la qualità, la sicurezza e l'integrità del servizio. 

Per fornire funzionalità di risoluzione dei problemi accurate ed efficienti, la funzionalità map include i dati sulla configurazione del software. I dati forniscono informazioni come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la funzionalità di monitoraggio delle prestazioni, vedere [visualizzare le prestazioni di VM Insights](../vm/vminsights-performance.md). Per visualizzare le dipendenze dell'applicazione individuate, vedere [visualizzare la mappa di VM Insights](../vm/vminsights-maps.md).
