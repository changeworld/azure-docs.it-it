---
title: Rimuovere Automazione di Azure Avvio/Arresto di macchine virtuali durante gli orari di minore attività panoramica
description: Questo articolo descrive come rimuovere la funzionalità Avvio/Arresto di macchine virtuali durante gli orari di minore attività e scollegare un account di Automazione dall'area di lavoro Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533947"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Rimuovere Avvio/Arresto di macchine virtuali durante gli orari di minore attività dall'account di Automazione

Dopo aver abilitato la Avvio/Arresto di macchine virtuali durante gli orari di minore attività per gestire lo stato di esecuzione delle macchine virtuali di Azure, è possibile decidere di interrompere l'uso. La rimozione di questa funzionalità può essere eseguita usando uno dei metodi seguenti in base ai modelli di distribuzione supportati:

* Eliminare il gruppo di risorse contenente l'account di Automazione e Monitoraggio di Azure'area di lavoro Log Analytics, ognuna dedicata a supportare questa funzionalità.
* Scollegare l'area di lavoro Log Analytics dall'account di Automazione ed eliminare l'account di Automazione dedicato per questa funzionalità.
* Eliminare la funzionalità da un account di Automazione e da un'area di lavoro collegata che supportano altri obiettivi di gestione e monitoraggio.

L'eliminazione di questa funzionalità rimuove solo i runbook associati, non elimina le pianificazioni o le variabili create durante la distribuzione o quelle definite dall'utente create dopo.

> [!NOTE]
> Prima di procedere, verificare [](../azure-resource-manager/management/lock-resources.md) che non siano applicati blocchi Resource Manager alla sottoscrizione, al gruppo di risorse o alla risorsa che impedisce l'eliminazione accidentale o la modifica delle risorse critiche. Quando si distribuisce la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività, il livello di blocco viene impostato su **CanNotDelete** su diverse risorse dipendenti nell'account di Automazione, in particolare i runbook e le variabili. Tutti i blocchi devono essere rimossi prima di poter eliminare l'account di Automazione.

## <a name="delete-the-dedicated-resource-group"></a>Eliminare il gruppo di risorse dedicato

Per eliminare il gruppo di risorse, seguire la procedura descritta nell'articolo Azure Resource Manager gruppo di risorse ed [eliminazione delle](../azure-resource-manager/management/delete-resource-group.md) risorse.

## <a name="delete-the-automation-account"></a>Eliminare l'account di Automazione

Per eliminare l'account di Automazione dedicato Avvio/Arresto di macchine virtuali durante gli orari di minore attività, seguire questa procedura.

1. Accedere ad Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) .

2. Passare all'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate.**

3. Selezionare **Vai all'area di lavoro**.

4. Fare clic su **Soluzioni** e **Generale**.

5. Nella pagina Soluzioni selezionare **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).

6. Nella pagina **VMManagementSolution[Workspace]** selezionare **Elimina** dal menu.

7. Durante la verifica delle informazioni e l'eliminazione della funzionalità è possibile tenere traccia dello stato scegliendo **Notifiche** dal menu. Al termine del processo di rimozione verrà visualizzata di nuovo la pagina Soluzioni.

### <a name="unlink-workspace-from-automation-account"></a>Scollegare l'area di lavoro dall'account di Automazione

Sono disponibili due opzioni per scollegare l'area di lavoro Log Analytics dall'account di Automazione. È possibile eseguire questo processo dall'account di Automazione o dall'area di lavoro collegata.

Per scollegarsi dall'account di Automazione, seguire questa procedura.

1. Nel portale di Azure selezionare **Account di Automazione**.

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare Scollega area **di** lavoro e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Per scollegarsi dall'area di lavoro, seguire questa procedura.

1. Nella finestra portale di Azure selezionare **Aree di lavoro Log Analytics.**

2. Nell'area di lavoro selezionare **Account di Automazione** in **Risorse correlate.**

3. Nella pagina Account di Automazione selezionare **Scollega account** e rispondere alle richieste.

Mentre tenta di scollegare l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

### <a name="delete-automation-account"></a>Eliminare un account di Automazione

1. Nel portale di Azure selezionare **Account di Automazione**.

2. Aprire l'account di Automazione **e scegliere Elimina** dal menu.

Durante la verifica delle informazioni e l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche**, scelto dal menu.

## <a name="delete-the-feature"></a>Eliminare la funzionalità

Per eliminare Avvio/Arresto di macchine virtuali durante gli orari di minore attività dall'account di Automazione, seguire questa procedura. L'account di Automazione e l'area di lavoro Log Analytics non vengono eliminati come parte del processo. Se non si vuole mantenere l'area di lavoro Log Analytics, è necessario eliminarla manualmente. Per altre informazioni sull'eliminazione dell'area di lavoro, vedere [Eliminare e ripristinare l'area di lavoro Azure Log Analytics.](../azure-monitor/logs/delete-workspace.md)

1. Passare all'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate.**

2. Selezionare **Vai all'area di lavoro**.

3. Fare clic su **Soluzioni** e **Generale**.

4. Nella pagina Soluzioni selezionare **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).

5. Nella pagina **VMManagementSolution[Workspace]** selezionare **Elimina** dal menu.

    ![Eliminare la funzionalità di gestione delle VM](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Nella finestra Elimina soluzione confermare di voler eliminare la funzionalità.

7. Durante la verifica delle informazioni e l'eliminazione della funzionalità è possibile tenere traccia dello stato scegliendo **Notifiche** dal menu. Al termine del processo di rimozione verrà visualizzata di nuovo la pagina Soluzioni.

8. Se non si vogliono mantenere [](automation-solution-vm-management.md#components) le risorse create dalla funzionalità o successivamente (ad esempio, variabili, pianificazioni e così via), è necessario eliminarle manualmente dall'account.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare nuovamente questa funzionalità, vedere [Abilitare l'avvio/arresto durante gli orari di ufficio.](automation-solution-vm-management-enable.md)