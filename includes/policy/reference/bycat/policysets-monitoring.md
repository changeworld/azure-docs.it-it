---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5c1857372eb4aad2bf87e4078bae5e1fc41412a8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091853"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Distribuire: configurare i prerequisiti per abilitare monitoraggio di Azure e gli agenti di sicurezza di Azure nelle macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Configurare i computer per installare automaticamente il monitoraggio di Azure e gli agenti di sicurezza di Azure. Il Centro sicurezza raccoglie gli eventi dagli agenti e li usa per fornire avvisi di sicurezza e attività di protezione avanzata personalizzate (raccomandazioni). Creare un gruppo di risorse e un'area di lavoro Log Analytics nella stessa area del computer in cui archiviare i record di controllo. Questo criterio si applica solo alle macchine virtuali in alcune aree. |5 |1.0.0-preview |
|[Abilita Monitoraggio di Azure nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Abilita Monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se la proprietà upgradePolicy del set di scalabilità è impostata su Manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento in tali VM. Nell'interfaccia della riga di comando il comando da usare è az vmss update-instances. |6 |1.0.1 |
|[Abilita Monitoraggio di Azure per le macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Abilitare Monitoraggio di Azure per le macchine virtuali (VM) nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |10 |2.0.0 |
