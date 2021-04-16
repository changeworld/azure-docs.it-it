---
title: Eliminare l'account Automazione di Azure
description: Questo articolo descrive come eliminare l'account di Automazione nei diversi scenari di configurazione.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: d088f3adc391068de5e337c10ab52dc3d3a2dd07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535554"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Come eliminare l'account Automazione di Azure

Dopo aver abilitato un account Automazione di Azure per automatizzare il processo IT o aziendale o aver abilitato le altre funzionalità per supportare la gestione delle operazioni dei computer Azure e non Azure, ad esempio Gestione aggiornamenti, è possibile decidere di interrompere l'uso dell'account di Automazione. Se sono state abilitate funzionalità che dipendono dall'integrazione con un'area di lavoro Log Analytics Monitoraggio di Azure, sono necessari altri passaggi per completare questa azione.

La rimozione dell'account di Automazione può essere eseguita usando uno dei metodi seguenti in base ai modelli di distribuzione supportati:

* Eliminare il gruppo di risorse contenente l'account di Automazione.
* Eliminare il gruppo di risorse contenente l'account di Automazione e l Monitoraggio di Azure'area di lavoro Log Analytics, se:

    * L'account e l'area di lavoro sono dedicati al supporto Gestione aggiornamenti, Rilevamento modifiche e inventario e/o Avvio/Arresto di macchine virtuali durante gli orari di minore attività.
    * L'account è dedicato all'automazione dei processi e integrato con un'area di lavoro per inviare lo stato e i flussi del processo del runbook.

* Scollegare l'area di lavoro Log Analytics dall'account di Automazione ed eliminare l'account di Automazione.
* Eliminare la funzionalità dall'area di lavoro collegata, scollegare l'account dall'area di lavoro e quindi eliminare l'account di Automazione.

Questo articolo illustra come rimuovere completamente l'account di Automazione tramite il portale di Azure, usando Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

> [!NOTE]
> Prima di procedere, verificare [](../azure-resource-manager/management/lock-resources.md) che non siano presenti blocchi Resource Manager applicati alla sottoscrizione, al gruppo di risorse o alla risorsa, impedendo così l'eliminazione o la modifica accidentale delle risorse critiche. Se è stata distribuita la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività, il livello di blocco viene impostato su **CanNotDelete** su diverse risorse dipendenti nell'account di Automazione, in particolare i runbook e le variabili. Tutti i blocchi devono essere rimossi prima di poter eliminare l'account di Automazione.

## <a name="delete-the-dedicated-resource-group"></a>Eliminare il gruppo di risorse dedicato

Per eliminare l'account di Automazione e anche l'area di lavoro Log Analytics, se collegata all'account, creata nello stesso gruppo di risorse dedicato all'account, seguire la procedura descritta nell'articolo Azure Resource Manager [resource group and resource deletion](../azure-resource-manager/management/delete-resource-group.md) (Gruppo di risorse di Azure Resource Manager ed eliminazione delle risorse).

## <a name="delete-a-standalone-automation-account"></a>Eliminare un account di Automazione autonomo

Se l'account di Automazione non è collegato a un'area di lavoro Log Analytics, seguire questa procedura per eliminarlo.

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Accedere ad Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .

2. Nel portale di Azure passare ad **Account di Automazione**.

3. Aprire l'account di Automazione e **selezionare Elimina** dal menu.

Mentre le informazioni vengono verificate e l'account viene eliminato, è possibile tenere traccia dello stato di avanzamento in **Notifiche**, scelto dal menu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Questo comando rimuove l'account di Automazione senza richiedere la convalida.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Eliminare un account di Automazione autonomo collegato all'area di lavoro

Se l'account di Automazione è collegato a un'area di lavoro Log Analytics per raccogliere i flussi di processo e i log dei processi, seguire questa procedura per eliminare l'account.

Sono disponibili due opzioni per scollegare l'area di lavoro Log Analytics dall'account di Automazione. È possibile eseguire questo processo dall'account di Automazione o dall'area di lavoro collegata.

Per scollegarsi dall'account di Automazione, seguire questa procedura.

1. Nel portale di Azure passare ad **Account di Automazione**.

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare Scollega area di **lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Per scollegare dall'area di lavoro, seguire questa procedura.

1. Nel portale di Azure passare alle aree **di lavoro Log Analytics.**

2. Nell'area di lavoro selezionare **Account di Automazione** in **Risorse correlate.**

3. Nella pagina Account di Automazione selezionare **Scollega account** e rispondere alle richieste.

Mentre tenta di scollegare l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Dopo aver scollegato correttamente l'account di Automazione dall'area di lavoro, seguire la procedura descritta nella sezione Account di [Automazione](#delete-a-standalone-automation-account) autonomo per eliminare l'account.

## <a name="delete-a-shared-capability-automation-account"></a>Eliminare un account di Automazione per funzionalità condivise

Per eliminare l'account di Automazione collegato a un'area di lavoro Log Analytics a supporto di Gestione aggiornamenti, Rilevamento modifiche e inventario e/o Avvio/Arresto di macchine virtuali durante gli orari di minore attività, seguire questa procedura.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Passaggio 1. Eliminare la soluzione dall'area di lavoro collegata

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Accedere ad Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .

2. Passare all'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate.**

3. Selezionare **Vai all'area di lavoro**.

4. Fare clic su **Soluzioni** e **Generale**.

5. Nella pagina Soluzioni selezionare una delle opzioni seguenti in base alle funzionalità distribuite nell'account:

    * Per Avvio/Arresto di macchine virtuali durante gli orari di minore attività, selezionare **Start-Stop-VM[workspace name]**.
    * Per Gestione aggiornamenti, selezionare **Aggiornamenti (nome dell'area di lavoro).**
    * Per Rilevamento modifiche e inventario, selezionare **ChangeTracking (nome dell'area di lavoro).**

6. Nella **pagina Soluzione** selezionare **Elimina** dal menu. Se nell'account di Automazione e nell'area di lavoro collegata vengono distribuite più funzionalità elencate sopra, è necessario selezionarle ed eliminarle prima di procedere.

7. Durante la verifica delle informazioni e l'eliminazione della funzionalità è possibile tenere traccia dello stato scegliendo **Notifiche** dal menu. Al termine del processo di rimozione verrà visualizzata di nuovo la pagina Soluzioni.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per rimuovere una soluzione installata usando Azure PowerShell, usare il cmdlet [Remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Passaggio 2: Scollegare l'area di lavoro dall'account di Automazione

Sono disponibili due opzioni per scollegare l'area di lavoro Log Analytics dall'account di Automazione. È possibile eseguire questo processo dall'account di Automazione o dall'area di lavoro collegata.

Per scollegarsi dall'account di Automazione, seguire questa procedura.

1. Nel portale di Azure passare ad Account **di Automazione.**

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare Scollega area di **lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Per scollegare dall'area di lavoro, seguire questa procedura.

1. Nel portale di Azure passare alle aree **di lavoro Log Analytics.**

2. Nell'area di lavoro selezionare **Account di Automazione** in **Risorse correlate**.

3. Nella pagina Account di Automazione selezionare **Scollega account** e rispondere alle richieste.

Mentre tenta di scollegare l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### <a name="step-3-delete-automation-account"></a>Passaggio 3. Eliminare un account di Automazione

Dopo aver scollegato correttamente l'account di Automazione dall'area di lavoro, seguire la procedura descritta nella sezione Account di [Automazione](#delete-a-standalone-automation-account) autonomo per eliminare l'account.

## <a name="next-steps"></a>Passaggi successivi

Per creare un account di Automazione dal portale di Azure, vedere [Creare un account di Automazione di Azure autonomo](automation-create-standalone-account.md). Se si preferisce creare l'account usando un modello, vedere [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md).