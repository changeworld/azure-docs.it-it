---
title: Gestione delle estensioni delle macchine virtuali con Azure Arc server abilitati
description: Azure Arc server abilitati possono gestire la distribuzione di estensioni di macchine virtuali che forniscono attività di automazione e configurazione post-distribuzione con macchine virtuali non di Azure.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 67f1b5b3db6ef446342e8381d54d487af1f3426a
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389792"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Gestione delle estensioni macchina virtuale con i server con abilitazione di Azure Arc

Le estensioni delle macchine virtuali sono piccole applicazioni che forniscono attività di configurazione e automazione post-distribuzione nelle macchine virtuali di Azure. Ad esempio, se una macchina virtuale richiede l'installazione del software, la protezione antivirus o l'esecuzione di uno script, è possibile usare un'estensione della macchina virtuale.

Azure Arc server abilitati consentono di distribuire estensioni di macchine virtuali di Azure in macchine virtuali Windows e Linux non di Azure, semplificando la gestione della macchina ibrida attraverso il ciclo di vita. Le estensioni vm possono essere gestite usando i metodi seguenti nei computer ibridi o nei server gestiti dai server abilitati per Arc:

- Il[portale di Azure](manage-vm-extensions-portal.md)
- L'[interfaccia della riga di comando di Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Modelli [Resource Manager Azure](manage-vm-extensions-template.md)

> [!NOTE]
> Azure Arc server abilitati non supporta la distribuzione e la gestione delle estensioni vm nelle macchine virtuali di Azure. Per le macchine virtuali di Azure, vedere l'articolo panoramica [dell'estensione vm](../../virtual-machines/extensions/overview.md) seguente.

## <a name="key-benefits"></a>Vantaggi principali

Azure Arc server abilitati per il supporto dell'estensione vm offre i vantaggi principali seguenti:

- Raccogliere i dati di log per l'analisi con [i log Monitoraggio di Azure](../../azure-monitor/logs/data-platform-logs.md) abilitando l'estensione macchina virtuale dell'agente di Log Analytics. Ciò è utile per eseguire analisi complesse tra dati provenienti da diversi tipi di origini.

- Con [Monitoraggio di Azure per le macchine virtuali](../../azure-monitor/vm/vminsights-overview.md), analizza le prestazioni delle macchine virtuali Windows e Linux e monitora i processi e le dipendenze da altre risorse e processi esterni. Questa operazione viene ottenuta abilitando sia l'agente di Log Analytics che le estensioni della macchina virtuale dell'agente di dipendenza.

- Scaricare ed eseguire script in computer connessi ibridi usando l'estensione script personalizzato. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o altre attività di configurazione o gestione.

- Aggiornamento automatico dei certificati archiviati in un [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="availability"></a>Disponibilità

La funzionalità di estensione della macchina virtuale è disponibile solo nell'elenco [delle aree supportate.](overview.md#supported-regions) Assicurarsi di eseguire l'onboard del computer in una di queste aree.

## <a name="extensions"></a>Estensioni

In questa versione sono supportate le estensioni vm seguenti nei computer Windows e Linux.

Per informazioni sul pacchetto Azure Connected Machine agente di estensione e informazioni dettagliate sul componente dell'agente di estensione, vedere [Panoramica dell'agente](agent-overview.md#agent-component-details).

> [!NOTE]
> Di recente è stato rimosso il supporto per l'estensione macchina virtuale DSC per i server abilitati per Arc. In alternativa, è consigliabile usare l'estensione script personalizzato per gestire la configurazione post-distribuzione del server o del computer.

### <a name="windows-extensions"></a>Estensioni di Windows

|Estensione |Publisher |Tipo |Informazioni aggiuntive |
|----------|----------|-----|-----------------------|
|Azure Defender scanner di vulnerabilità integrato |Qualys |WindowsAgent.AzureSecurityCenter |[Azure Defender soluzione integrata di valutazione delle vulnerabilità per Azure e i computer ibridi](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Estensione di script personalizzati |Microsoft.Compute | CustomScriptExtension |[Estensione script personalizzati di Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|Agente di Log Analytics |Microsoft.EnterpriseCloud.Monitoring |MicrosoftMonitoringAgent |[Estensione vm di Log Analytics per Windows](../../virtual-machines/extensions/oms-windows.md)|
|Monitoraggio di Azure per le macchine virtuali (informazioni dettagliate) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentWindows | [Estensione macchina virtuale dependency agent per Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|Azure Key Vault sincronizzazione dei certificati | Microsoft.Azure.Key.Vault |KeyVaultForWindows | [Estensione di macchina virtuale Key Vault per Windows](../../virtual-machines/extensions/key-vault-windows.md) |
|Agente di Monitoraggio di Azure |Microsoft.Azure.Monitor |AzureMonitorWindowsAgent |[Installare l'Monitoraggio di Azure (anteprima)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

### <a name="linux-extensions"></a>Estensioni Linux

|Estensione |Publisher |Tipo |Informazioni aggiuntive |
|----------|----------|-----|-----------------------|
|Azure Defender scanner di vulnerabilità integrato |Qualys |LinuxAgent.AzureSecurityCenter |[Azure Defender soluzione integrata di valutazione delle vulnerabilità per Azure e i computer ibridi](../../security-center/deploy-vulnerability-assessment-vm.md)|
|Estensione di script personalizzati |Microsoft.Azure.Extensions |CustomScript |[Estensione per script personalizzati Linux versione 2](../../virtual-machines/extensions/custom-script-linux.md) |
|Agente di Log Analytics |Microsoft.EnterpriseCloud.Monitoring |OmsAgentForLinux |[Estensione macchina virtuale log analytics per Linux](../../virtual-machines/extensions/oms-linux.md) |
|Monitoraggio di Azure per le macchine virtuali (informazioni dettagliate) |Microsoft.Azure.Monitoring.DependencyAgent |DependencyAgentLinux |[Estensione macchina virtuale Dependency Agent per Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |
|Azure Key Vault Sincronizzazione certificati | Microsoft.Azure.Key.Vault |KeyVaultForLinux | [Estensione macchina virtuale di Key Vault per Linux](../../virtual-machines/extensions/key-vault-linux.md) |
|Agente di Monitoraggio di Azure |Microsoft.Azure.Monitor |AzureMonitorLinuxAgent |[Installare l'Monitoraggio di Azure di distribuzione (anteprima)](../../azure-monitor/agents/azure-monitor-agent-install.md) |

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità dipende dai provider di risorse di Azure seguenti nella sottoscrizione:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se non sono già registrati, seguire la procedura descritta in [Registrare i provider di risorse di Azure.](agent-overview.md#register-azure-resource-providers)

Assicurarsi di esaminare la documentazione per ogni estensione di macchina virtuale a cui si fa riferimento nella tabella precedente per comprendere se sono presenti requisiti di rete o di sistema. Ciò consente di evitare problemi di connettività con un servizio o una funzionalità di Azure che si basa su tale estensione di macchina virtuale.

### <a name="log-analytics-vm-extension"></a>Estensione macchina virtuale di Log Analytics

L'estensione macchina virtuale dell'agente di Log Analytics per Linux richiede l'installazione di Python 2.x nel computer di destinazione.

### <a name="azure-key-vault-vm-extension-preview"></a>Azure Key Vault macchina virtuale (anteprima)

L Key Vault di macchina virtuale virtuale (anteprima) non supporta i sistemi operativi Linux seguenti:

- CentOS Linux 7 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

La distribuzione dell'estensione Key Vault macchina virtuale (anteprima) è supportata solo tramite:

- Interfaccia della riga di comando di Azure
- Oggetto Azure PowerShell
- Modello di Azure Resource Manager

Prima di distribuire l'estensione, è necessario completare quanto segue:

1. [Creare un insieme di credenziali e un](../../key-vault/certificates/quick-create-portal.md) certificato (autofirmati o importati).

2. Concedere al Azure Arc server abilitato l'accesso al segreto del certificato. Se si usa l'anteprima [del](../../key-vault/general/rbac-guide.md)controllo degli accessi in base al ruolo, cercare il nome della risorsa Azure Arc e assegnarle il ruolo Key Vault **Secrets User (anteprima).** Se si usano criteri di [Key Vault](../../key-vault/general/assign-access-policy-portal.md)di accesso, assegnare le autorizzazioni Secret **Get** Azure Arc'identità assegnata dal sistema della risorsa.

### <a name="connected-machine-agent"></a>Agente Connected Machine

Verificare che il computer corrisponda [alle versioni supportate](agent-overview.md#supported-operating-systems) del sistema operativo Windows e Linux per l Azure Connected Machine agent.

La versione minima dell'agente Connected Machine supportata con questa funzionalità in Windows e Linux è la versione 1.0.

Per aggiornare il computer alla versione dell'agente necessaria, vedere [Aggiornare l'agente](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Passaggi successivi

È possibile distribuire, gestire e rimuovere estensioni di macchina virtuale usando l'interfaccia della riga di comando di [Azure,](manage-vm-extensions-cli.md) [Azure PowerShell](manage-vm-extensions-powershell.md), dal portale di Azure [o](manage-vm-extensions-portal.md)dai modelli [Azure Resource Manager.](manage-vm-extensions-template.md)
