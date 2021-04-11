---
title: Archiviazione attendibile di servizi multimediali di Azure
description: Questa esercitazione illustra come abilitare l'archiviazione attendibile per servizi multimediali di Azure, usare identità gestite per l'archiviazione attendibile e concedere ai servizi di Azure l'accesso a un account di archiviazione quando si usa un firewall o una VPN.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: f076381333457c9ba2fd4325fa8aa7baad5d8a5b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282357"
---
# <a name="tutorial-media-services-trusted-storage"></a>Esercitazione: archiviazione attendibile di servizi multimediali

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Come abilitare l'archiviazione attendibile per servizi multimediali di Azure
> - Come usare le identità gestite per l'archiviazione attendibile
> - Come concedere ai servizi di Azure l'accesso a un account di archiviazione quando si usa il controllo di accesso alla rete, ad esempio un firewall o una VPN

Con l'API 2020-05-01 è possibile abilitare l'archiviazione attendibile associando un'identità gestita a un account di servizi multimediali.

>[!NOTE]
>L'archiviazione attendibile è disponibile solo nell'API e non è attualmente abilitata nel portale di Azure.

Servizi multimediali può accedere automaticamente all'account di archiviazione usando l'autenticazione di sistema. Servizi multimediali verifica che l'account di servizi multimediali e l'account di archiviazione si trovino nella stessa sottoscrizione. Verifica inoltre che l'utente che aggiunge l'associazione abbia accesso all'account di archiviazione con Azure Resource Manager RBAC.

Tuttavia, se si vuole usare il controllo di accesso alla rete per proteggere l'account di archiviazione e abilitare l'archiviazione attendibile, è necessaria l'autenticazione delle [identità gestite](concept-managed-identities.md) . Consente ai servizi multimediali di accedere all'account di archiviazione configurato con un firewall o una restrizione VNet tramite l'accesso di archiviazione attendibile.

## <a name="overview"></a>Panoramica

> [!IMPORTANT]
> Usare l'API 2020-05-01 per tutte le richieste a servizi multimediali.

Questi sono i passaggi generali per la creazione di archiviazione attendibile per servizi multimediali:

1. Creare un gruppo di risorse.
1. Creare un account di archiviazione.
1. Eseguire il polling dell'account di archiviazione fino a quando non è pronto. Quando l'account di archiviazione è pronto, la richiesta restituirà l'ID dell'entità servizio.
1. Trovare l'ID del ruolo *collaboratore dati BLOB di archiviazione* .
1. Chiamare il provider di autorizzazioni e aggiungere un'assegnazione di ruolo.
1. Aggiornare l'account di servizi multimediali per l'autenticazione all'account di archiviazione usando l'identità gestita.
1. Eliminare le risorse se non si vuole continuare a usarle e addebitarle.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessaria una sottoscrizione di Azure.  Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Ottenere l'ID tenant e l'ID sottoscrizione

Se non si sa come ottenere l'ID tenant e l'ID sottoscrizione, vedere [come trovare l'](setup-azure-tenant-how-to.md) ID tenant e [trovare l'ID tenant](setup-azure-tenant-how-to.md).

### <a name="create-a-service-principal-and-secret"></a>Creare un'entità servizio e un segreto

Se non si sa come creare un'entità servizio e un segreto, vedere [ottenere le credenziali per accedere all'API di servizi multimediali](access-api-howto.md).

## <a name="use-a-rest-client"></a>Usare un client REST

Questo script deve essere usato con un client REST, ad esempio ciò che è disponibile nelle estensioni di Visual Studio Code.  Adattarlo per l'ambiente di sviluppo.

## <a name="set-initial-variables"></a>Imposta variabili iniziali

Questa parte dello script è destinata all'uso in un client REST. Le variabili possono essere utilizzate in modo diverso all'interno dell'ambiente di sviluppo.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Ottenere un token per Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Ottenere un token per la API Graph

Questa parte dello script è destinata all'uso in un client REST. Le variabili possono essere utilizzate in modo diverso all'interno dell'ambiente di sviluppo.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Ottenere i dettagli dell'entità servizio

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Archiviare l'ID dell'entità servizio

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Crea account di archiviazione

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Ottenere lo stato dell'account di archiviazione

L'account di archiviazione richiederà un po' di tempo per fare in modo che questa richiesta esegua il polling dello stato.  Ripetere la richiesta fino a quando l'account di archiviazione non è pronto.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Ottenere i dettagli dell'account di archiviazione

Quando l'account di archiviazione è pronto, ottenere le proprietà dell'account di archiviazione.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Ottenere un token per ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Creare un account di servizi multimediali con un'identità gestita assegnata dal sistema

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Ottenere la definizione del ruolo dati BLOB di archiviazione di archiviazione

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Impostare l'assegnazione del ruolo di archiviazione

L'assegnazione di ruolo indica che l'entità servizio per l'account di servizi multimediali dispone del *collaboratore dati BLOB di archiviazione* del ruolo di archiviazione.  L'operazione potrebbe richiedere alcuni minuti ed è importante attendere o l'account di servizi multimediali non verrà configurato correttamente.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Consentire all'identità gestita di ignorare l'accesso all'account di archiviazione

Questa azione modifica l'accesso dall'identità gestita dal sistema all'identità gestita. In questo modo, l'account di archiviazione può accedere all'account di archiviazione tramite un firewall perché i servizi di Azure possono accedere all'account di archiviazione, indipendentemente dalle regole di accesso IP (ACL).

Anche in questo caso, attendere che il ruolo sia stato assegnato nell'account di archiviazione o che l'account di servizi multimediali non venga configurato in modo corretto.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Aggiornare l'account di servizi multimediali per l'uso dell'identità gestita

Potrebbe essere necessario ritentare questa richiesta alcune volte perché l'assegnazione del ruolo di archiviazione può richiedere alcuni minuti per la propagazione.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Testare l'accesso

Testare l'accesso creando un asset nell'account di archiviazione.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Eliminare le risorse

Se non si vuole mantenere le risorse create e continuare a addebitarle, eliminarle.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Passaggi successivi

[Come creare un asset](asset-create-asset-how-to.md)
