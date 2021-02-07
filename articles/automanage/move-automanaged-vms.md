---
title: Spostare una macchina virtuale di Azure automanage tra le aree
description: Informazioni su come spostare una macchina virtuale gestita in diverse aree
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e880992143bf79a5a99cc9830957f83167b6b46
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808291"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Spostare una macchina virtuale di Azure automanage in un'area diversa
Questo articolo descrive come proteggere la gestione automatica in una macchina virtuale (VM) quando la si sposta in un'area diversa. Potrebbe essere necessario spostare le macchine virtuali in un'altra area per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area di Azure, per soddisfare i requisiti di governance e criteri interni oppure in risposta ai requisiti della pianificazione della capacità. Le macchine virtuali spostate potrebbero essere attualmente gestite in modo autogestito ed è consigliabile che rimangano gestite in modo autogestito dopo lo spostamento.

## <a name="prerequisites"></a>Prerequisiti
* Assicurarsi che l'area di destinazione sia [supportata da automanage](./automanage-virtual-machines.md#prerequisites).
* Assicurarsi che la Log Analytics area dell'area di lavoro, l'area dell'account di automazione e l'area di destinazione siano tutte aree supportate dai mapping dell'area [qui](https://docs.microsoft.com/azure/automation/how-to/region-mappings).

## <a name="prepare-your-automanaged-vms-for-moving"></a>Preparare le macchine virtuali autogestite per lo stato di trasferimento
Disabilitare la gestione delle macchine virtuali autogestite. A tale scopo, selezionare le macchine virtuali nel pannello automanage e fare clic su **Disabilita Gestione autogestione** nel pannello automanage.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Spostare le macchine virtuali autogestite e abilitare di nuovo la gestione autonoma
Per informazioni dettagliate su come spostare le VM, vedere questo [articolo](https://docs.microsoft.com/azure/resource-mover/tutorial-move-region-virtual-machines).

Dopo aver spostato le macchine virtuali in più aree, è possibile riattivarle nuovamente. I dettagli sono disponibili [qui](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni su gestione autogestita di Azure](./automanage-virtual-machines.md)
* [Visualizza le domande frequenti relative a gestione autogestione di Azure](./faq.md)