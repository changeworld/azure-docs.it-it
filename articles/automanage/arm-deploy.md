---
title: Eseguire l'onboard di un computer Gestione automatica di Azure un modello di Arm
description: Informazioni su come eseguire l'onboard di un computer Gestione automatica di Azure un modello Azure Resource Manager macchina virtuale.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368644"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Eseguire l'onboard di un computer in Automanage con un modello di Azure Resource Manager (ARM)


## <a name="overview"></a>Panoramica
Seguire questa procedura per eseguire l'onboard di un computer in Procedure consigliate per la gestione automatica. Il modello arm seguente creerà un oggetto , ovvero la risorsa di Azure che rappresenta un computer di cui è stato `configurationProfileAssignment` onboarded in Gestione automatica.

## <a name="prerequisites"></a>Prerequisiti
* È necessario aver creato un account di gestione automatica esistente. Per [altre informazioni](./automanage-account.md) sull'account di gestione automatica e su come crearne uno, vedere questo documento.
* È necessario avere il **ruolo Collaboratore** nel gruppo di risorse contenente i computer di cui si vuole eseguire l'onboarding in Gestione automatica

## <a name="arm-template-overview"></a>Panoramica del modello di Arm
Il modello arm seguente consente di onboardare il computer specificato Gestione automatica di Azure procedure consigliate. I dettagli sul modello arm e i passaggi su come eseguire la distribuzione sono disponibili nella sezione Distribuzione del modello di Arm [di seguito.](#arm-template-deployment)
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Distribuzione di modelli di Arm
Il modello arm precedente creerà un'assegnazione del profilo di configurazione per il computer specificato, usando un account di gestione automatica specificato. Se non è stato creato un account di gestione automatica, vedere questo [documento.](./automanage-account.md)

Il `configurationProfileAssignment` valore può essere uno dei valori seguenti:
* "Produzione"
* "DevTest"

Per distribuire il modello di Arm, seguire questa procedura:
1. Salvare il modello di Arm seguente con il nome `azuredeploy.json`
1. Eseguire la distribuzione del modello di Arm con `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Specificare i valori per machineName, automanageAccountName e configurationProfileAssignment quando richiesto
1. L'operazione è stata completata.

Come per qualsiasi modello arm, è possibile eseguire il factoring dei parametri in un file separato e usarlo come argomento `azuredeploy.parameters.json` durante la distribuzione.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla gestione automatica per [Linux](./automanage-linux.md) e [Windows](./automanage-windows-server.md)