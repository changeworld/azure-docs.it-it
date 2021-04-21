---
title: Distribuire un ruolo di lavoro ibrido per runbook di Linux in Automazione di Azure
description: Questo articolo descrive come installare un ruolo di lavoro ibrido per runbook di Automazione di Azure per eseguire runbook in computer basati su Linux nel data center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 04/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24dc0d2b243eb6c13e5670a1438876132c5e429e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833653"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Linux

È possibile usare la funzionalità ruolo di lavoro ibrido per runbook dell'utente di Automazione di Azure per eseguire i runbook direttamente nel computer Azure o non Azure, inclusi i server registrati con i server abilitati per [Azure Arc.](../azure-arc/servers/overview.md) Dal computer o dal server che ospita il ruolo, è possibile eseguire i runbook direttamente su di esso e sulle risorse nell'ambiente per gestire tali risorse locali.

Il ruolo di lavoro ibrido per runbook di Linux esegue i runbook come un utente speciale a cui possono essere garantiti privilegi elevati per eseguire i comandi che richiedono l'elevazione. Automazione di Azure archivia e gestisce i runbook e li recapita a uno o più computer designati. Questo articolo descrive come installare il ruolo di lavoro ibrido per runbook in un computer Linux, come rimuovere il ruolo di lavoro e come rimuovere un gruppo di ruoli di lavoro ibridi per runbook.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di disporre di quanto segue.

### <a name="a-log-analytics-workspace"></a>Un'area di lavoro Log Analytics

Il ruolo di lavoro ibrido per runbook dipende da un'Monitoraggio di Azure di lavoro Log Analytics per installare e configurare il ruolo. È possibile crearlo tramite [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), [tramite PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)o nella [portale di Azure](../azure-monitor/logs/quick-create-workspace.md).

Se non si ha un'area di lavoro Log Analytics Monitoraggio di Azure, vedere le linee guida per la [progettazione Monitoraggio di Azure log](../azure-monitor/logs/design-logs-deployment.md) prima di creare l'area di lavoro.

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Il ruolo di lavoro ibrido per runbook richiede [l'agente di Log Analytics](../azure-monitor/agents/log-analytics-agent.md) per il sistema operativo Linux supportato. Per i server o i computer ospitati all'esterno di Azure, è possibile installare l'agente di Log Analytics [Azure Arc server abilitati.](../azure-arc/servers/overview.md)

>[!NOTE]
>Dopo aver installato l'agente di Log Analytics per Linux, non è necessario modificare le autorizzazioni della `sudoers.d` cartella o la relativa proprietà. L'autorizzazione Sudo è necessaria per **l'account nxautomation,** ovvero il contesto utente in cui viene eseguito il ruolo di lavoro ibrido per runbook. Le autorizzazioni non devono essere rimosse. La limitazione a determinate cartelle o comandi può comportare una modifica di rilievo.
>

### <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati

La funzionalità Ruolo di lavoro ibrido per runbook supporta le distribuzioni seguenti. Si presuppone che tutti i sistemi operativi siano x64. x86 non è supportato per alcun sistema operativo.

* Amazon Linux da 2012.09 a 2015.09
* CentOS Linux 5, 6, 7 e 8
* Oracle Linux 5, 6 e 7
* Red Hat Enterprise Linux Server 5, 6, 7 e 8
* Debian GNU/Linux 6, 7 e 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12 e 15 (SUSE non ha rilasciato versioni numerate 13 o 14)

> [!IMPORTANT]
> Prima di abilitare la Gestione aggiornamenti, che dipende dal ruolo di lavoro ibrido per runbook di sistema, verificare le distribuzioni supportate [qui.](update-management/overview.md#supported-operating-systems)

### <a name="minimum-requirements"></a>Requisiti minimi

I requisiti minimi per un sistema Linux e un ruolo di lavoro ibrido per runbook utente sono:

* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

| **Pacchetto obbligatorio** | **Descrizione** | **Versione minima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Libreria GNU C| 2.5-12 |
|Openssl| Librerie OpenSSL | 1.0 (sono supportati TLS 1.1 e TLS 1.2)|
|Curl | Client Web cURL | 7.15.5|
|Python-ctypes | Sono necessari Python 2.x o Python 3.x |
|PAM | Moduli di autenticazione modulare|
| **Pacchetto facoltativo** | **Descrizione** | **Versione minima**|
| PowerShell Core | Per eseguire runbook di PowerShell, PowerShell Core necessario installare. Vedere [Installazione di PowerShell Core in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) per informazioni su come installarlo. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Aggiunta di un computer a un gruppo di ruoli di lavoro ibrido per runbook

È possibile aggiungere il computer di lavoro a un gruppo di ruoli di lavoro ibrido per runbook in uno degli account di Automazione. Per i computer che ospitano il ruolo di lavoro ibrido per runbook di sistema gestito da Gestione aggiornamenti, possono essere aggiunti a un gruppo di ruoli di lavoro ibrido per runbook. È tuttavia necessario usare lo stesso account di Automazione per l'appartenenza al gruppo Gestione aggiornamenti ruolo di lavoro ibrido per runbook.

>[!NOTE]
>Automazione di Azure [Gestione aggiornamenti](./update-management/overview.md) automaticamente il ruolo di lavoro ibrido per runbook di sistema in un computer Azure o non Azure abilitato per Gestione aggiornamenti. Tuttavia, questo ruolo di lavoro non è registrato con alcun gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione. Per eseguire i runbook in tali computer, è necessario aggiungerli a un gruppo di ruoli di lavoro ibrido per runbook. Seguire il passaggio 4 nella sezione Installare un ruolo di lavoro [ibrido per runbook Linux](#install-a-linux-hybrid-runbook-worker) per aggiungerlo a un gruppo.

## <a name="supported-linux-hardening"></a>Protezione avanzata linux supportata

Gli elementi seguenti non sono ancora supportati:

* Cis

## <a name="supported-runbook-types"></a>Tipi di runbook supportati

I runbook worker ibridi Linux supportano un set limitato di tipi di runbook Automazione di Azure e sono descritti nella tabella seguente.

|Tipo di runbook | Supportato |
|-------------|-----------|
|Python 3 (anteprima)|Sì, obbligatorio solo per queste distribuzione: SUSE LES 15, RHEL 8 e CentOS 8|
|Python 2 |Sì, per qualsiasi distribuzione che non richiede Python 3<sup>1</sup> |
|PowerShell |Sì<sup>2</sup> |
|Flusso di lavoro PowerShell |No |
|Grafico |No |
|Grafico del flusso di lavoro di PowerShell |No |

<sup>1</sup> Vedere [Sistemi operativi Linux supportati.](#supported-linux-operating-systems)

<sup>2</sup> I runbook di PowerShell PowerShell Core devono essere installati nel computer Linux. Vedere [Installazione di PowerShell Core in Linux](/powershell/scripting/install/installing-powershell-core-on-linux) per informazioni su come installarlo.

### <a name="network-configuration"></a>Configurazione di rete

Per i requisiti di rete per il ruolo di lavoro ibrido per runbook, [vedere Configurazione della rete.](automation-hybrid-runbook-worker.md#network-planning)

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installare un ruolo di lavoro ibrido per runbook di Linux

Esistono due metodi per distribuire un ruolo di lavoro ibrido per runbook. È possibile importare ed eseguire un runbook dalla raccolta di runbook nel portale di Azure oppure eseguire manualmente una serie di comandi di PowerShell per eseguire la stessa attività.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importazione di un runbook dalla raccolta di runbook

La procedura di importazione è descritta in dettaglio in [Importare runbook](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)da GitHub con il portale di Azure . Il nome del runbook da importare è **Create Automation Linux HybridWorker**.

Il runbook usa i parametri seguenti.

| Parametro | Stato | Descrizione |
| ------- | ----- | ----------- |
| `Location` | Obbligatorio | Percorso dell'area di lavoro Log Analytics. |
| `ResourceGroupName` | Obbligatorio | Gruppo di risorse per l'account di Automazione. |
| `AccountName` | Obbligatorio | Nome dell'account di Automazione in cui verrà registrato il ruolo di lavoro ibrido per l'esecuzione. |
| `CreateLA` | Obbligatorio | Se true, usa il valore di per `WorkspaceName` creare un'area di lavoro Log Analytics. Se false, il valore di deve `WorkspaceName` fare riferimento a un'area di lavoro esistente. |
| `LAlocation` | Facoltativo | Percorso in cui verrà creata l'area di lavoro Log Analytics o in cui esiste già. |
| `WorkspaceName` | Facoltativo | Nome dell'area di lavoro Log Analytics da creare o usare. |
| `CreateVM` | Obbligatorio | Se true, usare il valore `VMName` di come nome di una nuova macchina virtuale. Se false, usare `VMName` per trovare e registrare una macchina virtuale esistente. |
| `VMName` | Facoltativo | Nome della macchina virtuale creata o registrata, a seconda del valore di `CreateVM` . |
| `VMImage` | Facoltativo | Nome dell'immagine della macchina virtuale da creare. |
| `VMlocation` | Facoltativo | Percorso della macchina virtuale creata o registrata. Se questa posizione non viene specificata, viene usato il `LAlocation` valore di . |
| `RegisterHW` | Obbligatorio | Se true, registrare la macchina virtuale come ruolo di lavoro ibrido. |
| `WorkerGroupName` | Obbligatorio | Nome del gruppo di lavoro ibrido. |

### <a name="manually-run-powershell-commands"></a>Eseguire manualmente i comandi di PowerShell

Per installare e configurare un ruolo di lavoro ibrido per runbook Linux, seguire questa procedura.

1. Abilitare la soluzione Automazione di Azure nell'area di lavoro Log Analytics eseguendo il comando seguente in un prompt dei comandi di PowerShell con privilegi elevati o Cloud Shell nel [portale di Azure](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Distribuire l'agente di Log Analytics nel computer di destinazione.

    * Per le macchine virtuali di Azure, installare l'agente di Log Analytics per Linux usando [l'estensione macchina virtuale per Linux.](../virtual-machines/extensions/oms-linux.md) L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente. È possibile usare un modello Azure Resource Manager, l'interfaccia della riga di comando di Azure o Criteri di Azure per assegnare il criterio predefinito Distribuisci agente di Log Analytics per macchine virtuali Linux o [ *Windows.*](../governance/policy/samples/built-in-policies.md#monitoring) Dopo aver installato l'agente, il computer può essere aggiunto a un gruppo di ruoli di lavoro ibrido per runbook nell'account di Automazione.

    * Per i computer non Azure, è possibile installare l'agente di Log Analytics [usando Azure Arc server abilitati.](../azure-arc/servers/overview.md) I server abilitati per Arc supportano la distribuzione dell'agente di Log Analytics usando i metodi seguenti:

        - Uso del framework delle estensioni di macchina virtuale.

            Questa funzionalità nei Azure Arc abilitati consente di distribuire l'estensione macchina virtuale dell'agente di Log Analytics in un server Windows e/o Linux non Azure. Le estensioni vm possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti dai server abilitati per Arc:

            - Il[portale di Azure](../azure-arc/servers/manage-vm-extensions-portal.md)
            - L'[interfaccia della riga di comando di Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Modelli [Resource Manager Azure](../azure-arc/servers/manage-vm-extensions-template.md)

        - Uso di Criteri di Azure.

            Con questo approccio si usa il criterio predefinito distribuisci l'agente di Log Analytics Criteri di Azure in computer Linux o [Windows Azure Arc](../governance/policy/samples/built-in-policies.md#monitoring) per controllare se nel server abilitato per Arc è installato l'agente di Log Analytics. Se l'agente non è installato, lo distribuisce automaticamente usando un'attività di correzione. In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [Abilita](../governance/policy/samples/built-in-initiatives.md#monitoring) Monitoraggio di Azure per le macchine virtuali per installare e configurare l'agente di Log Analytics.

        È consigliabile installare l'agente di Log Analytics per Windows o Linux usando Criteri di Azure.

    > [!NOTE]
    > Per gestire la configurazione dei computer che supportano il ruolo di lavoro ibrido per runbook con Desired State Configuration (DSC), è necessario aggiungere i computer come nodi DSC.

    > [!NOTE]
    > L'[account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) deve essere presente con le autorizzazioni sudo corrispondenti durante l'installazione di un ruolo di lavoro ibrido per runbook di Linux. Se si prova a installare il ruolo di lavoro e l'account non è presente o non ha le autorizzazioni appropriate, l'installazione non riesce.

3. Verificare che l'agente sta segnalando all'area di lavoro.

    L'agente di Log Analytics per Linux connette i computer a un'area Monitoraggio di Azure di lavoro Log Analytics. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per runbook.

    Quando l'agente si è connesso correttamente all'area di lavoro Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii dati di heartbeat all'area di lavoro.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nei risultati della ricerca dovrebbero essere visualizzati i record di heartbeat per il computer, che indicano che è connesso e segnala al servizio. Per impostazione predefinita, ogni agente trasmette un record di heartbeat all'area di lavoro assegnata.

4. Eseguire il comando seguente per aggiungere il computer a un gruppo di ruoli di lavoro ibridi per runbook, specificando i valori per i parametri `-w` `-k` , , e `-g` `-e` .

    È possibile ottenere le informazioni necessarie per i parametri `-k` e `-e` dalla **pagina** Chiavi nell'account di Automazione. Selezionare **Chiavi** nella **sezione Impostazioni** account sul lato sinistro della pagina.

    ![Pagina Gestisci chiavi](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Per il `-e` parametro , copiare il valore per **l'URL**.

    * Per il `-k` parametro , copiare il valore di **PRIMARY ACCESS KEY**.

    * Per il parametro , specificare il nome del gruppo di ruoli di lavoro ibridi per runbook a cui aggiungere il nuovo ruolo di lavoro ibrido `-g` per runbook di Linux. Se questo gruppo esiste già nell'account di Automazione, viene aggiunto il computer corrente. Se questo gruppo non esiste, viene creato con tale nome.

    * Per il parametro `-w` specificare l'ID dell'area di lavoro Log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Verificare la distribuzione dopo il completamento dello script. Nella pagina Gruppi di ruoli di lavoro ibridi per **runbook** nell'account di Automazione, nella scheda Gruppo ruoli di lavoro ibridi per **runbook** utente, vengono visualizzati il gruppo nuovo o esistente e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco nella pagina, scegliere Ruoli di lavoro ibridi dal menu a **sinistra.** Nella pagina **Ruoli di lavoro** ibridi è possibile visualizzare ogni membro del gruppo elencato.

    > [!NOTE]
    > Se si usa l'estensione macchina virtuale Log Analytics per Linux per una macchina virtuale di Azure, è consigliabile impostare su perché l'aggiornamento automatico delle versioni può causare problemi con il ruolo di lavoro ibrido `autoUpgradeMinorVersion` `false` per runbook. Per informazioni su come aggiornare manualmente l'estensione, vedere [Distribuzione dell'interfaccia della riga di comando di Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Disattivare la convalida della firma

Per impostazione predefinita, i ruoli di lavoro ibridi per runbook di Linux richiedono la convalida della firma. Se si esegue un runbook senza firma in un ruolo di lavoro, viene visualizzato un errore `Signature validation failed`. Per disattivare la convalida della firma, eseguire il comando seguente. Sostituire il secondo parametro con l'ID dell'area di lavoro Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Rimuovere il ruolo di lavoro ibrido per runbook

È possibile usare il comando `ls /var/opt/microsoft/omsagent` nel ruolo di lavoro ibrido per runbook per ottenere l'ID dell'area di lavoro. Viene creata una cartella denominata con l'ID dell'area di lavoro.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Questo script non rimuove l'agente di Log Analytics per Linux dal computer. Rimuove solo la funzionalità e la configurazione del ruolo di lavoro ibrido per runbook.

## <a name="remove-a-hybrid-worker-group"></a>Rimuovere un gruppo di ruoli di lavoro ibridi

Per rimuovere un gruppo di ruoli di lavoro ibrido per runbook di computer Linux, usare gli stessi passaggi di un gruppo di lavoro ibrido di Windows. Vedere [Rimuovere un gruppo di ruoli di lavoro ibridi](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

* Per informazioni su come risolvere i problemi dei ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook - Linux](troubleshoot/hybrid-runbook-worker.md#linux).
