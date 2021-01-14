---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f86b8ccfdf5a8cdf7dce537989c21ade06cb47d0
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046397"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Abilita Monitoraggio di Azure nei set di scalabilità di macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Abilita Monitoraggio di Azure per i set di scalabilità di macchine virtuali nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. Nota: se la proprietà upgradePolicy del set di scalabilità è impostata su Manuale, è necessario applicare l'estensione a tutte le macchine virtuali nel set chiamando l'aggiornamento in tali VM. Nell'interfaccia della riga di comando il comando da usare è az vmss update-instances. |6 |1.0.1 |
|[Abilita Monitoraggio di Azure per le macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Abilitare Monitoraggio di Azure per le macchine virtuali (VM) nell'ambito specificato (gruppo di gestione, sottoscrizione o gruppo di risorse). Accetta l'area di lavoro Log Analytics come parametro. |10 |2.0.0 |
