---
title: Panoramica di Rimuovi Avvio/Arresto di macchine virtuali durante gli orari di minore attività di automazione di Azure
description: Questo articolo descrive come rimuovere la funzionalità di Avvio/Arresto di macchine virtuali durante gli orari di minore attività e scollegare un account di automazione dall'area di lavoro Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2021
ms.topic: conceptual
ms.openlocfilehash: bed9cd23abc96c51cad0a13e81ee0b64f0d433b6
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012318"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Rimuovere Avvio/Arresto di macchine virtuali durante gli orari di minore attività dall'account di automazione

Dopo aver abilitato la funzionalità di Avvio/Arresto di macchine virtuali durante gli orari di minore attività per gestire lo stato di esecuzione delle macchine virtuali di Azure, è possibile decidere di smettere di usarla. La rimozione di questa funzionalità può essere eseguita utilizzando uno dei metodi seguenti basati sui modelli di distribuzione supportati:

* Eliminare il gruppo di risorse contenente l'account di automazione e il monitoraggio di Azure collegato Log Analytics area di lavoro, ciascuno dedicato per supportare questa funzionalità.
* Scollegare l'area di lavoro Log Analytics dall'account di automazione ed eliminare l'account di automazione dedicato per questa funzionalità.
* Eliminare la funzionalità da un account di automazione e da un'area di lavoro collegata che supporta altri obiettivi di gestione e monitoraggio.

L'eliminazione di questa funzionalità comporta solo la rimozione del manuali operativi associato, non l'eliminazione delle pianificazioni o delle variabili create durante la distribuzione o di quelle personalizzate create dopo.

## <a name="delete-the-dedicated-resource-group"></a>Eliminare il gruppo di risorse dedicato

1. Accedere ad Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .

2. Passare all'account di automazione e selezionare **area di lavoro collegata** in **risorse correlate**.

3. Selezionare **Vai all'area di lavoro**.

4. Fare clic su **Soluzioni** e **Generale**.

5. Nella pagina Soluzioni selezionare **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).

6. Nella pagina **VMManagementSolution [workspace]** selezionare **Elimina** dal menu.

    ![Eliminare la funzionalità di gestione delle VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

7. Per eliminare il gruppo di risorse creato per supportare solo Avvio/Arresto di macchine virtuali durante gli orari di minore attività, seguire i passaggi descritti nell'articolo relativo al [gruppo di risorse e all'eliminazione delle risorse Azure Resource Manager](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-the-automation-account"></a>Eliminare l'account di automazione

Per eliminare l'account di automazione dedicato a Avvio/Arresto di macchine virtuali durante gli orari di minore attività, seguire questa procedura.

1. Accedere ad Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .

2. Passare all'account di automazione e selezionare **area di lavoro collegata** in **risorse correlate**.

3. Selezionare **Vai all'area di lavoro**.

4. Fare clic su **Soluzioni** e **Generale**.

5. Nella pagina Soluzioni selezionare **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).

6. Nella pagina **VMManagementSolution [workspace]** selezionare **Elimina** dal menu.

7. Durante la verifica delle informazioni e l'eliminazione della funzionalità è possibile tenere traccia dello stato scegliendo **Notifiche** dal menu. Al termine del processo di rimozione verrà visualizzata di nuovo la pagina Soluzioni.

### <a name="unlink-workspace-from-automation-account"></a>Scollegare l'area di lavoro dall'account di Automazione

Sono disponibili due opzioni per scollegare l'area di lavoro Log Analytics dall'account di automazione. È possibile eseguire questo processo dall'account di automazione o dall'area di lavoro collegata.

Per scollegare l'account di automazione, seguire questa procedura.

1. Nel portale di Azure selezionare **Account di Automazione**.

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare **Scollega area di lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

Per scollegare dall'area di lavoro, seguire questa procedura.

1. Nella portale di Azure selezionare **log Analytics aree di lavoro**.

2. Dall'area di lavoro, selezionare **account di automazione** in **risorse correlate**.

3. Nella pagina account di automazione selezionare **Scollega account** e rispondi alle richieste.

Mentre tenta di scollegare l'account di automazione, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

### <a name="delete-automation-account"></a>Elimina account di automazione

1. Nel portale di Azure selezionare **Account di Automazione**.

2. Aprire l'account di automazione e scegliere **Elimina** dal menu.

Mentre le informazioni vengono verificate e l'account viene eliminato, è possibile tenere traccia dello stato di avanzamento in **notifiche**, scelto dal menu.

## <a name="delete-the-feature"></a>Elimina la funzionalità

Per eliminare Avvio/Arresto di macchine virtuali durante gli orari di minore attività dall'account di automazione, seguire questa procedura. L'account di Automazione e l'area di lavoro Log Analytics non vengono eliminati come parte del processo. Se non si vuole tenere l'area di lavoro Log Analytics, è necessario eliminarla manualmente. Per altre informazioni sull'eliminazione dell'area di lavoro, vedere [eliminare e ripristinare l'area di lavoro di Azure log Analytics](../azure-monitor/platform/delete-workspace.md).

1. Passare all'account di automazione e selezionare **area di lavoro collegata** in **risorse correlate**.

2. Selezionare **Vai all'area di lavoro**.

3. Fare clic su **Soluzioni** e **Generale**.

4. Nella pagina Soluzioni selezionare **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).

5. Nella pagina **VMManagementSolution [workspace]** selezionare **Elimina** dal menu.

    ![Eliminare la funzionalità di gestione delle VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Nella finestra Elimina soluzione confermare di voler eliminare la funzionalità.

7. Durante la verifica delle informazioni e l'eliminazione della funzionalità è possibile tenere traccia dello stato scegliendo **Notifiche** dal menu. Al termine del processo di rimozione verrà visualizzata di nuovo la pagina Soluzioni.

8. Se non si vogliono tenere le [risorse](automation-solution-vm-management.md#components) create dalla funzionalità o in seguito, ad esempio variabili, pianificazioni e così via, è necessario eliminarle manualmente dall'account.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare nuovamente questa funzionalità, vedere [abilitare l'avvio/arresto durante gli orari](automation-solution-vm-management-enable.md)di indisponibilità.