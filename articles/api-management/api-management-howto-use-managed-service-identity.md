---
title: Usare le identità gestite in Azure API Management | Microsoft Docs
description: Informazioni su come creare identità assegnate dal sistema e assegnate dall'utente in API Management usando portale di Azure, PowerShell e un modello Resource Manager.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40ee196f53af040e4099fb344de5488109ce001b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812247"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Usare le identità gestite in Azure API Management

Questo articolo illustra come creare un'identità gestita per un'istanza di Azure API Management e come accedere ad altre risorse. Un'identità gestita generata da Azure Active Directory (Azure AD) consente all'istanza di API Management di accedere in modo semplice e sicuro ad altre risorse protette Azure AD, ad esempio Azure Key Vault. Azure gestisce questa identità, quindi non è necessario effettuare il provisioning o ruotare i segreti. Per altre informazioni sulle identità gestite, vedere [Informazioni sulle identità gestite per le risorse di Azure.](../active-directory/managed-identities-azure-resources/overview.md)

È possibile concedere due tipi di identità a un'API Management:

- *Un'identità assegnata dal* sistema è associata al servizio e viene eliminata se il servizio viene eliminato. Il servizio può avere una sola identità assegnata dal sistema.
- *Un'identità assegnata dall'utente* è una risorsa di Azure autonoma che può essere assegnata al servizio. Il servizio può avere più identità assegnate dall'utente.

## <a name="create-a-system-assigned-managed-identity"></a>Creare un'identità gestita assegnata dal sistema

### <a name="azure-portal"></a>Portale di Azure

Per configurare un'identità gestita nel portale di Azure, si creerà prima un'istanza API Management e quindi si abiliterà la funzionalità.

1. Creare un'istanza di Gestione API nel portale come di consueto. Passare ad esso nel portale.
2. Selezionare **Identità gestite**.
3. Nella scheda **Assegnato dal** sistema impostare **Stato** su **On**. Selezionare **Salva**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Selezioni per l'abilitazione di un'identità gestita assegnata dal sistema" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La procedura seguente illustra come creare un'istanza API Management e assegnarle un'identità usando Azure PowerShell. 

1. Se necessario, installare Azure PowerShell usando le istruzioni nella guida [Azure PowerShell .](/powershell/azure/install-az-ps) Eseguire quindi `Connect-AzAccount` per creare una connessione con Azure.

2. Usare il codice seguente per creare l'istanza di . Per altri esempi su come usare Azure PowerShell con un'API Management, vedere API Management [di PowerShell.](powershell-samples.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Aggiornare un'istanza esistente per creare l'identità:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

È possibile creare un'istanza di Gestione API con un'identità includendo la proprietà seguente nella definizione della risorsa:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Questa proprietà indica ad Azure di creare e gestire l'identità per l'istanza di Gestione API.

Ad esempio, un modello completo di Azure Resource Manager può essere simile al seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Quando viene creata, l'istanza presenta le proprietà aggiuntive seguenti:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

La `tenantId` proprietà identifica il tenant Azure AD cui appartiene l'identità. La `principalId` proprietà è un identificatore univoco per la nuova identità dell'istanza. In Azure AD, l'entità servizio ha lo stesso nome che è stato dato all'istanza API Management servizio.

> [!NOTE]
> Un API Management istanza può avere contemporaneamente identità assegnate dal sistema e assegnate dall'utente. In questo caso, la `type` proprietà sarà `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Scenari supportati con identità assegnata dal sistema

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Ottenere un certificato TLS/SSL personalizzato per l'istanza API Management da Azure Key Vault
È possibile usare l'identità assegnata dal sistema di un'istanza API Management per recuperare i certificati TLS/SSL personalizzati archiviati in Azure Key Vault. È quindi possibile assegnare questi certificati a domini personalizzati nell'API Management predefinita. Tenere presente quanto segue:

- Il tipo di contenuto del segreto deve *essere application/x-pkcs12.*
- Usare l Key Vault endpoint segreto del certificato, che contiene il segreto.

> [!Important]
> Se non si specifica la versione dell'oggetto del certificato, API Management o ottiene automaticamente la versione più recente del certificato entro quattro ore dall'aggiornamento in Key Vault.

L'esempio seguente mostra un modello di Azure Resource Manager che contiene i passaggi seguenti:

1. Creare un'API Management con un'identità gestita.
2. Aggiornare i criteri di accesso dell'istanza di Azure Key Vault e consentire all'istanza di Gestione API di ottenere i segreti da essa.
3. Aggiornare l'istanza di Gestione API impostando un nome di dominio personalizzato tramite un certificato ottenuto dall'istanza di Key Vault.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Eseguire l'autenticazione al back-end usando un'API Management aziendale

È possibile usare l'identità assegnata dal sistema per eseguire l'autenticazione al [back-end tramite i criteri di identità gestita dall'autenticazione.](api-management-authentication-policies.md#ManagedIdentity)

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Connettersi alle risorse di Azure dietro il firewall IP usando l'identità gestita assegnata dal sistema


API Management è un servizio Microsoft attendibile per le risorse seguenti. In questo modo il servizio può connettersi alle risorse seguenti dietro un firewall. Dopo aver assegnato in modo [](../active-directory/managed-identities-azure-resources/overview.md) esplicito il ruolo di Azure appropriato all'identità gestita assegnata dal sistema per l'istanza della risorsa, l'ambito di accesso per l'istanza corrisponde al ruolo di Azure assegnato all'identità gestita.


|Servizio di Azure | Collegamento|
|---|---|
|Archiviazione di Azure | [Accesso attendibile ad azure-storage](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Bus di servizio di Azure | [Trusted-access-to-azure-service-bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Hub eventi di Azure | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

> [!NOTE]
> È possibile associare un API Management a un massimo di 10 identità gestite assegnate dall'utente.

### <a name="azure-portal"></a>Portale di Azure

Per configurare un'identità gestita nel portale, si creerà prima un'istanza API Management e quindi si abiliterà la funzionalità.

1. Creare un'istanza di Gestione API nel portale come di consueto. Passare ad esso nel portale.
2. Selezionare **Identità gestite**.
3. Nella scheda **Assegnato dall'utente** selezionare **Aggiungi**.
4. Cercare l'identità creata in precedenza e selezionarla. Selezionare **Aggiungi**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Selezioni per l'abilitazione di un'identità gestita assegnata dall'utente" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La procedura seguente illustra come creare un'istanza API Management e assegnarle un'identità usando Azure PowerShell. 

1. Se necessario, installare il Azure PowerShell usando le istruzioni nella guida [Azure PowerShell .](/powershell/azure/install-az-ps) Eseguire quindi `Connect-AzAccount` per creare una connessione con Azure.

2. Usare il codice seguente per creare l'istanza di . Per altri esempi di come usare Azure PowerShell con un'API Management, vedere API Management [esempi di PowerShell.](powershell-samples.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Aggiornare un servizio esistente per assegnare un'identità al servizio:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager

È possibile creare un'istanza di Gestione API con un'identità includendo la proprietà seguente nella definizione della risorsa:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

L'aggiunta del tipo assegnato dall'utente indica ad Azure di usare l'identità assegnata dall'utente specificata per l'istanza.

Ad esempio, un modello completo di Azure Resource Manager può essere simile al seguente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Quando viene creato, il servizio ha le proprietà aggiuntive seguenti:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

La `principalId` proprietà è un identificatore univoco per l'identità usata per Azure AD amministrazione. La proprietà è un identificatore univoco per la nuova identità dell'applicazione usata per specificare `clientId` l'identità da usare durante le chiamate di runtime.

> [!NOTE]
> Un API Management istanza può avere contemporaneamente identità assegnate dal sistema e assegnate dall'utente. In questo caso, la `type` proprietà sarà `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Scenari supportati con l'identità gestita assegnata dall'utente

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Ottenere un certificato TLS/SSL personalizzato per l'istanza API Management da Azure Key Vault
È possibile usare qualsiasi identità assegnata dall'utente per stabilire una relazione di trust tra un'API Management e KeyVault. Questo trust può quindi essere usato per recuperare i certificati TLS/SSL personalizzati archiviati in Azure Key Vault. È quindi possibile assegnare questi certificati a domini personalizzati nell'API Management predefinita. 

Tenere presente quanto segue:

- Il tipo di contenuto del segreto deve *essere application/x-pkcs12.*
- Usare l Key Vault endpoint segreto del certificato, che contiene il segreto.

> [!Important]
> Se non si specifica la versione dell'oggetto del certificato, API Management o ottiene automaticamente la versione più recente del certificato entro quattro ore dall'aggiornamento in Key Vault.

Per il modello completo, vedere l'API Management con SSL basato su [KeyVault usando l'identità assegnata dall'utente.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json)

In questo modello, verrà distribuito quanto segue:

* Gestione API di Azure
* Identità assegnata dall'utente gestito di Azure
* Azure KeyVault per l'archiviazione del certificato SSL/TLS

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuisci in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Eseguire l'autenticazione al back-end usando un'identità assegnata dall'utente

È possibile usare l'identità assegnata dall'utente per eseguire l'autenticazione al [back-end tramite i criteri di identità gestita dall'autenticazione.](api-management-authentication-policies.md#ManagedIdentity)

## <a name="remove-an-identity"></a><a name="remove"></a>Rimuovere un'identità

È possibile rimuovere un'identità assegnata dal sistema disabilitando la funzionalità tramite il portale o il modello Azure Resource Manager nello stesso modo in cui è stata creata. Le identità assegnate dall'utente possono essere rimosse separatamente. Per rimuovere tutte le identità, impostare il tipo di identità su `"None"` .

La rimozione di un'identità assegnata dal sistema in questo modo ne comporta l'eliminazione anche da Azure AD. Anche le identità assegnate dal sistema vengono rimosse automaticamente Azure AD quando viene eliminata API Management'istanza di .

Per rimuovere tutte le identità usando il modello Azure Resource Manager, aggiornare questa sezione:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Se un'API Management è configurata con un certificato SSL personalizzato da Key Vault e si tenta di disabilitare un'identità gestita, la richiesta avrà esito negativo.
>
> È possibile sbloccarsi passando da un certificato Azure Key Vault a un certificato codificato inline e quindi disabilitando l'identità gestita. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato](configure-custom-domain.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle identità gestite per le risorse di Azure:

* [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelli di Gestione risorse di Azure](https://github.com/Azure/azure-quickstart-templates)
* [Eseguire l'autenticazione con un'identità gestita in un criterio](./api-management-authentication-policies.md#ManagedIdentity)
