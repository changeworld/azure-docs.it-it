---
title: Gestione automatica di Azure account
description: Informazioni sul funzionamento di un account di gestione automatica e su come crearne uno.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368699"
---
# <a name="automanage-accounts"></a>Gestione automatica degli account

L'account di gestione automatica è l'identità usata dal servizio Gestione automatica per eseguire le operazioni automatizzate.

Nell'esperienza portale di Azure, quando si abilita La gestione automatica nelle macchine virtuali, è disponibile un elenco a discesa Avanzate nel pannello Enable Azure VM best practice (Abilita le procedure consigliate per la macchina virtuale di **Azure)** che consente di assegnare o creare manualmente l'account di gestione automatica.

All'account di gestione automatica verranno  concessi i ruoli **Collaboratore** e Collaboratore criteri risorse alle sottoscrizioni contenenti i computer di cui si è stati esemeti l'onboarding in Gestione automatica. È possibile usare lo stesso account di gestione automatica nei computer in  più sottoscrizioni, che concederà le autorizzazioni Di collaboratore account e Collaboratore criteri **risorse** per tutte le sottoscrizioni.

Se la macchina virtuale è connessa a un'area di lavoro Log Analytics in un'altra sottoscrizione, all'account di gestione automatica verranno concessi sia **Collaboratore** che Collaboratore criteri risorse **anche** in quell'altra sottoscrizione.

Se si abilita La gestione automatica con un nuovo account di gestione automatica, sono necessarie le autorizzazioni seguenti per la **sottoscrizione:** Ruolo proprietario o **Collaboratore** insieme ai ruoli Amministratore **accesso** utenti.

Se si abilita La gestione automatica con un account di gestione automatica esistente, è necessario avere il ruolo **Collaboratore** nel gruppo di risorse contenente le macchine virtuali.

> [!NOTE]
> Quando si disabilitano le procedure consigliate per la gestione automatica, le autorizzazioni dell'account di gestione automatica per tutte le sottoscrizioni associate rimarranno. Rimuovere manualmente le autorizzazioni andando alla pagina IAM della sottoscrizione o eliminare l'account di gestione automatica. L'account di gestione automatica non può essere eliminato se gestisce ancora computer.

## <a name="create-an-automanage-account"></a>Creare un account di gestione automatica
È possibile creare un account di gestione automatica usando il portale o un modello di Gestione arm.

### <a name="portal"></a>Portale
1. Passare al **pannello Gestione automatica** nel portale
1. Fare **clic su Abilita nel computer esistente**
1. In **Avanzate** fare clic su "Crea un nuovo account"
1. Compilare i campi obbligatori e fare clic su **Crea**

### <a name="arm-template"></a>Modello ARM
La creazione di un account di gestione automatica con un modello di Gestione arm richiede 2 passaggi:
1. Creare l'account di gestione automatica
1. Concedere autorizzazioni sufficienti all'account per consentire l'esecuzione di operazioni per l'utente
    1. Per questo passaggio è necessario l'ID oggetto dell'account creato.
        1. I passaggi per trovare i dettagli dell'entità servizio dell'account (incluso l'ID oggetto) sono disponibili [qui.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)
    1. Dopo aver trovato l'entità servizio, copiare **l'ID oggetto**. Salvarlo perché sarà necessario per delegare le autorizzazioni di seguito.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Creare un account di gestione automatica (non concede autorizzazioni)
Per creare un account di Gestione automatica, salvare il modello di Azure Azure Data Azure Come ed eseguire il comando dell'interfaccia della riga `azuredeploy.json` di comando di Azure riportato di seguito. Al termine, passare al secondo modello seguente per delegare autorizzazioni sufficienti all'account.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Concedere le autorizzazioni all'account di gestione automatica
Per concedere autorizzazioni sufficienti all'account di gestione automatica, è necessario eseguire le operazioni seguenti:
1. Salvare il modello arm seguente con nome ed `azuredeploy2.json` eseguire il comando dell'interfaccia della riga di comando di Azure riportato di seguito.
1. Quando richiesto, immettere l'ID oggetto dell'account di Gestione automatica creato e salvato.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su Gestione automatica dei servizi per [Linux](./automanage-linux.md) e [Windows](./automanage-windows-server.md)