---
title: Panoramica dei ruoli di lavoro ibridi per runbook di Automazione di Azure
description: Questo articolo fornisce una panoramica dei ruoli di lavoro ibridi per runbook, che è possibile usare per eseguire runbook su computer nel data center locale o nel provider di servizi cloud.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2bb178302d399805eb84b233060d5717e2dba8b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830557"
---
# <a name="hybrid-runbook-worker-overview"></a>Panoramica del ruolo di lavoro ibrido per runbook

I runbook in Automazione di Azure potrebbero non avere accesso alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nella piattaforma cloud di Azure. È possibile usare la funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire i runbook direttamente nel computer che ospita il ruolo e sulle risorse nell'ambiente per gestire tali risorse locali. I runbook vengono archiviati e gestiti in Automazione di Azure e quindi recapitati a uno o più computer assegnati.

## <a name="runbook-worker-types"></a>Tipi di ruolo di lavoro runbook

Esistono due tipi di Runbook Worker: sistema e utente. Nella tabella seguente viene descritta la differenza tra di essi.

|Type | Descrizione |
|-----|-------------|
|**Sistema** |Supporta un set di runbook nascosti usati dalla funzionalità Gestione aggiornamenti che sono progettati per installare gli aggiornamenti specificati dall'utente nei computer Windows e Linux.<br> Questo tipo di ruolo di lavoro ibrido per runbook non è membro di un gruppo di ruoli di lavoro ibridi per runbook e pertanto non esegue runbook che hanno come destinazione un gruppo di ruoli di lavoro per runbook. |
|**Utente** |Supporta runbook definiti dall'utente destinati all'esecuzione diretta nel computer Windows e Linux che sono membri di uno o più gruppi di runbook worker. |

Un ruolo di lavoro ibrido per runbook può essere eseguito nel sistema operativo Windows o Linux e questo ruolo si basa sulla segnalazione dell'agente di [Log Analytics](../azure-monitor/agents/log-analytics-agent.md) a un'area di lavoro log Monitoraggio di Azure di lavoro [Log Analytics.](../azure-monitor/logs/design-logs-deployment.md) L'area di lavoro non solo consente di monitorare il computer per il sistema operativo supportato, ma anche di scaricare i componenti necessari per installare il ruolo di lavoro ibrido per runbook.

Quando Automazione di Azure [Gestione aggiornamenti](./update-management/overview.md) abilitata, qualsiasi computer connesso all'area di lavoro Log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per runbook di sistema. Per configurarlo come ruolo di lavoro ibrido per runbook di Windows, vedere Distribuire un ruolo di lavoro ibrido per [runbook](automation-windows-hrw-install.md) di Windows e per Linux. Vedere Distribuire un ruolo di lavoro [ibrido per runbook di Linux.](automation-linux-hrw-install.md)

## <a name="how-does-it-work"></a>Come funziona?

![Panoramica del ruolo di lavoro ibrido per runbook](media/automation-hybrid-runbook-worker/automation.png)

Ogni utente ruolo di lavoro ibrido per runbook è membro di un gruppo di ruoli di lavoro ibridi per runbook specificato durante l'installazione del ruolo di lavoro. Un gruppo può includere un singolo ruolo di lavoro, ma è possibile includere più ruoli di lavoro in un gruppo per la disponibilità elevata. Ogni computer può ospitare un ruolo di lavoro ibrido per runbook che fa riferimento a un account di Automazione. non è possibile registrare il ruolo di lavoro ibrido tra più account di Automazione. Un ruolo di lavoro ibrido può restare in ascolto solo dei processi da un singolo account di Automazione. Per i computer che ospitano il ruolo di lavoro ibrido per runbook di sistema gestito da Gestione aggiornamenti, possono essere aggiunti a un gruppo di ruoli di lavoro ibridi per runbook. È tuttavia necessario usare lo stesso account di Automazione per l'appartenenza al gruppo Gestione aggiornamenti ruoli di lavoro ibridi per runbook.

Quando si avvia un runbook in un ruolo di lavoro ibrido per runbook dell'utente, si specifica il gruppo in cui viene eseguito. Ogni ruolo di lavoro del gruppo esegue il polling di Automazione di Azure per vedere se sono disponibili processi. Se è disponibile un processo, il primo ruolo di lavoro che raggiunge il processo lo ottiene. Il tempo di elaborazione della coda processi dipende dal profilo hardware e dal carico del ruolo di lavoro ibrido. Non è possibile scegliere un computer di lavoro specifico. Il ruolo di lavoro ibrido si serve di un meccanismo di polling (ogni 30 secondi) e segue un ordine di first-come,first-serve. A seconda di quando è stato inserito un processo, a seconda del ruolo di lavoro ibrido che effettua il ping del servizio di Automazione, lo preleva. Un singolo ruolo di lavoro ibrido può in genere prelevare quattro processi per ogni ping, ovvero ogni 30 secondi. Se la frequenza di push dei processi è superiore a quattro per 30 secondi, è possibile che un altro ruolo di lavoro ibrido nel gruppo di ruoli di lavoro ibridi per runbook abbia prelevato il processo.

Un ruolo di lavoro ibrido per runbook non ha molti dei limiti delle risorse [della sandbox](automation-runbook-execution.md#runbook-execution-environment) di Azure [per](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) lo spazio su disco, la memoria o i socket di rete. I limiti per un ruolo di lavoro ibrido sono correlati solo alle risorse [](automation-runbook-execution.md#fair-share) del ruolo di lavoro e non sono vincolati dal limite di tempo di condivisione equa delle sandbox di Azure.

Per controllare la distribuzione dei runbook nei ruoli di lavoro ibridi per runbook e quando o come vengono attivati i processi, è possibile registrare il ruolo di lavoro ibrido in diversi gruppi di ruoli di lavoro ibridi per runbook all'interno dell'account di Automazione. Impostare come destinazione i processi per il gruppo o i gruppi specifici per supportare la disposizione di esecuzione.

## <a name="hybrid-runbook-worker-installation"></a>Installazione di un ruolo di lavoro ibrido per runbook

Il processo di installazione di un ruolo di lavoro ibrido per runbook utente dipende dal sistema operativo. La tabella seguente definisce i tipi di distribuzione.

|Sistema operativo  |Tipi distribuzione  |
|---------|---------|
|Windows     | [Automatizzata](automation-windows-hrw-install.md#automated-deployment)<br>[Manuale](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Manuale](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Il metodo di installazione consigliato per un computer Windows è l'uso di un runbook Automazione di Azure per automatizzare completamente il processo di configurazione. Se ciò non è possibile, è possibile seguire una procedura dettagliata per installare e configurare manualmente il ruolo. Per i computer Linux si esegue uno script Python per installare l'agente nel computer.

## <a name="network-planning"></a><a name="network-planning"></a>Pianificazione della rete

Controllare [Automazione di Azure configurazione di](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) rete per informazioni dettagliate su porte, URL e altri dettagli di rete necessari per il ruolo di lavoro ibrido per runbook.

### <a name="proxy-server-use"></a>Uso del server proxy

Se si usa un server proxy per la comunicazione tra Automazione di Azure e computer che eseguono l'agente di Log Analytics, assicurarsi che le risorse appropriate siano accessibili. Il timeout per le richieste provenienti dal ruolo di lavoro ibrido per runbook e dai servizi di Automazione è di 30 secondi. Dopo tre tentativi, la richiesta ha esito negativo.

### <a name="firewall-use"></a>Uso del firewall

Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso. Se si usa il gateway di Analisi dei log come proxy, assicurarsi che sia configurato per i ruoli di lavoro ibridi per runbook. Vedere [Configurare il gateway di Log Analytics per i processi di lavoro ibridi per runbook di Automazione](../azure-monitor/agents/gateway.md).

### <a name="service-tags"></a>Tag di servizio

Automazione di Azure supporta i tag del servizio di rete virtuale di Azure, a partire dal tag del servizio [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei gruppi [di sicurezza](../virtual-network/network-security-groups-overview.md#security-rules) di rete [o Firewall di Azure](../firewall/service-tags.md). I tag di servizio possono essere usati al posto di indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag del servizio **GuestAndHybridManagement**  nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio di automazione. Questo tag di servizio non supporta consentire un controllo più granulare limitando gli intervalli IP a un'area specifica.

Il tag di servizio per il Automazione di Azure fornisce solo gli IP usati per gli scenari seguenti:

* Attivare webhook dall'interno della rete virtuale
* Consentire ai processi di lavoro ibridi per runbook State Configuration agenti nella rete virtuale di comunicare con il servizio Automazione

>[!NOTE]
>Il tag di servizio **GuestAndHybridManagement** attualmente non supporta l'esecuzione di processi runbook in una sandbox di Azure, solo direttamente in un ruolo di lavoro ibrido per runbook.

## <a name="support-for-impact-level-5-il5"></a>Supporto per il livello di impatto 5 (IL5)

Automazione di Azure ruolo di lavoro ibrido per runbook può essere usato in Azure per enti pubblici per supportare carichi di lavoro di livello 5 di impatto in una delle due configurazioni seguenti:

* [Macchina virtuale isolata](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Quando vengono distribuiti, utilizzano l'intero host fisico per tale computer, fornendo il livello di isolamento necessario per supportare i carichi di lavoro IL5.

* [Host dedicati di Azure,](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host)che fornisce server fisici in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure.

>[!NOTE]
>L'isolamento del calcolo tramite il ruolo di lavoro ibrido per runbook è disponibile per i cloud commerciali di Azure e us government.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Indirizzi di Gestione aggiornamenti per i ruoli di lavoro ibridi per runbook

Oltre agli indirizzi standard e alle porte necessarie per il ruolo di lavoro ibrido per runbook, Gestione aggiornamenti ha altri requisiti di configurazione di rete descritti nella sezione pianificazione [della](./update-management/overview.md#ports) rete.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>State Configuration di Automazione di Azure in un ruolo di lavoro ibrido per runbook

È possibile eseguire [State Configuration di Automazione di Azure](automation-dsc-overview.md) in un ruolo di lavoro ibrido per runbook. Per gestire la configurazione di server che supportano il ruolo di lavoro ibrido per runbook, è necessario aggiungere i server come nodi DSC. Vedere [Abilitare computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Limiti del ruolo di lavoro per runbook

Il numero massimo di gruppi di ruoli di lavoro ibridi per ogni account di Automazione è 4000 ed è applicabile per entrambi i ruoli di lavoro ibridi & utente. Se si hanno più di 4.000 computer da gestire, è consigliabile creare un altro account di Automazione.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbook in un ruolo di lavoro ibrido per runbook

Potrebbero essere presenti runbook che gestiscono le risorse nel computer locale o vengono eseguiti su risorse nell'ambiente locale in cui viene distribuito un ruolo di lavoro ibrido per runbook utente. In questo caso, è possibile scegliere di eseguire i runbook nel ruolo di lavoro ibrido anziché in un account di Automazione. I runbook eseguiti in un ruolo di lavoro ibrido per runbook hanno una struttura identica a quelli eseguiti nell'account di Automazione. Vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Processi per i ruoli di lavoro ibridi per runbook

Tenere presente che i processi per i ruoli di lavoro ibridi per runbook vengono eseguiti con l'account di **sistema** locale in Windows o con l'[account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Automazione di Azure i processi nei processi ibridi per runbook in modo diverso rispetto ai processi eseguiti nelle sandbox di Azure. Vedere [Ambiente di esecuzione dei runbook](automation-runbook-execution.md#runbook-execution-environment).

In caso di riavvio del computer host con il ruolo di lavoro ibrido per runbook, qualsiasi processo di runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per i runbook del flusso di lavoro di PowerShell. Se un processo di runbook viene riavviato più di tre volte, viene sospeso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorizzazioni runbook per un ruolo di lavoro ibrido per runbook

Poiché accedono a risorse non di Azure, i runbook in esecuzione in un ruolo di lavoro ibrido per runbook dell'utente non possono usare il meccanismo di autenticazione in genere usato dai runbook che eseguono l'autenticazione alle risorse di Azure. Il runbook può fornire la propria autenticazione alle risorse locali o configurare l'autenticazione mediante [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). È anche possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

## <a name="view-system-hybrid-runbook-workers"></a>Visualizzare i worker ibridi per runbook di sistema

Dopo aver abilitato Gestione aggiornamenti funzionalità nei computer Windows o Linux, è possibile eseguire l'inventario dell'elenco dei ruoli di lavoro ibridi per runbook di sistema nel portale di Azure. È possibile visualizzare fino a 2.000 ruoli  di lavoro nel portale  selezionando la scheda Gruppo di ruoli di lavoro ibridi di sistema dall'opzione Gruppo di ruoli di lavoro ibridi nel riquadro a sinistra per l'account di Automazione selezionato.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Pagina Gruppi di ruoli di lavoro ibridi del sistema di account di Automazione" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Se si dispone di più di 2.000 utenti ibridi, per ottenere un elenco di tutti, è possibile eseguire lo script di PowerShell seguente:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#general).
