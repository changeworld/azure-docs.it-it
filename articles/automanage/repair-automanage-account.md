---
title: Ripristinare un account Gestione automatica di Azure danneggiato
description: Se di recente è stata spostata una sottoscrizione che contiene un account di gestione automatica in un nuovo tenant, è necessario riconfigurarla. In questo articolo si apprenderà come fare.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e6bf5404a33e0b4e57c2ff8d82d8791eda3d0f06
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834193"
---
# <a name="repair-an-automanage-account"></a>Ripristinare un account di gestione automatica
[L Gestione automatica di Azure account è](./automanage-virtual-machines.md#automanage-account) il contesto di sicurezza o l'identità con cui si verificano le operazioni automatizzate. Se di recente è stata spostata una sottoscrizione che contiene un account di gestione automatica in un nuovo tenant, è necessario riconfigurare l'account. Per riconfigurarlo, è necessario reimpostare il tipo di identità e assegnare i ruoli appropriati per l'account.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Passaggio 1: Reimpostare il tipo di identità Account di gestione automatica
Reimpostare il tipo di identità Account di gestione automatica usando il modello di Azure Resource Manager (ARM) seguente. Salvare il file in locale come armdeploy.jso con un nome simile. Prendere nota del nome e della posizione dell'account di Gestione automatica perché sono parametri obbligatori nel modello di Gestione risorse di Microsoft.

1. Creare una Resource Manager distribuzione usando il modello seguente. Usare `identityType = None`.
    * È possibile creare la distribuzione nell'interfaccia della riga di comando di Azure usando `az deployment sub create` . Per altre informazioni, vedere [az deployment sub](/cli/azure/deployment/sub).
    * È possibile creare la distribuzione in PowerShell usando il `New-AzDeployment` modulo . Per altre informazioni, vedere [New-AzDeployment.](/powershell/module/az.resources/new-azdeployment)

1. Eseguire di nuovo lo stesso modello di Arm con `identityType = SystemAssigned` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Passaggio 2: Assegnare i ruoli appropriati per l'account di gestione automatica
L'account di gestione automatica richiede i ruoli Collaboratore e Collaboratore ai criteri delle risorse nella sottoscrizione che contiene le macchine virtuali che gestione automatica sta gestendo. È possibile assegnare questi ruoli usando l'interfaccia della riga di portale di Azure, i modelli arm o l'interfaccia della riga di comando di Azure.

Se si usa un modello arm o l'interfaccia della riga di comando di Azure, è necessario l'ID entità (noto anche come ID oggetto) dell'account di gestione automatica. L'ID non è necessario se si usa il portale di Azure. È possibile trovare questo ID usando questi metodi:

- [Interfaccia della riga di comando](/cli/azure/ad/sp)di Azure: usare il comando `az ad sp list --display-name <name of your Automanage Account>` .

- portale di Azure: passare a **Azure Active Directory** e cercare l'account di gestione automatica in base al nome. In **Applicazioni aziendali** selezionare il nome dell'account di gestione automatica quando viene visualizzato.

### <a name="azure-portal"></a>Portale di Azure
1. In **Sottoscrizioni** passare alla sottoscrizione che contiene le macchine virtuali con gestione automatica.
1. Passare a **Controllo di accesso (IAM).**
1. Selezionare **Aggiungi assegnazioni di ruolo**.
1. Selezionare il **ruolo Collaboratore** e immettere il nome dell'account di gestione automatica.
1. Selezionare **Salva**.
1. Ripetere i passaggi da 3 a 5, questa volta con il **ruolo Collaboratore criteri** risorse.

### <a name="arm-template"></a>Modello ARM
Eseguire il modello di Arm seguente. Sarà necessario l'ID entità dell'account di gestione automatica. I passaggi per ottenerlo sono all'inizio di questa sezione. Immettere l'ID quando richiesto.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Eseguire questi comandi:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sulle Gestione automatica di Azure](./automanage-virtual-machines.md)
