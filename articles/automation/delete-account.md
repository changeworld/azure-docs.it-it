---
title: Elimina l'account di automazione di Azure
description: Questo articolo descrive come eliminare l'account di automazione nei diversi scenari di configurazione.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735092"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Come eliminare un account di automazione di Azure

Dopo aver abilitato un account di automazione di Azure per automatizzare l'IT o il processo di business oppure abilitare le altre funzionalità per supportare la gestione delle operazioni dei computer Azure e non Azure, ad esempio Gestione aggiornamenti, è possibile decidere di interrompere l'uso dell'account di automazione. Se sono state abilitate funzionalità che dipendono dall'integrazione con un'area di lavoro di monitoraggio di Azure Log Analytics, è necessario eseguire altri passaggi per completare questa azione.

La rimozione dell'account di automazione può essere eseguita usando uno dei metodi seguenti in base ai modelli di distribuzione supportati:

* Eliminare il gruppo di risorse contenente l'account di automazione.
* Eliminare il gruppo di risorse contenente l'account di automazione e il monitoraggio di Azure collegato Log Analytics area di lavoro, se:

    * L'account e l'area di lavoro sono dedicati a supportare Gestione aggiornamenti, Rilevamento modifiche e inventario e/o Avvio/Arresto di macchine virtuali durante gli orari di minore attività.
    * L'account è dedicato all'automazione dei processi ed è integrato con un'area di lavoro per inviare lo stato e i flussi del processo del Runbook.

* Scollegare l'area di lavoro Log Analytics dall'account di automazione ed eliminare l'account di automazione.
* Eliminare la funzionalità dall'area di lavoro collegata, scollegarla dall'area di lavoro e quindi eliminare l'account di automazione.

Questo articolo descrive come rimuovere completamente l'account di automazione tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST.

## <a name="delete-the-dedicated-resource-group"></a>Eliminare il gruppo di risorse dedicato

Per eliminare l'account di automazione e anche l'area di lavoro Log Analytics se collegata all'account, creata nello stesso gruppo di risorse dedicato all'account, seguire i passaggi descritti nell'articolo [Azure Resource Manager Resource Group and Resource deletion](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-a-standalone-automation-account"></a>Eliminare un account di automazione autonomo

Se l'account di automazione non è collegato a un'area di lavoro Log Analytics, attenersi alla procedura seguente per eliminarlo.

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Accedere ad Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .

2. Nella portale di Azure passare a account di **automazione**.

3. Aprire l'account di automazione e scegliere **Elimina** dal menu.

Mentre le informazioni vengono verificate e l'account viene eliminato, è possibile tenere traccia dello stato di avanzamento in **notifiche**, scelto dal menu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Questo comando rimuove l'account di automazione senza richiedere la convalida.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Eliminare un account di automazione autonomo collegato all'area di lavoro

Se l'account di automazione è collegato a un'area di lavoro di Log Analytics per raccogliere i flussi di processo e i log del processo, eseguire la procedura seguente per eliminare l'account.

Sono disponibili due opzioni per scollegare l'area di lavoro Log Analytics dall'account di automazione. È possibile eseguire questo processo dall'account di automazione o dall'area di lavoro collegata.

Per scollegare l'account di automazione, seguire questa procedura.

1. Nella portale di Azure passare a account di **automazione**.

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare **Scollega area di lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

Per scollegare dall'area di lavoro, seguire questa procedura.

1. Nella portale di Azure passare a **log Analytics aree di lavoro**.

2. Dall'area di lavoro, selezionare **account di automazione** in **risorse correlate**.

3. Nella pagina account di automazione selezionare **Scollega account** e rispondere alle richieste.

Mentre tenta di scollegare l'account di automazione, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

Una volta scollegato correttamente l'account di automazione dall'area di lavoro, eseguire i passaggi nella sezione [account di automazione autonomo](#delete-a-standalone-automation-account) per eliminare l'account.

## <a name="delete-a-shared-capability-automation-account"></a>Eliminare un account di automazione della funzionalità condivisa

Per eliminare l'account di automazione collegato a un'area di lavoro di Log Analytics per supportare Gestione aggiornamenti, Rilevamento modifiche e inventario e/o Avvio/Arresto di macchine virtuali durante gli orari di minore attività, seguire questa procedura.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Passaggio 1. Eliminare la soluzione dall'area di lavoro collegata

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Accedere ad Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .

2. Passare all'account di automazione e selezionare **area di lavoro collegata** in **risorse correlate**.

3. Selezionare **Vai all'area di lavoro**.

4. Fare clic su **Soluzioni** e **Generale**.

5. Nella pagina soluzioni selezionare una delle opzioni seguenti in base alle funzionalità distribuite nell'account:

    * Per Avvio/Arresto di macchine virtuali durante gli orari di minore attività, selezionare **Start-Stop-VM [nome area di lavoro]**.
    * Per Gestione aggiornamenti, selezionare **aggiornamenti (nome dell'area di lavoro)**.
    * Per Rilevamento modifiche e l'inventario, selezionare **rilevamento modifiche (nome dell'area di lavoro)**.

6. Nella pagina **soluzione** selezionare **Elimina** dal menu. Se più di una delle funzionalità elencate in precedenza viene distribuita nell'account di automazione e nell'area di lavoro collegata, è necessario selezionare ed eliminare ognuna di esse prima di procedere.

7. Durante la verifica delle informazioni e l'eliminazione della funzionalità è possibile tenere traccia dello stato scegliendo **Notifiche** dal menu. Al termine del processo di rimozione verrà visualizzata di nuovo la pagina Soluzioni.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per rimuovere una soluzione installata usando Azure PowerShell, usare il cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Passaggio 2: Scollegare l'area di lavoro dall'account di Automazione

Sono disponibili due opzioni per scollegare l'area di lavoro Log Analytics dall'account di automazione. È possibile eseguire questo processo dall'account di automazione o dall'area di lavoro collegata.

Per scollegare l'account di automazione, seguire questa procedura.

1. Nella portale di Azure passare a account di **automazione**.

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare **Scollega area di lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

Per scollegare dall'area di lavoro, seguire questa procedura.

1. Nella portale di Azure passare a **log Analytics aree di lavoro**.

2. Dall'area di lavoro, selezionare **account di automazione** in **risorse correlate**.

3. Nella pagina account di automazione selezionare **Scollega account** e rispondere alle richieste.

Mentre tenta di scollegare l'account di automazione, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

### <a name="step-3-delete-automation-account"></a>Passaggio 3. Elimina account di automazione

Una volta scollegato correttamente l'account di automazione dall'area di lavoro, eseguire i passaggi nella sezione [account di automazione autonomo](#delete-a-standalone-automation-account) per eliminare l'account.

## <a name="next-steps"></a>Passaggi successivi

Per creare un account di Automazione dal portale di Azure, vedere [Creare un account di Automazione di Azure autonomo](automation-create-standalone-account.md). Se si preferisce creare l'account usando un modello, vedere [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md).