---
title: Domande frequenti su Automazione di Azure | Microsoft Docs
description: Questo articolo contiene le risposte alle domande frequenti su Automazione di Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ab4ce9b8691f27fe392d2e3099677d45900d2e3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834229"
---
# <a name="azure-automation-frequently-asked-questions"></a>Domande frequenti su Automazione di Azure

Questo articolo contiene un elenco di domande frequenti Microsoft su Automazione di Azure. Per altre domande sulle funzionalità, passare al forum di discussione e pubblicare le domande. Quando viene posta di frequente una domanda, questa viene aggiunta a questo articolo in modo che venga trovata in modo rapido e semplice.

## <a name="update-management"></a>Gestione degli aggiornamenti

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>È possibile evitare aggiornamenti imprevisti a livello del sistema operativo?

In alcune varianti di Linux, ad esempio Red Hat Enterprise Linux, è possibile che gli aggiornamenti a livello di sistema operativo vengano eseguiti tramite pacchetti. Di conseguenza potrebbero verificarsi esecuzioni di Gestione aggiornamenti in cui il numero di versione del sistema operativo cambia. Poiché Gestione aggiornamenti usa gli stessi metodi per l'aggiornamento dei pacchetti usati localmente da un amministratore in un computer Linux, questo comportamento è intenzionale.

Per evitare l'aggiornamento della versione del sistema operativo tramite distribuzioni di Gestione aggiornamenti, usare la funzionalità **Esclusione**.

In Red Hat Enterprise Linux il nome del pacchetto da escludere è `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Perché non vengono applicati aggiornamenti critici e della sicurezza?

Durante la distribuzione degli aggiornamenti in un computer Linux è possibile selezionare le classificazioni degli aggiornamenti. Questa opzione filtra gli aggiornamenti che soddisfano i criteri specificati. Questo filtro viene applicato in locale nel computer in cui viene distribuito l'aggiornamento.

Poiché Gestione aggiornamenti esegue l'arricchimento degli aggiornamenti nel cloud, è possibile contrassegnare alcuni aggiornamenti in Gestione aggiornamenti come elementi che influiscono sulla sicurezza, anche se questa informazione non è indicata nel computer locale. Se si applicano aggiornamenti critici a un computer Linux, è possibile che alcuni non vengano contrassegnati come elementi che influiscono sulla sicurezza del computer e che quindi non vengano applicati. Tuttavia, Gestione aggiornamenti potrebbe comunque segnalare tale computer come non conforme perché contiene informazioni aggiuntive sull'aggiornamento pertinente.

La distribuzione degli aggiornamenti in base alla relativa classificazione non funziona nelle versioni RTM di CentOS. Per distribuire correttamente gli aggiornamenti per CentOS, selezionare tutte le classificazioni per assicurarsi che gli aggiornamenti vengano applicati. Per SUSE, selezionando **SOLO** altri aggiornamenti come classificazione è possibile installare prima alcuni altri aggiornamenti della sicurezza se sono correlati a zypper (gestione pacchetti) o alle relative dipendenze. Si tratta di una limitazione di zypper. In alcuni casi può essere necessario eseguire di nuovo la distribuzione degli aggiornamenti e quindi verificarla tramite l'apposito log.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>È possibile distribuire gli aggiornamenti tra tenant di Azure?

Se si hanno computer in un altro tenant di Azure che segnalano a Gestione aggiornamenti che necessitano di una patch, occorrerà usare la seguente soluzione alternativa per pianificarli. È possibile usare il cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) con il parametro `ForUpdateConfiguration` per creare una pianificazione. Usare quindi il cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) e passare i computer dell'altro tenant al parametro `NonAzureComputer`. L'esempio seguente illustra come farlo.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Automazione dei processi - Runbook Python

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Quale versione di Python 3 è supportata in Automazione di Azure?

Per i processi cloud, è supportato Python 3.8. Gli script e i pacchetti di qualsiasi versione 3.x potrebbero funzionare se il codice è compatibile tra versioni diverse.

Per i processi ibridi in Windows Hybrid Runbook Worker, è possibile scegliere di installare qualsiasi versione 3.x che si vuole usare. Per i processi ibridi nei processi di lavoro ibridi per runbook Linux, si dipende dalla versione di Python 3 installata nel computer per eseguire OMSConfig DSC e il ruolo di lavoro ibrido Linux. È consigliabile installare la versione 3.6. Tuttavia, versioni diverse dovrebbero funzionare anche se non sono presenti modifiche di rilievo nelle firme dei metodi o nei contratti tra le versioni di Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>I runbook Python 2 e Python 3 possono essere eseguiti nello stesso account di automazione?

Sì, non esiste alcuna limitazione per l'uso di runbook Python 2 e Python 3 nello stesso account di automazione.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Qual è il piano per la migrazione di runbook e pacchetti Python 2 esistenti in Python 3?

Automazione di Azure non prevede di eseguire la migrazione di runbook e pacchetti Python 2 a Python 3. È necessario eseguire questa migrazione manualmente. I runbook e i pacchetti Python 2 nuovi e esistenti continueranno a funzionare.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Quali sono i pacchetti supportati per impostazione predefinita nell'ambiente Python 3?

Il pacchetto di Azure 4.0.0 viene installato per impostazione predefinita nell'ambiente di automazione Python 3. È possibile importare manualmente una versione più recente del pacchetto di Azure per eseguire l'override della versione predefinita.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Cosa succede se si esegue un runbook Python 3 che fa riferimento a un pacchetto Python 2 o viceversa?

Python 2 e Python 3 hanno ambienti di esecuzione diversi. Mentre un runbook Python 2 è in esecuzione, solo i pacchetti Python 2 possono essere importati e simili per Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Ricerca per categorie tra runbook e pacchetti Python 2 e Python 3?

Python 3 è una nuova definizione di runbook che distingue tra i runbook Python 2 e Python 3. Analogamente, viene introdotto un altro tipo di pacchetto per i pacchetti Python 3.

## <a name="next-steps"></a>Passaggi successivi

Se non si risponde alla domanda qui, è possibile fare riferimento alle fonti seguenti per altre domande e risposte.

- [Automazione di Azure](/answers/topics/azure-automation.html)
- [Forum di commenti e suggerimenti](https://feedback.azure.com/forums/905242-update-management)
