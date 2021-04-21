---
title: Creare un ruolo Azure Resource Manager personalizzato e assegnarlo all'entità servizio - Azure
description: Questo articolo fornisce indicazioni su come creare un ruolo di Azure Resource Manager personalizzato e assegnarlo all'entità servizio per Analisi video live in IoT Edge'interfaccia della riga di comando di Azure.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 6c33f6703522fc0b28237e22c16c96587467df40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788512"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Creare un ruolo Azure Resource Manager personalizzato e assegnarlo all'entità servizio

Analisi video live'istanza IoT Edge modulo deve avere un account Servizi multimediali di Azure attivo per il corretto funzionamento. La relazione tra il Analisi video live nel modulo IoT Edge e l'account di Servizio multimediale di Azure viene stabilita tramite un set di proprietà del modulo gemello. Una di queste proprietà gemelle è [un'entità servizio](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) che consente all'istanza del modulo di comunicare con e attivare le operazioni necessarie nell'account di Servizi multimediali. Per ridurre al minimo il potenziale uso improprio e/o l'esposizione accidentale dei dati dal dispositivo perimetrale, questa entità servizio deve avere il minor numero di privilegi.

Questo articolo illustra i passaggi per la creazione di un ruolo Azure Resource Manager personalizzato con Azure Cloud Shell, che viene quindi usato per creare un'entità servizio.

## <a name="prerequisites"></a>Prerequisiti  

I prerequisiti per questo articolo sono i seguenti:

* Sottoscrizione di Azure con sottoscrizione del proprietario.
* Un Azure Active Directory con privilegi per creare un'app e assegnare un'entità servizio a un ruolo.

Il modo più semplice per verificare se l'account dispone delle autorizzazioni appropriate è tramite il portale. Vedere [Controllare le autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Panoramica  

Verranno descritti i passaggi per creare un ruolo personalizzato e collegarlo a un'entità servizio nell'ordine seguente:

1. Creare un account di Servizio multimediale, se non è già presente.
1. Creare un'entità servizio.
1. Creare un ruolo Azure Resource Manager con privilegi limitati.
1. "Limitare" i privilegi dell'entità servizio usando il ruolo personalizzato creato.
1. Eseguire un test semplice per verificare se è possibile limitare correttamente l'entità servizio.
1. Acquisire i parametri che verranno usati nei manifesti IoT Edge distribuzione.

### <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali  

Se non si ha un account di Servizio multimediale, seguire questa procedura per crearne uno.

1. Passare al [Cloud Shell](https://shell.azure.com/).
1. Selezionare "Bash" come ambiente nell'elenco a discesa sul lato sinistro della finestra della shell

    ![L'estremità dello schermo mostra Bash selezionato dalla finestra della shell.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Impostare la sottoscrizione di Azure come account predefinito usando il modello di comando seguente:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Creare un [gruppo di risorse e](/cli/azure/group#az_group_create) un account di [archiviazione](/cli/azure/storage/account#az_storage_account_create).
1. Creare ora un account di Servizi multimediali di Azure usando il modello di comando seguente in Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Creare un'entità servizio  

Verrà ora creata una nuova entità servizio che verrà collegata all'account di Servizi multimediali.

Senza parametri di autenticazione, l'autenticazione basata su password viene usata con una password casuale per l'entità servizio. In Cloud Shell usare il modello di comando seguente:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Questo comando genera una risposta simile alla seguente:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. L'output per un'entità servizio con autenticazione della password include la chiave password che in questo caso è il parametro "AadSecret". 

    Assicurarsi di copiare questo valore perché non può essere recuperato. Se si dimentica la password, [reimpostare le credenziali dell'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. L'appId e la chiave del tenant vengono visualizzati nell'output rispettivamente come "AadClientId" e "AadTenantId". Vengono usati nell'autenticazione dell'entità servizio. Registrare i valori, che possono essere tuttavia recuperati in qualsiasi momento con [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list).

### <a name="create-a-custom-role-definition"></a>Creare una definizione di ruolo personalizzata  

Per creare un ruolo personalizzato, seguire questa procedura:

1. Creare un file JSON di definizione del ruolo nel sistema locale e salvare il testo seguente nel file. 
    1. Sostituire < yourSubscriptionId> con l'ID sottoscrizione di Azure
    1. Le uniche azioni consentite per questo ruolo sono:
        * listContainerSas: consente al modulo di elencare gli URL dei contenitori di archiviazione con firme di accesso condiviso per il caricamento e il download del contenuto degli asset.
        * Scrivere asset: consente al modulo di creare o aggiornare qualsiasi asset
        * listEdgePolicies: elenca i criteri applicati al dispositivo perimetrale  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. Dopo la creazione, eseguire il modello di comando seguente per creare la nuova definizione di ruolo nella sottoscrizione:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    Al termine dell'esecuzione del comando, verrà visualizzato l'output seguente:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Crea assegnazione ruolo  

Per aggiungere un'assegnazione di ruolo, è necessario l'objectId dell'entità servizio a cui si vuole assegnare il ruolo personalizzato appena creato.

Usare il comando seguente in Cloud Shell per ottenere objectId:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` può essere recuperato dall'output del passaggio [Crea entità](#create-service-principal) servizio.

Il comando precedente stamperà l'objectId dell'entità servizio. 

```
“objectId” : “<yourObjectId>”,
```

Usare [az role assignment create command](/cli/azure/role/assignment#az_role_assignment_create) template per collegare il ruolo personalizzato all'entità servizio:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Parametri

|Parametri|Descrizione| 
|---|---|
|--role |Nome o ID del ruolo personalizzato. In questo caso: "LVAEdge User".|
|--assignee-object-id|ID oggetto dell'entità servizio che verrà utilizzata.|

Il risultato sarà simile al seguente:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Verificare che l'assegnazione di ruolo sia stata verificata

Per verificare che l'entità servizio sia ora collegata al ruolo personalizzato appena creato, eseguire il comando seguente:

```
az role assignment list  --assignee < objectId>
```

Il risultato dovrebbe essere simile al seguente:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Cercare "roleDefinitionName" e verificare che il relativo valore sia impostato su "LVAEdge User". 

Ciò conferma che il ruolo utente personalizzato è stato collegato all'entità servizio usata per l'applicazione.

### <a name="test-the-service-principal-access-control"></a>Testare il controllo di accesso dell'entità servizio

1. Accedere usando l'entità servizio. A questo scopo, saranno necessarie 3 informazioni per il Azure Active Directory per concedere il token di accesso appropriato che è possibile ottenere dall'output del passaggio Crea entità [servizio:](#create-service-principal)
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. A questo punto, provare ad accedere usando il modello di comando seguente:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  A questo punto, è possibile verificare se l'accesso è limitato all'entità servizio con ruolo "LVAEdge User" provando a creare un gruppo di risorse per assicurarsi che non riesca. Eseguire il comando seguente in Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Questo comando avrà esito negativo e avrà un aspetto simile al seguente:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Passaggi successivi  

Si notino i valori seguenti di questo articolo. Questi valori saranno necessari per configurare le proprietà dei dispositivi gemelli del Analisi video live nel modulo IoT Edge, vedere Schema JSON del modulo [gemello.](module-twin-configuration-schema.md)

| Variabile di questo articolo|Nome della proprietà del dispositivo gemello Analisi video live in IoT Edge|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
