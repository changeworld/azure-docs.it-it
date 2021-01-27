---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e5edcfad636c76107adc7e72b50ea61249aa00a
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807122"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Distribuire: configurare i prerequisiti per abilitare monitoraggio di Azure e gli agenti di sicurezza di Azure nelle macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Configurare i computer per installare automaticamente il monitoraggio di Azure e gli agenti di sicurezza di Azure. Il Centro sicurezza raccoglie gli eventi dagli agenti e li usa per fornire avvisi di sicurezza e attività di protezione avanzata personalizzate (raccomandazioni). Creare un gruppo di risorse e un'area di lavoro Log Analytics nella stessa area del computer in cui archiviare i record di controllo. Questo criterio si applica solo alle macchine virtuali in alcune aree. |5 |1.0.0-preview |
|[Abilita Monitoraggio di Azure nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Abilita Monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se la proprietà upgradePolicy del set di scalabilità è impostata su Manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento in tali VM. Nell'interfaccia della riga di comando il comando da usare è az vmss update-instances. |6 |1.0.1 |
|[Abilita Monitoraggio di Azure per le macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Abilitare Monitoraggio di Azure per le macchine virtuali (VM) nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |10 |2.0.0 |
