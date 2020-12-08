---
title: Aggiungere un'identità gestita a un tipo di nodo del cluster gestito Service Fabric (anteprima)
description: Questo articolo illustra come aggiungere un'identità gestita a un tipo di nodo del cluster gestito Service Fabric
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 00e679b07a44b799b6ac6677201bb59eeddcd6cf
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841469"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Aggiungere un'identità gestita a un tipo di nodo del cluster gestito Service Fabric (anteprima)

Ogni tipo di nodo in un cluster gestito da Service Fabric è supportato da un set di scalabilità di macchine virtuali. Per consentire l'utilizzo di identità gestite con un tipo di nodo cluster gestito, `vmManagedIdentity` è stata aggiunta una proprietà alle definizioni dei tipi di nodo contenenti un elenco di identità che possono essere utilizzate `userAssignedIdentities` . Il mirroring delle funzionalità consente di usare le identità gestite nei cluster non gestiti, ad esempio usando un'identità gestita con l' [estensione del set di scalabilità di macchine virtuali Azure Key Vault](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows).


Per un esempio di distribuzione di un cluster gestito Service Fabric che usa l'identità gestita in un tipo di nodo, vedere [questo modello](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). Per un elenco delle aree supportate, vedere [domande frequenti sul cluster gestito](https://docs.microsoft.com/azure/service-fabric/faq-managed-cluster#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Per questa funzionalità sono attualmente supportate solo le identità assegnate dall'utente.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

* Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Se si prevede di usare PowerShell, [installare](https://docs.microsoft.com/cli/azure/install-azure-cli) l'interfaccia della riga di comando di Azure per eseguire i comandi di riferimento cli.

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente 

È possibile definire un'identità gestita assegnata dall'utente nella sezione Resources di un modello di Azure Resource Manager (ARM) per la creazione durante la distribuzione:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

o creato tramite PowerShell:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Aggiungere un'assegnazione di ruolo con Service Fabric provider di risorse

Aggiungere un'assegnazione di ruolo all'identità gestita con l'applicazione del provider di risorse Service Fabric. Questa assegnazione consente Service Fabric provider di risorse di assegnare l'identità al set di scalabilità di macchine virtuali del cluster gestito. 

Se applicabile, è necessario utilizzare i valori seguenti:

|Nome|Valore del provider di risorse Service Fabric corrispondente|
|----|-------------------------------------|
|ID applicazione|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|ID dell'oggetto.|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Nome definizione ruolo|ID di definizione del ruolo|
|----|-------------------------------------|
|Managed Identity Operator (Operatore per identità gestita)|f1a07417-d97a-45cb-824c-7a7467783830
|



Questa assegnazione di ruolo può essere definita nella sezione Resources utilizzando l'ID oggetto e l'ID di definizione del ruolo:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

o creato tramite PowerShell usando l'ID applicazione e l'ID di definizione del ruolo:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

ID oggetto e ID di definizione del ruolo:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Aggiungere le proprietà dell'identità gestita alla definizione del tipo di nodo

Infine, aggiungere le `vmManagedIdentity` `userAssignedIdentities` proprietà e alla definizione del tipo di nodo del cluster gestito:

```json

"properties": {
    "isPrimary" : true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher" : "MicrosoftWindowsServer",
    "vmImageOffer" : "WindowsServer",
    "vmImageSku" : "2019-Datacenter",
    "vmImageVersion" : "latest",
    "vmManagedIdentity": {
        "userAssignedIdentities": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
        ]
    }
}
```

Dopo la distribuzione, l'identità gestita creata è stata aggiunta al set di scalabilità di macchine virtuali del tipo di nodo designato e può essere usata come previsto in qualsiasi cluster non gestito.

## <a name="troubleshooting"></a>Risoluzione dei problemi

L'impossibilità di aggiungere correttamente un'assegnazione di ruolo verrà soddisfatta con l'errore seguente durante la distribuzione:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Portale di Azure errore di distribuzione che mostra il client con l'ID oggetto/applicazione di SFRP che non dispone dell'autorizzazione per eseguire attività di gestione delle identità":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire un'app in un cluster gestito di Service Fabric](https://docs.microsoft.com/azure/service-fabric/tutorial-managed-cluster-deploy-app) 
