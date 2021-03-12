---
title: Configurare le chiavi gestite dal cliente per l'API di Azure per FHIR
description: La funzionalità Bring Your Own Key (BYOK) è supportata nell'API di Azure per FHIR tramite Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: 08b5f63f1fffc2369eff620ca1937f298c2d9ca5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019209"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configurare chiavi gestite dal cliente inattive

Quando si crea un nuovo account dell'API di Azure per FHIR, per impostazione predefinita i dati vengono crittografati usando chiavi gestite da Microsoft. A questo punto, è possibile aggiungere un secondo livello di crittografia per i dati usando una chiave personalizzata che si sceglie e si gestisce in autonomia.

In Azure questa operazione viene in genere eseguita usando una chiave di crittografia nel Azure Key Vault del cliente. SQL di Azure, Archiviazione di Azure e Cosmos DB sono alcuni esempi che attualmente forniscono questa funzionalità. L'API di Azure per FHIR sfrutta questo supporto da Cosmos DB. Quando si crea un account, è possibile specificare un URI chiave Azure Key Vault. Questa chiave verrà passata al Cosmos DB quando viene effettuato il provisioning dell'account di database. Quando viene inviata una richiesta a FHIR, Cosmos DB recupera la chiave e la usa per crittografare/decrittografare i dati. Per iniziare, vedere i collegamenti seguenti:

- [Registrare il provider di risorse Azure Cosmos DB per la sottoscrizione di Azure](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configurare l'istanza di Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Aggiungere un criterio di accesso all'istanza di Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Generare una chiave in Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Uso del portale di Azure

Quando si crea l'account dell'API di Azure per FHIR in portale di Azure, è possibile visualizzare un'opzione di configurazione "crittografia dei dati" in "Impostazioni database" nella scheda "impostazioni aggiuntive". Per impostazione predefinita, verrà scelta l'opzione chiave gestita dal servizio. 

È possibile scegliere la chiave dal tasto di scelta:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Selezione chiavi":::

In alternativa, è possibile specificare la chiave di Azure Key Vault selezionando l'opzione "chiave gestita dal cliente". È possibile immettere l'URI della chiave qui:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Creare l'API di Azure per FHIR":::

Per gli account FHIR esistenti, è possibile visualizzare la scelta relativa alla chiave di crittografia (chiave gestita dal servizio o dal cliente) nel pannello "Database" come illustrato di seguito. Non è possibile modificare l'opzione di configurazione una volta scelta. Sarà tuttavia possibile modificare e aggiornare la chiave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

È inoltre possibile creare una nuova versione della chiave specificata. Successivamente i dati verranno crittografati con la nuova versione senza interruzioni del servizio. Si può anche rimuovere l'accesso alla chiave per rimuovere l'accesso ai dati. Quando la chiave è disabilitata, le query generano un errore. Se la chiave è riabilitata, le query risulteranno riuscite.




## <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Con l'URI della chiave di Azure Key Vault, è possibile configurare CMK tramite PowerShell eseguendo il comando PowerShell seguente:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Come per il metodo PowerShell, è possibile configurare CMK passando l'URI della chiave di Azure Key Vault sotto il `key-vault-key-uri` parametro ed eseguendo il comando CLI seguente: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Uso del modello di Azure Resource Manager

Con l'URI della chiave di Azure Key Vault, è possibile configurare CMK passandolo sotto la proprietà **keyVaultKeyUri** nell'oggetto **Properties** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

Ed è possibile distribuire il modello con lo script di PowerShell seguente:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare le chiavi gestite dal cliente inattive usando portale di Azure, PowerShell, l'interfaccia della riga di comando e Gestione risorse modello. Per altre domande possibili, vedere la sezione relativa alle domande frequenti su Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: come configurare CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)