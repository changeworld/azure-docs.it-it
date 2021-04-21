---
title: Distribuire un ruolo di lavoro ibrido per runbook Windows in Automazione di Azure
description: Questo articolo descrive come distribuire un ruolo di lavoro ibrido per runbook che è possibile usare per eseguire runbook in computer basati su Windows nel data center locale o nell'ambiente cloud.
services: automation
ms.subservice: process-automation
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4bf27ffc888e189f15e1c435309cbeddb1c11fec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830341"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Distribuire un ruolo di lavoro ibrido per runbook di Windows

È possibile usare la funzionalità ruolo di lavoro ibrido per runbook dell'utente di Automazione di Azure per eseguire i runbook direttamente in un computer Azure o non Azure, inclusi i server registrati con i server abilitati per [Azure Arc.](../azure-arc/servers/overview.md) Dal computer o dal server che ospita il ruolo, è possibile eseguire i runbook direttamente su di esso e sulle risorse nell'ambiente per gestire tali risorse locali.

Automazione di Azure archivia e gestisce i runbook e li recapita a uno o più computer designati. Questo articolo descrive come distribuire un ruolo di lavoro ibrido per runbook in un computer Windows, come rimuovere il ruolo di lavoro e come rimuovere un gruppo di ruoli di lavoro ibridi per runbook.

Dopo avere distribuito correttamente un ruolo di lavoro per runbook, esaminare [Esecuzione dei runbook per Hybrid Runbook Worker](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel data center locale o un altro ambiente cloud.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di disporre di quanto segue.

### <a name="a-log-analytics-workspace"></a>Un'area di lavoro Log Analytics

Il ruolo di lavoro ibrido per runbook dipende da un'Monitoraggio di Azure di lavoro Log Analytics per installare e configurare il ruolo. È possibile crearlo tramite [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), [tramite PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)o nella [portale di Azure](../azure-monitor/logs/quick-create-workspace.md).

Se non si ha un'area di lavoro Log Analytics Monitoraggio di Azure, vedere le linee guida per la [progettazione Monitoraggio di Azure log](../azure-monitor/logs/design-logs-deployment.md) prima di creare l'area di lavoro.

### <a name="log-analytics-agent"></a>Agente di Log Analytics

Il ruolo di lavoro ibrido per runbook richiede [l'agente di Log Analytics](../azure-monitor/agents/log-analytics-agent.md) per il sistema operativo Windows supportato. Per i server o i computer ospitati all'esterno di Azure, è possibile installare l'agente di Log Analytics [Azure Arc server abilitati.](../azure-arc/servers/overview.md)

### <a name="supported-windows-operating-system"></a>Sistema operativo Windows supportato

La funzionalità Ruolo di lavoro ibrido per runbook supporta i sistemi operativi seguenti:

* Windows Server 2019 (incluso Server Core)
* Windows Server 2016, versione 1709 e 1803 (escluso Server Core)
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 Enterprise (inclusa la versione multisessione) e Pro
* Windows 8 Enterprise e Pro
* Windows 7 SP1

### <a name="minimum-requirements"></a>Requisiti minimi

I requisiti minimi per un sistema Windows e un ruolo di lavoro ibrido per runbook utente sono:

* Windows PowerShell 5.1[(scaricare WMF 5.1).](https://www.microsoft.com/download/details.aspx?id=54616) PowerShell Core non è supportato.
* .NET Framework 4.6.2 o versioni successive
* Due core
* 4 GB di RAM
* Porta 443 (in uscita)

### <a name="network-configuration"></a>Configurazione di rete

Per i requisiti di rete per il ruolo di lavoro ibrido per runbook, [vedere Configurazione della rete.](automation-hybrid-runbook-worker.md#network-planning)

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Aggiunta di un computer a un gruppo di ruoli di lavoro ibridi per runbook

È possibile aggiungere il computer di lavoro a un gruppo di ruoli di lavoro ibridi per runbook in uno degli account di Automazione. Per i computer che ospitano il ruolo di lavoro ibrido per runbook di sistema gestito da Gestione aggiornamenti, possono essere aggiunti a un gruppo di ruoli di lavoro ibridi per runbook. È tuttavia necessario usare lo stesso account di Automazione per l'appartenenza al gruppo Gestione aggiornamenti ruoli di lavoro ibridi per runbook.

>[!NOTE]
>Automazione di Azure [Gestione aggiornamenti](./update-management/overview.md) installa automaticamente il ruolo di lavoro ibrido per runbook di sistema in un computer Azure o non Azure abilitato per Gestione aggiornamenti. Tuttavia, questo ruolo di lavoro non è registrato con alcun gruppo di ruoli di lavoro ibridi per runbook nell'account di Automazione. Per eseguire i runbook in tali computer, è necessario aggiungerli a un gruppo di ruoli di lavoro ibridi per runbook. Seguire il passaggio 6 nella sezione [Distribuzione manuale](#manual-deployment) per aggiungerlo a un gruppo.

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>Abilitare per la gestione con Automazione di Azure State Configuration

Per informazioni sull'abilitazione dei computer per la gestione con Automazione di Azure State Configuration, vedere Abilitare i computer [per la gestione Automazione di Azure State Configuration](automation-dsc-onboarding.md).

> [!NOTE]
> Per gestire la configurazione dei computer che supportano il ruolo di lavoro ibrido per runbook con Desired State Configuration (DSC), è necessario aggiungere i computer come nodi DSC.

## <a name="installation-options"></a>Opzione di installazione

Per installare e configurare un ruolo di lavoro ibrido per runbook utente di Windows, è possibile usare uno dei metodi seguenti.

* Usare uno script di PowerShell fornito per [automatizzare completamente](#automated-deployment) il processo di configurazione di uno o più computer Windows. Questo è il metodo consigliato per i computer nel data center o in un altro ambiente cloud.
* Importare manualmente la soluzione Automazione, installare l'agente di Log Analytics per Windows e configurare il ruolo di lavoro nel computer.

## <a name="automated-deployment"></a>Distribuzione automatizzata

Esistono due metodi per distribuire automaticamente un ruolo di lavoro ibrido per runbook. È possibile importare un runbook dalla raccolta di runbook nel portale di Azure ed eseguirlo oppure è possibile scaricare manualmente uno script dal PowerShell Gallery.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Importazione di un runbook dalla raccolta di runbook

La procedura di importazione è descritta in dettaglio in [Importare runbook](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)da GitHub con il portale di Azure . Il nome del runbook da importare è **Create Automation Windows HybridWorker**.

Il runbook usa i parametri seguenti.

| Parametro | Stato | Descrizione |
| ------- | ----- | ----------- |
| `Location` | Obbligatorio | Percorso dell'area di lavoro Log Analytics. |
| `ResourceGroupName` | Obbligatorio | Gruppo di risorse per l'account di Automazione. |
| `AccountName` | Obbligatorio | Nome dell'account di Automazione in cui verrà registrato il ruolo di lavoro con esecuzione ibrida. |
| `CreateLA` | Obbligatorio | Se true, usa il valore di per `WorkspaceName` creare un'area di lavoro Log Analytics. Se false, il valore di deve `WorkspaceName` fare riferimento a un'area di lavoro esistente. |
| `LAlocation` | Facoltativo | Percorso in cui verrà creata l'area di lavoro Log Analytics o in cui esiste già. |
| `WorkspaceName` | Facoltativo | Nome dell'area di lavoro Log Analytics da usare. |
| `CreateVM` | Obbligatorio | Se true, usare il valore `VMName` di come nome di una nuova macchina virtuale. Se false, usare `VMName` per trovare e registrare una macchina virtuale esistente. |
| `VMName` | Facoltativo | Nome della macchina virtuale creata o registrata, a seconda del valore di `CreateVM` . |
| `VMImage` | Facoltativo | Nome dell'immagine della macchina virtuale da creare. |
| `VMlocation` | Facoltativo | Percorso della macchina virtuale creata o registrata. Se questa posizione non viene specificata, viene usato il `LAlocation` valore di . |
| `RegisterHW` | Obbligatorio | Se true, registrare la macchina virtuale come ruolo di lavoro ibrido. |
| `WorkerGroupName` | Obbligatorio | Nome del gruppo di lavoro ibrido. |

### <a name="download-a-script-from-the-powershell-gallery"></a>Scaricare uno script dal PowerShell Gallery

Questo metodo di distribuzione automatizzata usa lo **script** di PowerShellNew-OnPremiseHybridWorker.ps1per automatizzare e configurare il ruolo di lavoro ibrido per runbook di Windows. Esegue le operazioni seguenti:

* Installa i moduli necessari
* Accede con l'account Azure
* Verifica l'esistenza del gruppo di risorse e dell'account di Automazione specificati
* Crea riferimenti agli attributi dell'account di Automazione
* Crea un'Monitoraggio di Azure di lavoro Log Analytics se non specificata
* Abilitare la soluzione Automazione di Azure nell'area di lavoro
* Scaricare e installare l'agente di Log Analytics per Windows
* Registrare il computer come ruolo di lavoro ibrido per runbook

Eseguire la procedura seguente per installare il ruolo nel computer Windows usando lo script .

1. Scaricare lo script **New-OnPremiseHybridWorker.ps1** da [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Dopo aver scaricato lo script, copiarlo o eseguirlo nel computer di destinazione. Lo script usa i parametri seguenti.

    | Parametro | Stato | Descrizione |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | Obbligatorio | nome del gruppo di risorse associato all'account di Automazione. |
    | `AutomationAccountName` | Obbligatorio | nome dell'account di Automazione.
    | `Credential` | Facoltativo | Credenziali da usare per l'accesso all'ambiente di Azure. |
    | `HybridGroupName` | Obbligatorio | nome di un gruppo di ruoli di lavoro ibridi per runbook specificato come destinazione per i runbook che supportano questo scenario. |
    | `OMSResourceGroupName` | Facoltativo | nome del gruppo di risorse per l'area di lavoro Log Analytics. Se questo gruppo di risorse non è specificato, viene usato il valore di `AAResourceGroupName`. |
    | `SubscriptionID` | Obbligatorio | Identificatore della sottoscrizione di Azure associata all'account di automazione. |
    | `TenantID` | Facoltativo | Identificatore dell'organizzazione tenant associato all'account di automazione. |
    | `WorkspaceName` | Facoltativo | Nome dell'area di lavoro Log Analytics. Se non si dispone di un'area di lavoro Log Analytics, lo script ne crea e configura una. |

1. Aprire un prompt dei comandi di PowerShell con privilegi elevati a 64 bit.

1. Dal prompt dei comandi di PowerShell passare alla cartella che contiene lo script scaricato. Modificare i valori per i parametri `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` e `WorkspaceName`. Quindi, eseguire lo script.

    Verrà chiesto di eseguire l'autenticazione con Azure dopo aver eseguito lo script. È necessario accedere con un account membro del ruolo **Amministratori** della sottoscrizione e co-amministratore della sottoscrizione.

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

1. Verrà chiesto di accettare l'installazione di NuGet e di eseguire l'autenticazione con le credenziali di Azure. Se non si ha la versione più recente di NuGet, è possibile scaricarla da [Versioni di distribuzione NuGet disponibili.](https://www.nuget.org/downloads)

1. Verificare la distribuzione al termine dello script. Nella pagina Gruppi di ruoli di lavoro ibridi per **runbook** nell'account di Automazione, nella scheda gruppo Ruoli di lavoro ibridi per **runbook** utente, vengono visualizzati il nuovo gruppo e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco nella pagina. Nel menu a sinistra scegliere Ruoli di lavoro **ibridi.** Nella pagina **Ruoli di lavoro** ibridi è possibile visualizzare ogni membro del gruppo elencato.

## <a name="manual-deployment"></a>Distribuzione manuale

Per installare e configurare un ruolo di lavoro ibrido per runbook di Windows, seguire questa procedura.

1. Abilitare la soluzione Automazione di Azure nell'area di lavoro Log Analytics eseguendo il comando seguente in un prompt dei comandi di PowerShell con privilegi elevati o Cloud Shell nella portale di Azure [.](https://portal.azure.com)

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. Distribuire l'agente di Log Analytics nel computer di destinazione.

    * Per le macchine virtuali di Azure, installare l'agente di Log Analytics per Windows usando [l'estensione macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md). L'estensione installa l'agente di Log Analytics in macchine virtuali di Azure e registra le macchine virtuali in un'area di lavoro Log Analytics esistente. È possibile usare un modello Azure Resource Manager, PowerShell o Criteri di Azure per assegnare il criterio predefinito Distribuisci agente di Log Analytics per macchine virtuali Linux o [ *Windows.*](../governance/policy/samples/built-in-policies.md#monitoring) Dopo aver installato l'agente, il computer può essere aggiunto a un gruppo di ruoli di lavoro ibrido per runbook nell'account di Automazione.
    
    * Per i computer non Azure, è possibile installare l'agente di Log Analytics [usando Azure Arc server abilitati.](../azure-arc/servers/overview.md) I server abilitati per Arc supportano la distribuzione dell'agente di Log Analytics usando i metodi seguenti:
    
        - Uso del framework delle estensioni di macchina virtuale.
        
            Questa funzionalità nei Azure Arc abilitati consente di distribuire l'estensione macchina virtuale dell'agente di Log Analytics in un server Windows e/o Linux non Azure. Le estensioni vm possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti dai server abilitati per Arc:
        
            - Il[portale di Azure](../azure-arc/servers/manage-vm-extensions-portal.md)
            - L'[interfaccia della riga di comando di Azure](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Modelli [Resource Manager Azure](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - Uso di Criteri di Azure.
        
            Con questo approccio si usa il criterio predefinito Criteri di Azure Deploy Log Analytics agent to Linux or Windows Azure Arc machines to audit if the Arc enabled server has the Log Analytics agent installed (Distribuisci agente di Log Analytics in computer Linux o [Windows Azure Arc)](../governance/policy/samples/built-in-policies.md#monitoring) per controllare se nel server abilitato per Arc è installato l'agente di Log Analytics. Se l'agente non è installato, lo distribuisce automaticamente usando un'attività di correzione. In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece [l'iniziativa Abilita](../governance/policy/samples/built-in-initiatives.md#monitoring) Monitoraggio di Azure per le macchine virtuali per installare e configurare l'agente di Log Analytics.

    È consigliabile installare l'agente di Log Analytics per Windows o Linux usando Criteri di Azure.

1. Verificare che l'agente sta segnalando all'area di lavoro

    L'agente di Log Analytics per Windows connette i computer a un'area Monitoraggio di Azure Log Analytics. Quando si installa l'agente nel computer e lo si connette all'area di lavoro, vengono scaricati automaticamente i componenti necessari per il ruolo di lavoro ibrido per runbook.

    Quando l'agente si è connesso correttamente all'area di lavoro Log Analytics dopo alcuni minuti, è possibile eseguire la query seguente per verificare che invii dati di heartbeat all'area di lavoro.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nei risultati della ricerca dovrebbero essere visualizzati i record heartbeat per il computer, a indicare che è connesso e che viene segnalato al servizio. Per impostazione predefinita, ogni agente trasmette un record di heartbeat all'area di lavoro assegnata. Per completare l'installazione e la configurazione dell'agente, eseguire queste operazioni.

1. Verificare la versione del ruolo di lavoro ibrido per runbook nel computer che ospita l'agente di Log Analytics, individuare `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` e prendere nota della **sottocartella della** versione. Questa cartella verrà visualizzata nel computer alcuni minuti dopo che la soluzione è stata abilitata nell'area di lavoro.

1. Installare l'ambiente runbook e connettersi a Automazione di Azure. Quando si configura un agente per l'invio  di report a un'area di lavoro Log Analytics e si importa la soluzione automazione, la soluzione esegue il push del `HybridRegistration` modulo PowerShell. Questo modulo contiene il `Add-HybridRunbookWorker` cmdlet . Usare questo cmdlet per installare l'ambiente runbook nel computer e registrarlo con Automazione di Azure.

    Aprire una sessione di PowerShell in modalità amministratore ed eseguire i comandi seguenti per importare il modulo.

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. Eseguire il `Add-HybridRunbookWorker` cmdlet specificando i valori per i parametri , e `Url` `Key` `GroupName` .

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    È possibile ottenere le informazioni necessarie per i parametri `Url` e dalla pagina Chiavi `Key` nell'account di Automazione.  Selezionare **Chiavi** nella **sezione Impostazioni** account sul lato sinistro della pagina.

    ![Pagina Gestisci chiavi](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Per il `Url` parametro , copiare il valore per **l'URL**.

    * Per il `Key` parametro , copiare il valore di **PRIMARY ACCESS KEY**.

    * Per il parametro `GroupName`, usare il nome del gruppo di ruoli di lavoro ibridi per runbook. Se questo gruppo esiste già nell'account di Automazione, viene aggiunto il computer corrente. Se questo gruppo non esiste, verrà aggiunto.

    * Se necessario, impostare il parametro `Verbose` per ricevere i dettagli sull'installazione.

1. Verificare la distribuzione dopo il completamento del comando. Nella pagina Gruppi di ruoli di lavoro ibridi per **runbook** nell'account di Automazione, nella scheda gruppo Ruoli di lavoro ibridi per **runbook** utente, vengono visualizzati il gruppo nuovo o esistente e il numero di membri. Se si tratta di un gruppo esistente, il numero di membri viene incrementato. È possibile selezionare il gruppo dall'elenco nella pagina. Nel menu a sinistra scegliere Ruoli di lavoro **ibridi.** Nella pagina **Ruoli di lavoro** ibridi è possibile visualizzare ogni membro del gruppo elencato.

## <a name="install-powershell-modules"></a>Installare i moduli di PowerShell

I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Poiché questi moduli non vengono distribuiti automaticamente nei computer locali, è necessario installarli manualmente. Il modulo di Azure è l'eccezione. Tale modulo viene installato per impostazione predefinita e fornisce l'accesso ai cmdlet per tutti i servizi e le attività di Azure per Automazione di Azure.

Poiché lo scopo principale del ruolo di lavoro ibrido per runbook è quello di gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano queste risorse, particolarmente il modulo `PowerShellGet`. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

I moduli installati devono trovarsi in un percorso a cui fa riferimento la variabile di ambiente `PSModulePath`, in modo che il ruolo di lavoro ibrido possa importarli automaticamente. Per altre informazioni, vedere [Installare moduli in PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module).

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>Rimuovere il ruolo di lavoro ibrido per runbook

1. Nel portale di Azure passare all'account di Automazione.

1. In **Impostazioni Account** selezionare **Chiavi** e prendere nota dei valori di **URL** e **Chiave di accesso primaria**.

1. Aprire una sessione di PowerShell in modalità amministratore ed eseguire questo comando con i valori dell'URL e della chiave di accesso primaria. Usare il parametro `Verbose` per un log dettagliato del processo di rimozione. Per rimuovere le macchine non aggiornate dal gruppo di ruoli di lavoro ibridi, usare il parametro facoltativo `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Rimuovere un gruppo di ruoli di lavoro ibridi

Per rimuovere un gruppo di ruoli di lavoro ibridi per runbook, è prima necessario rimuovere il ruolo di lavoro ibrido per runbook da ogni computer membro del gruppo. Eseguire quindi queste operazioni per rimuovere il gruppo:

1. Nel portale di Azure aprire l'account di automazione.

1. Selezionare **Gruppi di ruolo di lavoro ibridi** in **Automazione processi**. Selezionare il gruppo che si vuole eliminare. Viene visualizzata la pagina delle proprietà per quel gruppo.

   ![Pagina Proprietà](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. Nella pagina delle proprietà del gruppo selezionato selezionare **Elimina**. Compare un messaggio che chiede di confermare. Selezionare **Sì** se si è certi di voler continuare.

   ![Messaggio di conferma](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#general).
