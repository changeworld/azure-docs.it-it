---
title: Fare riferimento a una rete virtuale esistente in un modello di set di scalabilità di Azure
description: Informazioni su come aggiungere una rete virtuale in un modello di set di scalabilità di macchine virtuali di Azure esistente
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076667"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Fare riferimento a una rete virtuale esistente in un modello di set di scalabilità di Azure

Questo articolo illustra come modificare il [modello di set di scalabilità di base](virtual-machine-scale-sets-mvss-start.md) per distribuire in una rete virtuale esistente anziché crearne uno nuovo.

## <a name="prerequisites"></a>Prerequisiti

In un articolo precedente è stato creato un [modello di set di scalabilità di base](virtual-machine-scale-sets-mvss-start.md). Sarà necessario il modello precedente in modo che sia possibile modificarlo per creare un modello che distribuisca un set di scalabilità in una rete virtuale esistente.

## <a name="identify-subnet"></a>Identificare la subnet

Prima di tutto aggiungere un parametro `subnetId`. Questa stringa viene passata nella configurazione del set di scalabilità e permette al set di scalabilità di identificare la subnet creata in precedenza in cui distribuire le macchine virtuali. Il formato della stringa deve essere il seguente:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Ad esempio, per distribuire il set di scalabilità in una rete virtuale esistente con nome `myvnet` , subnet `mysubnet` , gruppo di risorse `myrg` e sottoscrizione `00000000-0000-0000-0000-000000000000` , subnetId sarà: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>Elimina risorsa di rete virtuale aggiuntiva

Dal momento che si usa una rete virtuale esistente e non è necessario distribuirne una nuova, eliminare successivamente la risorsa rete virtuale dalla matrice `resources`.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>Rimuovi clausola Dependency

La rete virtuale esiste già prima della distribuzione del modello, pertanto non è necessario specificare una `dependsOn` clausola dal set di scalabilità alla rete virtuale. Individuare le righe seguenti:

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>Parametro di pass subnet

Infine, passare il `subnetId` parametro impostato dall'utente, anziché usare `resourceId` per ottenere l'ID di una VNET nella stessa distribuzione, ovvero il modello di set di scalabilità di base valido.

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
