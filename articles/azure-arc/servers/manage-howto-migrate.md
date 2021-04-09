---
title: Come eseguire la migrazione di server abilitati per Azure Arc tra aree
description: Informazioni su come eseguire la migrazione di un server abilitato per Azure Arc da un'area a un'altra.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650176"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Come eseguire la migrazione di server abilitati per Azure Arc tra aree

Esistono scenari in cui si vuole spostare il server di Azure Arc abilitato esistente da un'area a un'altra. Ad esempio, è stato realizzato che il computer è stato registrato nell'area errata, per migliorare la gestibilità o per spostare i motivi di governance.

Per eseguire la migrazione di un server abilitato per Azure Arc da un'area di Azure a un'altra, è necessario disinstallare le estensioni della macchina virtuale, eliminare la risorsa in Azure e ricrearla nell'altra area. Prima di eseguire questi passaggi, è necessario controllare il computer per verificare quali estensioni della macchina virtuale sono installate.

> [!NOTE]
> Mentre le estensioni installate continuano a funzionare ed eseguono le normali operazioni al termine di questa procedura, non sarà possibile gestirle. Se si tenta di ridistribuire le estensioni nel computer, è possibile che si verifichi un comportamento imprevedibile.

## <a name="move-machine-to-other-region"></a>Sposta il computer in un'altra area

> [!NOTE]
> Durante questa operazione, si verifica un tempo di inattività durante la migrazione.

1. Rimuovere le estensioni VM installate dalla [portale di Azure](manage-vm-extensions-portal.md#uninstall-extension), usando l' [interfaccia](manage-vm-extensions-cli.md#remove-an-installed-extension)della riga di comando di Azure o [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

2. Usare lo strumento **azcmagent** con il parametro [Disconnect](manage-agent.md#disconnect) per disconnettere il computer da Azure Arc ed eliminare la risorsa Machine da Azure. La disconnessione del computer dai server abilitati per Arc non comporta la rimozione dell'agente del computer connesso e non è necessario rimuovere l'agente come parte di questo processo. È possibile eseguire questa operazione manualmente durante l'accesso interattivo oppure automatizzare l'uso della stessa entità servizio usata per l'onboarding di più agenti o con un [token di accesso](../../active-directory/develop/access-tokens.md)della piattaforma Microsoft Identity. Se non è stata usata un'entità servizio per registrare il computer con i server abilitati per Azure Arc, vedere l' [articolo](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) seguente per creare un'entità servizio.

3. Registrare di nuovo l'agente del computer connesso con i server abilitati per Arc nell'altra area. Eseguire lo `azcmagent` strumento con il parametro [Connect](manage-agent.md#connect) per completare questo passaggio.

4. Ridistribuire le estensioni VM originariamente distribuite nel computer da server abilitati per Arc. Se l'agente di Monitoraggio di Azure per le macchine virtuali (Insights) o l'agente di Log Analytics è stato distribuito usando criteri di Azure, gli agenti vengono ridistribuiti dopo il [ciclo di valutazione](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)successivo.

## <a name="next-steps"></a>Passaggi successivi

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer usando [criteri di Azure](../../governance/policy/overview.md), come la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md)della VM, verificando che il computer stia segnalando l'area di lavoro log Analytics prevista, abilitare il monitoraggio con [monitoraggio di Azure con](../../azure-monitor/vm/vminsights-enable-policy.md) i criteri di VM e molto altro ancora.