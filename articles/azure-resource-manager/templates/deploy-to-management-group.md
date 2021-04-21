---
title: Distribuire le risorse nel gruppo di gestione
description: Viene descritto come distribuire le risorse nell'ambito del gruppo di gestione in un Azure Resource Manager distribuzione.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 74e00921a1170a7750f4a2d239bb778150ac2cae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781938"
---
# <a name="management-group-deployments-with-arm-templates"></a>Distribuzioni di gruppi di gestione con modelli arm

Man mano che l'organizzazione matura, è possibile distribuire un modello di Azure Resource Manager (modello arm) per creare risorse a livello di gruppo di gestione. Ad esempio, potrebbe essere necessario [](../../governance/policy/overview.md) definire e assegnare criteri o il controllo degli accessi in base al ruolo [di Azure per](../../role-based-access-control/overview.md) un gruppo di gestione. Con i modelli a livello di gruppo di gestione, è possibile applicare criteri in modo dichiarativo e assegnare ruoli a livello di gruppo di gestione.

## <a name="supported-resources"></a>Risorse supportate

Non tutti i tipi di risorse possono essere distribuiti a livello di gruppo di gestione. Questa sezione elenca i tipi di risorse supportati.

Per Azure Blueprints, usare:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [Versioni](/azure/templates/microsoft.blueprint/blueprints/versions)

Per Criteri di Azure, usare:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Per il controllo degli accessi in base al ruolo di Azure, usare:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Per i modelli annidati distribuiti in sottoscrizioni o gruppi di risorse, usare:

* [Distribuzioni](/azure/templates/microsoft.resources/deployments)

Per la gestione delle risorse, usare:

* [tag](/azure/templates/microsoft.resources/tags)

I gruppi di gestione sono risorse a livello di tenant. È tuttavia possibile creare gruppi di gestione in una distribuzione del gruppo di gestione impostando l'ambito del nuovo gruppo di gestione sul tenant. Vedere [Gruppo di gestione](#management-group).

## <a name="schema"></a>SCHEMA

Lo schema utilizzato per le distribuzioni dei gruppi di gestione è diverso da quello per le distribuzioni di gruppi di risorse.

Per i modelli, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

Lo schema per un file di parametri è lo stesso per tutti gli ambiti di distribuzione. Per i file di parametri, usare:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Comandi di distribuzione

Per eseguire la distribuzione in un gruppo di gestione, usare i comandi di distribuzione del gruppo di gestione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'interfaccia della riga di comando di Azure, [usare az deployment mg create:](/cli/azure/deployment/mg#az_deployment_mg_create)

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per Azure PowerShell, usare [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Per informazioni più dettagliate sui comandi di distribuzione e sulle opzioni per la distribuzione di modelli arm, vedere:

* [Distribuire risorse con modelli e portale di Azure](deploy-portal.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](deploy-cli.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](deploy-powershell.md)
* [Distribuire risorse con i modelli arm e Azure Resource Manager'API REST](deploy-rest.md)
* [Usare un pulsante di distribuzione per distribuire modelli dal repository GitHub](deploy-to-azure-button.md)
* [Distribuire modelli di Arm da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Percorso e nome della distribuzione

Per le distribuzioni a livello di gruppo di gestione, è necessario specificare un percorso per la distribuzione. Il percorso di distribuzione è separato dal percorso delle risorse distribuite e specifica dove archiviare i dati di distribuzione. [Anche le](deploy-to-subscription.md) [distribuzioni](deploy-to-tenant.md) di sottoscrizioni e tenant richiedono una posizione. Per [le distribuzioni](deploy-to-resource-group.md) di gruppi di risorse, il percorso del gruppo di risorse viene usato per archiviare i dati di distribuzione.

È possibile specificare un nome per la distribuzione oppure usare il nome predefinito. Il nome predefinito è il nome del file modello. Ad esempio, la distribuzione di un modello denominato _azuredeploy.json_ crea un nome di distribuzione predefinito di **azuredeploy**.

Per ogni nome di distribuzione il percorso non è modificabile. Non è possibile creare una distribuzione in un percorso se esiste una distribuzione con lo stesso nome in un percorso diverso. Ad esempio, se si crea una distribuzione del gruppo di gestione con il nome **deployment1** in **centralus**, non sarà più possibile creare un'altra distribuzione con il nome **deployment1** ma con un percorso **westus**. Se viene visualizzato il codice di errore `InvalidDeploymentLocation`, utilizzare un nome diverso o lo stesso percorso come la distribuzione precedente per tale nome.

## <a name="deployment-scopes"></a>Ambiti di distribuzione

Quando si esegue la distribuzione in un gruppo di gestione, è possibile distribuire le risorse in:

* gruppo di gestione di destinazione dall'operazione
* un altro gruppo di gestione nel tenant
* sottoscrizioni nel gruppo di gestione
* gruppi di risorse nel gruppo di gestione
* tenant per il gruppo di risorse

[L'ambito di](scope-extension-resources.md) una risorsa di estensione può essere una destinazione diversa dalla destinazione di distribuzione.

L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Questa sezione illustra come specificare ambiti diversi. È possibile combinare questi ambiti diversi in un singolo modello.

### <a name="scope-to-target-management-group"></a>Ambito per il gruppo di gestione di destinazione

Le risorse definite nella sezione resources del modello vengono applicate al gruppo di gestione dal comando di distribuzione.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Ambito di un altro gruppo di gestione

Per impostare come destinazione un altro gruppo di gestione, aggiungere una distribuzione annidata e specificare la `scope` proprietà . Impostare la `scope` proprietà su un valore nel formato `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Ambito della sottoscrizione

È anche possibile specificare come destinazione le sottoscrizioni all'interno di un gruppo di gestione. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per impostare come destinazione una sottoscrizione all'interno del gruppo di gestione, usare una distribuzione annidata e la `subscriptionId` proprietà .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Ambito del gruppo di risorse

È anche possibile specificare come destinazione i gruppi di risorse all'interno del gruppo di gestione. L'utente che distribuisce il modello deve avere accesso all'ambito specificato.

Per impostare come destinazione un gruppo di risorse all'interno del gruppo di gestione, usare una distribuzione annidata. Impostare le proprietà `subscriptionId` e `resourceGroup`. Non impostare un percorso per la distribuzione annidata perché viene distribuito nel percorso del gruppo di risorse.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Per usare una distribuzione del gruppo di gestione per la creazione di un gruppo di risorse all'interno di una sottoscrizione e la distribuzione di un account di archiviazione in tale gruppo di risorse, vedere Distribuire nella sottoscrizione e nel gruppo [di risorse.](#deploy-to-subscription-and-resource-group)

### <a name="scope-to-tenant"></a>Ambito del tenant

Per creare risorse nel tenant, impostare `scope` su `/` . L'utente che distribuisce il modello deve avere [l'accesso necessario per la distribuzione nel tenant](deploy-to-tenant.md#required-access).

Per utilizzare una distribuzione annidata, impostare `scope` e `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

In caso contrario, è possibile impostare l'ambito `/` su per alcuni tipi di risorse, ad esempio i gruppi di gestione. La creazione di un nuovo gruppo di gestione è descritta nella sezione successiva.

## <a name="management-group"></a>Gruppo di gestione

Per creare un gruppo di gestione in una distribuzione del gruppo di gestione, è necessario impostare l'ambito su `/` per il gruppo di gestione.

Nell'esempio seguente viene creato un nuovo gruppo di gestione nel gruppo di gestione radice.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

Nell'esempio seguente viene creato un nuovo gruppo di gestione nel gruppo di gestione specificato come padre. Si noti che l'ambito è impostato su `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>Sottoscrizioni

Per usare un modello arm per creare una nuova sottoscrizione di Azure in un gruppo di gestione, vedere:

* [Creare sottoscrizioni di Azure Contratto Enterprise a livello di codice](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Creare sottoscrizioni di Azure a livello di codice per un Contratto del cliente Microsoft](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Creare sottoscrizioni di Azure a livello di codice per un Contratto Microsoft Partner](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Per distribuire un modello che sposta una sottoscrizione di Azure esistente in un nuovo gruppo di gestione, vedere [Spostare sottoscrizioni nel modello di Azure Azure](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)

## <a name="azure-policy"></a>Criteri di Azure

Le definizioni dei criteri personalizzati distribuite nel gruppo di gestione sono estensioni del gruppo di gestione. Per ottenere l'ID di una definizione di criteri personalizzata, usare la [funzione extensionResourceId().](template-functions-resource.md#extensionresourceid) Le definizioni dei criteri predefiniti sono risorse a livello di tenant. Per ottenere l'ID di una definizione di criteri predefinita, usare la [funzione tenantResourceId().](template-functions-resource.md#tenantresourceid)

L'esempio seguente illustra come [definire un](../../governance/policy/concepts/definition-structure.md) criterio a livello di gruppo di gestione e assegnarlo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Distribuire nella sottoscrizione e nel gruppo di risorse

Da una distribuzione a livello di gruppo di gestione, è possibile scegliere come destinazione una sottoscrizione all'interno del gruppo di gestione. L'esempio seguente crea un gruppo di risorse all'interno di una sottoscrizione e distribuisce un account di archiviazione in tale gruppo di risorse.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'assegnazione di ruoli, vedere [Aggiungere assegnazioni di ruolo di Azure Azure Resource Manager modelli.](../../role-based-access-control/role-assignments-template.md)
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* È anche possibile distribuire modelli a livello [di sottoscrizione e](deploy-to-subscription.md) a livello di [tenant.](deploy-to-tenant.md)
