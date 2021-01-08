---
title: Gestione delle estensioni VM con i server abilitati per Azure Arc
description: I server abilitati per Azure Arc possono gestire la distribuzione delle estensioni delle macchine virtuali che forniscono attività di configurazione e automazione post-distribuzione con macchine virtuali non di Azure.
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: 5430b1c1318747cccfb95f031700fddaad716284
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020622"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestione delle estensioni macchina virtuale con i server con abilitazione di Azure Arc

Le estensioni delle macchine virtuali sono piccole applicazioni che forniscono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione del software, la protezione antivirus o per eseguire uno script al suo interno, è possibile usare un'estensione della macchina virtuale.

I server abilitati per Azure ARC consentono di distribuire le estensioni di VM di Azure in macchine virtuali Windows e Linux non di Azure, semplificando la gestione del computer ibrido durante il ciclo di vita. Le estensioni di macchina virtuale possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti da server abilitati per Arc:

- Il[portale di Azure](manage-vm-extensions-portal.md)
- L'[interfaccia della riga di comando di Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Modelli di gestione risorse](manage-vm-extensions-template.md) di Azure

## <a name="key-benefits"></a>Vantaggi principali

Il supporto delle estensioni VM per i server abilitati per Azure Arc offre i vantaggi principali seguenti:

- Usare la [configurazione dello stato di automazione di Azure](../../automation/automation-dsc-overview.md) per archiviare centralmente le configurazioni e mantenere lo stato desiderato dei computer connessi ibridi abilitati tramite l'estensione VM DSC.

- Raccogliere i dati di log per l'analisi con i [log in monitoraggio di Azure](../../azure-monitor/platform/data-platform-logs.md) abilitati tramite l'estensione della macchina virtuale agente log Analytics. Questa operazione è utile per eseguire analisi complesse tra i dati provenienti da diversi tipi di origini.

- Con [monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md), analizza le prestazioni delle macchine virtuali Windows e Linux e monitora i processi e le dipendenze da altre risorse e processi esterni. Questa operazione viene eseguita tramite l'abilitazione dell'agente Log Analytics e delle estensioni della macchina virtuale dell'agente di dipendenza.

- Scaricare ed eseguire script su computer connessi ibridi usando l'estensione script personalizzata. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o altre attività di configurazione o gestione.

- Aggiorna automaticamente i certificati archiviati in un [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Disponibilità

La funzionalità di estensione della macchina virtuale è disponibile solo nell'elenco delle [aree supportate](overview.md#supported-regions). Assicurarsi di caricare il computer in una di queste aree.

## <a name="extensions"></a>Estensioni

In questa versione sono supportate le estensioni di macchina virtuale seguenti nei computer Windows e Linux.

Per informazioni sul pacchetto dell'agente di computer connesso di Azure e per informazioni dettagliate sul componente agente di estensione, vedere [Panoramica degli agenti](agent-overview.md#agent-component-details).

### <a name="windows-extensions"></a>Estensioni di Windows

|Estensione |Publisher |Tipo |Informazioni aggiuntive |
|----------|----------|-----|-----------------------|
|Scanner di vulnerabilità integrata di Azure Defender |Qualys |WindowsAgent. AzureSecurityCenter |[Soluzione integrata di valutazione della vulnerabilità di Azure Defender per macchine ibride e Azure](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Estensione di script personalizzati |Microsoft.Compute | CustomScriptExtension |[Estensione script personalizzato Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|PowerShell DSC |Microsoft. PowerShell |DSC |[Estensione DSC di Windows PowerShell](../../virtual-machines/extensions/dsc-windows.md)|
|Agente di Log Analytics |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Log Analytics estensione VM per Windows](../../virtual-machines/extensions/oms-windows.md)|
|Monitoraggio di Azure per le macchine virtuali (informazioni dettagliate) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Estensione macchina virtuale di Dependency Agent per Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Sincronizzazione del certificato Azure Key Vault | Microsoft. Azure. Key. Vault |KeyVaultForWindows | [Estensione di macchina virtuale Key Vault per Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agente di Monitoraggio di Azure |Microsoft. Azure. monitor |AzureMonitorWindowsAgent |[Installare l'agente di monitoraggio di Azure (anteprima)](../../azure-monitor/platform/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Estensioni Linux

|Estensione |Publisher |Tipo |Informazioni aggiuntive |
|----------|----------|-----|-----------------------|
|Scanner di vulnerabilità integrata di Azure Defender |Qualys |LinuxAgent.AzureSecurityCenter |[Soluzione integrata di valutazione della vulnerabilità di Azure Defender per macchine ibride e Azure](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Estensione di script personalizzati |Microsoft. Azure. Extensions |CustomScript |[Estensione script personalizzato Linux versione 2](../../virtual-machines/extensions/custom-script-linux.md) |
|PowerShell DSC |Microsoft.OSTCExtensions |DSCForLinux |[Estensione DSC PowerShell per Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agente di Log Analytics |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Log Analytics estensione VM per Linux](../../virtual-machines/extensions/oms-linux.md) |
|Monitoraggio di Azure per le macchine virtuali (informazioni dettagliate) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Estensione macchina virtuale dell'agente di dipendenza per Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Sincronizzazione del certificato Azure Key Vault | Microsoft. Azure. Key. Vault |KeyVaultForLinux | [Estensione macchina virtuale di Key Vault per Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agente di Monitoraggio di Azure |Microsoft. Azure. monitor |AzureMonitorLinuxAgent |[Installare l'agente di monitoraggio di Azure (anteprima)](../../azure-monitor/platform/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità dipende dai provider di risorse di Azure seguenti nella sottoscrizione:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se non sono già registrati, seguire la procedura descritta in [registrare i provider di risorse di Azure](agent-overview.md#register-azure-resource-providers).

Assicurarsi di consultare la documentazione relativa a ogni estensione di macchina virtuale a cui viene fatto riferimento nella tabella precedente per comprendere se sono presenti requisiti di rete o di sistema. Questo può aiutare a evitare di riscontrare problemi di connettività con un servizio o una funzionalità di Azure che si basa su tale estensione della macchina virtuale.

### <a name="log-analytics-vm-extension"></a>Estensione della macchina virtuale Log Analytics

Per l'estensione della macchina virtuale dell'agente Log Analytics per Linux è necessario che Python 2. x sia installato nel computer di destinazione. 

### <a name="azure-key-vault-vm-extension-preview"></a>Estensione della macchina virtuale Azure Key Vault (anteprima)

L'estensione della macchina virtuale Key Vault (anteprima) non supporta i sistemi operativi Linux seguenti:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

La distribuzione dell'estensione della macchina virtuale Key Vault (anteprima) è supportata solo con:

- Interfaccia della riga di comando di Azure
- Azure PowerShell
- Modello di Azure Resource Manager

Prima di distribuire l'estensione, è necessario completare le operazioni seguenti:

1. [Creare un insieme di credenziali e un certificato](../../key-vault/certificates/quick-create-portal.md) (autofirmato o importazione).

2. Concedere al server Azure Arc abilitato l'accesso al segreto del certificato. Se si usa l' [Anteprima RBAC](../../key-vault/general/rbac-guide.md), cercare il nome della risorsa Azure Arc e assegnargli il ruolo **utente Key Vault Secrets (anteprima)** . Se si usano i [criteri di accesso key Vault](../../key-vault/general/assign-access-policy-portal.md), assegnare le autorizzazioni **Get** Secret all'identità assegnata al sistema della risorsa di Azure Arc.

### <a name="connected-machine-agent"></a>Agente Connected Machine

Verificare che il computer corrisponda alle [versioni supportate](agent-overview.md#supported-operating-systems) del sistema operativo Windows e Linux per l'agente del computer connesso di Azure.

La versione minima dell'agente del computer connesso supportato con questa funzionalità in Windows e Linux è la versione 1,0.

Per aggiornare il computer alla versione dell'agente richiesta, vedere [upgrade Agent](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Passaggi successivi

È possibile distribuire, gestire e rimuovere le estensioni di macchina virtuale usando l'interfaccia della riga di comando di [Azure](manage-vm-extensions-cli.md), [Azure PowerShell](manage-vm-extensions-powershell.md), dal [portale di Azure](manage-vm-extensions-portal.md)o dai [modelli di Azure Resource Manager](manage-vm-extensions-template.md).
