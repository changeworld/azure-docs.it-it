---
title: Abilitare Rilevamento modifiche e inventario di Automazione di Azure da una macchina virtuale di Azure
description: Questo articolo descrive come abilitare Rilevamento modifiche e inventario da una macchina virtuale di Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 61b45d6f6414b1e8e1f48f6d46957b21b9b8c58b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052499"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Abilitare Rilevamento modifiche e inventario da una macchina virtuale di Azure

Questo articolo descrive come usare una macchina virtuale di Azure per abilitare [rilevamento modifiche e l'inventario](overview.md) in altri computer. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Rilevamento modifiche e inventario.

> [!NOTE]
> Quando si abilita Rilevamento modifiche e inventario, sono supportate solo determinate aree geografiche per il collegamento a un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../automation-security-overview.md) per gestire i computer.
* Una [macchina virtuale](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Macchine virtuali** oppure cercare e selezionare **Macchine virtuali** nella pagina Home.

2. Selezionare la macchina virtuale per cui si vuole abilitare Rilevamento modifiche e inventario. Le macchine virtuali possono esistere in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione.

3. Nella pagina della VM selezionare **Inventario** o **Rilevamento modifiche** in **Gestione della configurazione**.

4. È necessario avere l'autorizzazione `Microsoft.OperationalInsights/workspaces/read` per determinare se la macchina virtuale è abilitata per un'area di lavoro. Per informazioni sulle autorizzazioni aggiuntive necessarie, vedere le [autorizzazioni di installazione delle funzionalità](../automation-role-based-access-control.md#feature-setup-permissions). Per informazioni su come abilitare più macchine contemporaneamente, vedere [Abilitare Rilevamento modifiche e inventario da un account di Automazione](enable-from-automation-account.md).

5. Scegliere l'area di lavoro Log Analytics e l'account di Automazione e fare clic su **Abilita** per abilitare Rilevamento modifiche e inventario per la macchina virtuale. L'installazione richiede fino a 15 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso della funzionalità, vedere [gestire rilevamento modifiche](manage-change-tracking.md) e [gestire l'inventario](manage-inventory-vms.md).

* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](../troubleshoot/change-tracking.md).