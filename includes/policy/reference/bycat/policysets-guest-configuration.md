---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0cb4e9bb5c65dd32b20c8fc0fc8a45db8856371a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105032712"
---
|Nome |Descrizione |Criteri |Versione |
|---|---|---|---|
|[Controlla i computer con impostazioni di sicurezza delle password non sicure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Questa iniziativa distribuisce i requisiti dei criteri e controlla i computer con impostazioni di sicurezza delle password non sicure. Per altre informazioni sui criteri di configurazione guest, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). |9 |1.0.0 |
|[\[Anteprima \] : distribuire i prerequisiti per abilitare i criteri di configurazione Guest nelle macchine virtuali](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Questa iniziativa aggiunge un'identità gestita assegnata dal sistema e distribuisce l'estensione Configurazione guest appropriata per la piattaforma alle macchine virtuali che sono idonee per il monitoraggio da parte dei criteri di Configurazione guest. Si tratta di un prerequisito per i criteri di Configurazione guest e deve essere assegnato all'ambito di assegnazione dei criteri prima di usare qualsiasi criterio di Configurazione guest. Per altre informazioni su Configurazione guest, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol). |4 |1.0.0-preview |
|[\[Anteprima \] : i computer Windows devono soddisfare i requisiti per la linea di base di sicurezza di Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Questa iniziativa controlla i computer Windows con impostazioni non conformi alla baseline di sicurezza di Azure. Per informazioni dettagliate, vedere [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
