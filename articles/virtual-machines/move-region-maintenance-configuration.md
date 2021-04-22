---
title: Spostare una configurazione di manutenzione in un'altra area di Azure
description: Informazioni su come spostare una configurazione di manutenzione di una macchina virtuale in un'altra area di Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: f833f3eb9e3d94da6178a0a9a9cf4f95ec0682e7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865370"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Spostare una configurazione del controllo di manutenzione in un'altra area

Seguire questo articolo per spostare una configurazione di Controllo di manutenzione in un'area di Azure diversa. È possibile spostare una configurazione per diversi motivi. Ad esempio, per sfruttare una nuova area, distribuire funzionalità o servizi disponibili in un'area specifica, soddisfare i requisiti interni di criteri e governance o in risposta alla pianificazione della capacità.

[Il controllo della](maintenance-control.md)manutenzione, con configurazioni di manutenzione personalizzate, consente di controllare il modo in cui gli aggiornamenti della piattaforma vengono applicati alle macchine virtuali e agli host dedicati di Azure. Esistono due scenari per lo spostamento del controllo di manutenzione tra aree:

- Per spostare la configurazione del controllo di manutenzione, ma non le risorse associate alla configurazione, seguire le istruzioni in questo articolo.
- Per spostare le risorse associate a una configurazione di manutenzione, ma non la configurazione stessa, seguire [queste istruzioni.](move-region-maintenance-configuration-resources.md)
- Per spostare sia la configurazione di manutenzione che le risorse associate, seguire prima le istruzioni in questo articolo. Seguire quindi [queste istruzioni.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a spostare una configurazione del controllo di manutenzione:

- Le configurazioni di manutenzione sono associate alle macchine virtuali di Azure o agli host dedicati di Azure. Assicurarsi che le risorse vm/host esistano nella nuova area prima di iniziare.
- Identify: 
    - Configurazioni di controllo della manutenzione esistenti.
    - Gruppi di risorse in cui si trovano attualmente le configurazioni esistenti. 
    - Gruppi di risorse a cui verranno aggiunte le configurazioni dopo lo spostamento nella nuova area. 
    - Risorse associate alla configurazione di manutenzione da spostare.
    - Verificare che le risorse nella nuova area siano uguali a quelle associate alle configurazioni di manutenzione correnti. Le configurazioni possono avere gli stessi nomi nella nuova area di quella precedente, ma questo non è obbligatorio.

## <a name="prepare-and-move"></a>Preparazione e spostamento 

1. Recuperare tutte le configurazioni di manutenzione in ogni sottoscrizione. A tale scopo, eseguire il comando dell'interfaccia della riga di comando [az maintenance configuration list,](/cli/azure/maintenance/configuration#az_maintenance_configuration_list) sostituendo $subId con l'ID sottoscrizione.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Esaminare l'elenco di tabella restituito dei record di configurazione all'interno della sottoscrizione. Ecco un esempio. L'elenco conterrà i valori per l'ambiente specifico.

    **Nome** | **Località** | **Gruppo di risorse**
    --- | --- | ---
    Ignora manutenzione | eastus2 | configuration-resource-group
    IgniteDemoConfig | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | configurazione di test
    

3. Salvare l'elenco come riferimento. Quando si spostano le configurazioni, è possibile verificare che tutto sia stato spostato.
4. Come riferimento, eseguire il mapping di ogni configurazione/gruppo di risorse al nuovo gruppo di risorse nella nuova area.
5. Creare nuove configurazioni di manutenzione nella nuova area usando [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)o l'interfaccia della [riga di comando.](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)
6. Associare le configurazioni alle risorse nella nuova area usando [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)o l'interfaccia della [riga di comando.](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)


## <a name="verify-the-move"></a>Verificare lo spostamento

Dopo aver spostate le configurazioni, confrontare le configurazioni e le risorse nella nuova area con l'elenco di tabelle preparato.


## <a name="clean-up-source-resources"></a>Pulire le risorse di origine

Dopo lo spostamento, provare a eliminare le configurazioni di manutenzione spostate nell'area di origine, [in PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)o nell'interfaccia della [riga di comando.](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)


## <a name="next-steps"></a>Passaggi successivi

Seguire [queste istruzioni se](move-region-maintenance-configuration-resources.md) è necessario spostare le risorse associate alle configurazioni di manutenzione. 
