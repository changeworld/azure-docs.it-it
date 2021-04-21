---
title: Automazione di Azure sicurezza dei dati
description: Questo articolo illustra in che modo Automazione di Azure protegge la privacy e protegge i dati.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb18cca782b85e608c3c7ddb001ecb03b86055f6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833527"
---
# <a name="management-of-azure-automation-data"></a>Gestione dei dati di Automazione di Azure

Questo articolo contiene diversi argomenti che illustrano come i dati vengono protetti e protetti in un Automazione di Azure locale.

## <a name="tls-12-enforcement-for-azure-automation"></a>Imposizione di TLS 1.2 per Automazione di Azure

Per assicurare la sicurezza dei dati in transito verso Automazione di Azure, è consigliabile configurare l'uso di Transport Layer Security (TLS) 1.2. Di seguito è riportato un elenco di metodi o client che comunicano tramite HTTPS al servizio di automazione:

* Chiamate webhook

* I thread di lavoro ibridi per runbook, che includono computer gestiti da Gestione aggiornamenti e Rilevamento modifiche e inventario.

* Nodi DSC

Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**. Non è consigliabile impostare in modo esplicito l'agente in modo che usi solo TLS 1.2 a meno che non sia necessario, in quanto può interrompere le funzionalità di sicurezza a livello di piattaforma che consentono di rilevare e sfruttare automaticamente i protocolli più nuovi e più sicuri non appena diventano disponibili, ad esempio TLS 1.3.

Per informazioni sul supporto di TLS 1.2 con l'agente di Log Analytics per Windows e Linux, che è una dipendenza per il ruolo di lavoro ibrido per runbook, vedere Panoramica dell'agente [di Log Analytics - TLS 1.2.](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol)

### <a name="platform-specific-guidance"></a>Indicazioni specifiche in base alla piattaforma

|Piattaforma/linguaggio | Supporto | Altre informazioni |
| --- | --- | --- |
|Linux | Le distribuzioni Linux si basano generalmente su [OpenSSL](https://www.openssl.org) per supportare TLS 1.2.  | Controllare nel [log delle modifiche di OpenSSL](https://www.openssl.org/news/changelog.html) per assicurarsi che la versione di OpenSSL sia supportata.|
| Windows 8.0 - 10 | Supportato e abilitato per impostazione predefinita. | Assicurarsi che le [impostazioni predefinite](/windows-server/security/tls/tls-registry-settings) siano ancora in uso.  |
| Windows Server 2012 - 2016 | Supportato e abilitato per impostazione predefinita. | Per verificare che siano ancora in uso le [impostazioni predefinite](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Supportato ma non abilitato per impostazione predefinita. | Vedere la pagina [Transport Layer Security (TLS) registry settings](/windows-server/security/tls/tls-registry-settings) (Impostazioni del Registro di sistema di Transport Layer Security (TLS)) per informazioni dettagliate su come eseguire l'abilitazione.  |

## <a name="data-retention"></a>Conservazione dei dati

Quando si elimina una risorsa in Automazione di Azure, questa viene conservata per molti giorni a scopo di controllo prima della rimozione definitiva. In tale periodo non sarà possibile visualizzare o usare la risorsa. Questi criteri sono applicabili anche alle risorse appartenenti a un account di Automazione eliminato. I criteri di conservazione sono applicabili a tutti gli utenti e non è attualmente possibile personalizzarli. Se tuttavia è necessario conservare i dati per un periodo di tempo più lungo, è possibile [inviare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

| Data | Policy |
|:--- |:--- |
| Account |Un account viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente. |
| Asset |Un asset viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione di un account che possiede l'asset. Gli asset includono variabili, pianificazioni, credenziali, certificati, pacchetti Python 2 e connessioni. |
| Nodi DSC |Un nodo DSC viene rimosso in modo permanente 30 giorni dopo l'annullamento della registrazione dall'account di Automazione tramite il portale di Azure o il cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) in Windows PowerShell. I nodi vengono rimossi in modo permanente anche 30 giorni dopo aver eliminato l'account che possiede il nodo. |
| Processi |Un processo viene eliminato e rimosso definitivamente 30 giorni dopo la modifica, ad esempio dopo il completamento, l'arresto o la sospensione del processo. |
| Moduli |Un modulo viene rimosso definitivamente 30 giorni dopo l'eliminazione da parte di un utente o 30 giorni dopo l'eliminazione di un account che possiede il modulo. |
| Configurazioni di nodo/File MOF |Una configurazione di nodo precedente verrà rimossa definitivamente 30 giorni dopo che viene generata una nuova configurazione di nodo. |
| Report sul nodo |Un report sul nodo viene rimosso definitivamente 90 giorni dopo la generazione di un nuovo report per quel nodo. |
| Runbook |Un runbook viene rimosso definitivamente 30 giorni dopo che un utente ha eliminato la risorsa oppure 30 giorni dopo che un utente ha eliminato l'account che contiene la<sup>risorsa 1.</sup> |

<sup>1</sup> Il runbook può essere recuperato entro la finestra di 30 giorni tramite la presentazione di un evento supporto tecnico di Azure evento imprevisto con Microsoft Azure supporto tecnico. Passare al sito [supporto tecnico di Azure e](https://azure.microsoft.com/support/options/) selezionare Invia una richiesta **di supporto**.

## <a name="data-backup"></a>Backup dei dati

Quando si elimina un account di automazione in Azure, vengono eliminati tutti gli oggetti presenti nell'account. Gli oggetti includono runbook, moduli, configurazioni, impostazioni, processi e asset. Non sarà possibile ripristinare gli oggetti dopo l'eliminazione dell'account. È possibile usare le informazioni seguenti per eseguire il backup dei contenuti dell'account di Automazione prima di eliminarlo.

### <a name="runbooks"></a>Runbook

È possibile esportare i runbook in file di script usando il portale di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) in Windows PowerShell. È possibile importare questi file di script in un altro account di Automazione, come illustrato in [Gestire runbook in Automazione di Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Moduli di integrazione

Non è possibile esportare moduli di integrazione da Automazione di Azure, ma devono essere resi disponibili all'esterno dell'account di Automazione.

### <a name="assets"></a>Asset

Non è possibile esportare gli asset di Automazione di Azure: certificati, connessioni, credenziali, pianificazioni e variabili. È invece possibile usare il portale di Azure e i cmdlet di Azure per prendere nota dei dettagli di questi asset. Usare quindi questi dettagli per creare eventuali asset usati dai runbook importati in un altro account di Automazione.

Non è possibile recuperare il valore delle variabili crittografate o i campi password delle credenziali mediante i cmdlet. Se non si conoscono questi valori, è possibile recuperarli in un runbook. Per recuperare i valori delle variabili, vedere [Asset di tipo variabile in Automazione di Azure](shared-resources/variables.md). Per altre informazioni sul recupero dei valori delle credenziali, vedere [Asset credenziali in Automazione di Azure](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>Configurazioni DSC

È possibile esportare le configurazioni DSC in file di script tramite il portale di Azure o il cmdlet [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) in Windows PowerShell. È possibile importare e usare queste configurazioni in un altro account di Automazione.

## <a name="geo-replication-in-azure-automation"></a>Replica geografica in Automazione di Azure

La replica geografica è standard negli account di Automazione di Azure. Quando si configura l'account, è possibile scegliere un'area primaria. Il servizio interno di replica geografica di Automazione assegna automaticamente un'area secondaria all'account. Il servizio esegue quindi il backup continuo dei dati dell'account dall'area primaria all'area secondaria. L'elenco completo delle aree primarie e secondarie è disponibile in [Continuità aziendale e ripristino di emergenza (BCDR): aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

Il backup creato dal servizio di replica geografica di Automazione è una copia completa degli asset di automazione, delle configurazioni e di elementi simili. Questo backup può essere usato se l'area primaria diventa inattiva e perde i dati. Nell'eventualità improbabile che vengano persi dei dati di un'area primaria, Microsoft tenta di ripristinarli.

> [!NOTE]
> Automazione di Azure i dati dei clienti nell'area selezionata dal cliente. Ai fini del bcdr, per tutte le aree tranne Brasile meridionale e Asia sud-orientale, i dati Automazione di Azure vengono archiviati in un'area diversa (area abbinata di Azure). Solo per l'area Brasile meridionale (Stato di San Paolo) del Brasile e l'area Asia sud-orientale (Singapore) dell'area geografica Asia Pacifico, i dati Automazione di Azure vengono archiviati nella stessa area per soddisfare i requisiti di residenza dei dati per queste aree.

Il servizio di replica geografica di Automazione non è accessibile direttamente ai clienti esterni se si verifica un errore a livello di area. Se si vogliono mantenere la configurazione e i runbook di Automazione durante gli errori a livello di area:

1. Selezionare un'area secondaria da associare all'area geografica dell'account di Automazione primario.

2. Creare un account di Automazione nell'area secondaria.

3. Nell'account primario esportare i runbook come file di script.

4. Importare i runbook nell'account di Automazione dell'area secondaria.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli asset sicuri in Automazione di Azure, vedere [Crittografia di asset sicuri in Automazione di Azure](automation-secure-asset-encryption.md).

* Per altre informazioni sulla replica geografica, vedere [Creazione e uso della replica geografica attiva](../azure-sql/database/active-geo-replication-overview.md).
