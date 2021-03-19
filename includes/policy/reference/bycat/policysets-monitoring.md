---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 30f24136e555aa542864f2e7895b45443297348c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604926"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[\[Anteprima \] : distribuire e configurare i prerequisiti per abilitare monitoraggio di Azure e gli agenti di sicurezza di Azure nelle macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Configurare i computer per installare automaticamente il monitoraggio di Azure e gli agenti di sicurezza di Azure. Il Centro sicurezza raccoglie gli eventi dagli agenti e li usa per fornire avvisi di sicurezza e attività di protezione avanzata personalizzate (raccomandazioni). Creare un gruppo di risorse e un'area di lavoro Log Analytics nella stessa area del computer in cui archiviare i record di controllo. Questo criterio si applica solo alle macchine virtuali in alcune aree. |5 |1.0.0-preview |
|[Abilita Monitoraggio di Azure nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Abilita Monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se la proprietà upgradePolicy del set di scalabilità è impostata su Manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento in tali VM. Nell'interfaccia della riga di comando il comando da usare è az vmss update-instances. |6 |1.0.1 |
|[Abilita Monitoraggio di Azure per le macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Abilitare Monitoraggio di Azure per le macchine virtuali (VM) nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |10 |2.0.0 |
