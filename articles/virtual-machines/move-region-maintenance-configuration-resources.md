---
title: Spostare le risorse associate a una configurazione di manutenzione in un'altra area
description: Informazioni su come spostare le risorse associate a una configurazione di manutenzione della macchina virtuale in un'altra area di Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 4427071edf237d82e8a99d44678d77d23e180fff
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865244"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Spostare le risorse in una configurazione di controllo di manutenzione in un'altra area

Seguire questo articolo per spostare le risorse associate a una configurazione di Controllo di manutenzione in un'area di Azure diversa. Potrebbe essere necessario spostare una configurazione per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area, per distribuire funzionalità o servizi disponibili in un'area specifica, per soddisfare i requisiti interni di criteri e governance o in risposta alla pianificazione della capacità.

[Il controllo della](maintenance-control.md)manutenzione, con configurazioni di manutenzione personalizzate, consente di controllare il modo in cui gli aggiornamenti della piattaforma vengono applicati alle macchine virtuali e agli host dedicati di Azure. Esistono un paio di scenari per lo spostamento del controllo di manutenzione tra aree:

- Per spostare le risorse associate a una configurazione di manutenzione, ma non alla configurazione stessa, seguire questo articolo.
- Per spostare la configurazione del controllo di manutenzione, ma non le risorse associate alla configurazione, seguire [queste istruzioni.](move-region-maintenance-configuration.md)
- Per spostare sia la configurazione di manutenzione che le risorse associate, seguire prima [queste istruzioni.](move-region-maintenance-configuration.md) Seguire quindi le istruzioni in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a spostare le risorse associate a una configurazione del controllo di manutenzione:

- Assicurarsi che le risorse da spostare esistano nella nuova area prima di iniziare.
- Verificare le configurazioni di Controllo di manutenzione associate alle macchine virtuali di Azure e agli host dedicati di Azure da spostare. Controllare ogni risorsa singolarmente. Attualmente non è possibile recuperare le configurazioni per più risorse.
- Quando si recuperano le configurazioni per una risorsa:
    - Assicurarsi di usare l'ID sottoscrizione per l'account, non un ID host dedicato di Azure sottoscrizione.
    - Interfaccia della riga di comando: il parametro --output table viene usato solo per la leggibilità e può essere eliminato o modificato.
    - PowerShell: il Format-Table name viene usato solo per la leggibilità e può essere eliminato o modificato.
    - Se si usa PowerShell, viene visualizzato un errore se si tenta di elencare le configurazioni per una risorsa a cui non sono associate configurazioni. L'errore sarà simile al seguente: "Operazione non riuscita con stato: 'Non trovato'. Dettagli: 404 Errore client: Non trovato per url".

    
## <a name="prepare-to-move"></a>Preparare lo spostamento

1. Prima di iniziare, definire queste variabili. È stato fornito un esempio per ognuno di essi.

    **Variabile** | **Dettagli** | **Esempio**
    --- | ---
    $subId | ID per la sottoscrizione contenente le configurazioni di manutenzione | "our-subscription-ID"
    $rsrcGroupName | Nome gruppo di risorse (macchina virtuale di Azure) | "VMResourceGroup"
    $vmName | Nome risorsa macchina virtuale |  "myVM"
    $adhRsrcGroupName |  Gruppo di risorse (host dedicati) | "HostResourceGroup"
    $adh | Nome host dedicato | "myHost"
    $adhParentName | Nome risorsa padre | "HostGroup"
    
2. Per recuperare le configurazioni di manutenzione usando il [comando Get-AZConfigurationAssignment di](/powershell/module/az.maintenance/get-azconfigurationassignment) PowerShell:

    - Per gli host dedicati di Azure, eseguire:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Per le macchine virtuali di Azure, eseguire:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Per recuperare le configurazioni di manutenzione usando il comando [az maintenance assignment](/cli/azure/maintenance/assignment) dell'interfaccia della riga di comando:

    - Per gli host dedicati di Azure:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Per le macchine virtuali di Azure:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Sposta 

1. [Seguire queste istruzioni](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) per spostare le macchine virtuali di Azure nella nuova area.
2. Dopo aver spostato le risorse, riapplicare le configurazioni di manutenzione alle risorse nella nuova area in base alle esigenze, a seconda che le configurazioni di manutenzione siano state spostate o meno. È possibile applicare una configurazione di manutenzione a una risorsa usando [PowerShell](../virtual-machines/maintenance-control-powershell.md) o l'interfaccia della [riga di comando.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>Verificare lo spostamento

Verificare le risorse nella nuova area e verificare le configurazioni associate per le risorse nella nuova area. 

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Dopo lo spostamento, provare a eliminare le risorse spostate nell'area di origine.


## <a name="next-steps"></a>Passaggi successivi

Se [è necessario spostare](move-region-maintenance-configuration.md) le configurazioni di manutenzione, seguire queste istruzioni. 
