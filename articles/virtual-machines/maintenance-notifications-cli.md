---
title: Ottenere notifiche di manutenzione usando l'interfaccia della riga di comando
description: Visualizzare le notifiche di manutenzione per le macchine virtuali in esecuzione in Azure e avviare la manutenzione Self-Service usando l'interfaccia della riga di comando di Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4bd5c8ae7b4f2ba2d057f61712ce799814e19050
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202148"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Gestione delle notifiche di manutenzione pianificata tramite l'interfaccia della riga di comando

**Questo articolo si applica alle macchine virtuali che eseguono sia Linux che Windows.**

È possibile usare l'interfaccia della riga di comando per vedere quando le macchine virtuali sono pianificate per la [manutenzione](maintenance-notifications.md). Le informazioni di manutenzione pianificate sono disponibili in [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view).
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Output
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Avvia manutenzione

La chiamata seguente avvierà la manutenzione in una macchina virtuale se `IsCustomerInitiatedMaintenanceAllowed` è impostato su true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Distribuzioni classiche

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Se si dispone ancora di macchine virtuali legacy distribuite tramite il modello di distribuzione classico, è possibile usare l'interfaccia della riga di comando classica di Azure per eseguire query per le macchine virtuali e avviare la manutenzione.

Verificare che sia attiva la modalità corretta per operare con una VM classica digitando:

```
azure config mode asm
```

Per ottenere lo stato di manutenzione della macchina virtuale denominata *myVM*, digitare:

```
azure vm show myVM 
``` 

Per avviare la manutenzione della macchina virtuale classica denominata *myVM* nel servizio *myService* e nella distribuzione *myDeployment*, digitare:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile gestire la manutenzione pianificata usando il [Azure PowerShell](maintenance-notifications-powershell.md) o il [portale](maintenance-notifications-portal.md).
