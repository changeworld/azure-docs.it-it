---
title: Abilitare VM Insights usando modelli di Gestione risorse
description: Questo articolo descrive come abilitare VM Insights per una o più macchine virtuali di Azure o set di scalabilità di macchine virtuali usando Azure PowerShell o modelli Azure Resource Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031871"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>Abilitare VM Insights usando modelli di Gestione risorse
Questo articolo descrive come abilitare VM Insights per una macchina virtuale o un set di scalabilità di macchine virtuali usando modelli di Gestione risorse. Questa procedura può essere utilizzata per gli elementi seguenti:

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchina virtuale ibrida connessa ad Azure Arc

## <a name="prerequisites"></a>Prerequisiti

- [Creare e configurare un'area di lavoro log Analytics](./vminsights-configure-workspace.md). 
- Vedere [sistemi operativi supportati](./vminsights-enable-overview.md#supported-operating-systems) per assicurarsi che il sistema operativo della macchina virtuale o del set di scalabilità di macchine virtuali che si sta abilitando sia supportato. 

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Sono stati creati esempi Azure Resource Manager modelli per l'onboarding delle macchine virtuali e dei set di scalabilità di macchine virtuali. Questi modelli includono scenari che è possibile usare per abilitare il monitoraggio in una risorsa esistente e creare una nuova risorsa con monitoraggio abilitato.

>[!NOTE]
>Il modello deve essere distribuito nello stesso gruppo di risorse della macchina virtuale o del set di scalabilità di macchine virtuali in fase di abilitazione.


I modelli di Azure Resource Manager sono disponibili in un file di archivio (con estensione zip) che è possibile [scaricare](https://aka.ms/VmInsightsARMTemplates) dal repository GitHub. Contenuto delle cartelle di inclusione di file che rappresentano ogni scenario di distribuzione con un file di modello e di parametri. Prima di eseguirli, modificare il file dei parametri e specificare i valori necessari. 

Il file di download contiene i modelli seguenti per diversi scenari:

- Il modello **ExistingVmOnboarding** Abilita VM Insights se la macchina virtuale esiste già.
- Il modello **NewVmOnboarding** crea una macchina virtuale e consente a VM Insights di monitorarla.
- Il modello **ExistingVmssOnboarding** Abilita VM Insights se il set di scalabilità di macchine virtuali esiste già.
- Il modello **NewVmssOnboarding** Crea set di scalabilità di macchine virtuali e consente a VM Insights di monitorarli.
- Il modello **ConfigureWorkspace** configura l'area di lavoro di log Analytics per supportare VM Insights abilitando le soluzioni e la raccolta dei contatori delle prestazioni del sistema operativo Linux e Windows.

>[!NOTE]
>Se i set di scalabilità di macchine virtuali sono già presenti e i criteri di aggiornamento sono impostati su **manuale**, VM Insights non verrà abilitato per impostazione predefinita dopo l'esecuzione del modello di Azure Resource Manager **ExistingVmssOnboarding** . È necessario aggiornare manualmente le istanze.

## <a name="deploy-templates"></a>Distribuire modelli
I modelli possono essere distribuiti usando [qualsiasi metodo di distribuzione per i modelli di gestione risorse](../../azure-resource-manager/templates/deploy-powershell.md) inclusi gli esempi seguenti con PowerShell e l'interfaccia della riga di comando.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con VM Insights.

- Per visualizzare le dipendenze dell'applicazione individuate, vedere [visualizzare la mappa di VM Insights](vminsights-maps.md).

- Per identificare i colli di bottiglia e l'utilizzo complessivo delle prestazioni della VM, vedere [visualizzare le prestazioni delle VM di Azure](vminsights-performance.md).