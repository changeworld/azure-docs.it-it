---
title: Crittografare i dati della distribuzione
description: Informazioni sulla crittografia dei dati resi persistenti per le risorse dell'istanza di contenitore e su come crittografare i dati con una chiave gestita dal cliente
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 23c81aeab3bf6e9ee7f2d89fbdf8def20dab4aa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790870"
---
# <a name="encrypt-deployment-data"></a>Crittografare i dati della distribuzione

Quando si Istanze di Azure Container risorse di contenitori nel cloud, il servizio ACI raccoglie e rende persistenti i dati correlati ai contenitori. ACI crittografa automaticamente questi dati quando vengono resi persistenti nel cloud. Questa crittografia protegge i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. ACI offre anche la possibilità di crittografare questi dati con la propria chiave, offrendo un maggiore controllo sui dati correlati alle distribuzioni di ACI.

## <a name="about-aci-data-encryption"></a>Informazioni sulla crittografia dei dati di ACI 

I dati in ACI vengono crittografati e decrittografati usando la crittografia AES a 256 bit. È abilitato per tutte le distribuzioni di ACI e non è necessario modificare la distribuzione o i contenitori per sfruttare i vantaggi di questa crittografia. Sono inclusi i metadati relativi alla distribuzione, le variabili di ambiente, le chiavi passate nei contenitori e i log resi persistenti dopo l'arresto dei contenitori, in modo che sia comunque possibile vederli. La crittografia non influisce sulle prestazioni del gruppo di contenitori e non è previsto alcun costo aggiuntivo per la crittografia.

## <a name="encryption-key-management"></a>Gestione delle chiavi di crittografia

È possibile usare chiavi gestite da Microsoft per la crittografia dei dati del contenitore oppure è possibile gestire la crittografia con le proprie chiavi. Nella tabella seguente vengono confrontate queste opzioni: 

|    |    chiavi gestite da Microsoft     |     Chiavi gestite dal cliente     |
|----|----|----|
|    **Operazioni di crittografia/decrittografia**    |    Azure    |    Azure    |
|    **Archiviazione chiavi**    |    Archivio chiavi Microsoft    |    Insieme di credenziali chiave di Azure    |
|    **Responsabilità della rotazione delle chiavi**    |    Microsoft    |    Cliente    |
|    **Accesso alle chiavi**    |    Solo Microsoft    |    Microsoft, Customer    |

Il resto del documento illustra i passaggi necessari per crittografare i dati di distribuzione di ACI con la chiave (chiave gestita dal cliente). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Crittografare i dati con una chiave gestita dal cliente

### <a name="create-service-principal-for-aci"></a>Creare un'entità servizio per ACI

Il primo passaggio consiste nell'assicurarsi che al tenant di [Azure](../active-directory/develop/quickstart-create-new-tenant.md) sia assegnata un'entità servizio per concedere le autorizzazioni al Istanze di Azure Container servizio. 

> [!IMPORTANT]
> Per eseguire il comando seguente e creare correttamente un'entità servizio, verificare di avere le autorizzazioni per creare entità servizio nel tenant.
>

Il comando dell'interfaccia della riga di comando seguente configura ACI SP nell'ambiente Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

L'output dell'esecuzione di questo comando dovrebbe mostrare un'entità servizio che è stata impostata con "displayName": "Servizio istanza di Azure Container".

Se non è possibile creare correttamente l'entità servizio:
* verificare di avere le autorizzazioni per eseguire questa operazione nel tenant
* verificare se nel tenant esiste già un'entità servizio per la distribuzione in ACI. A tale scopo, eseguire e `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` usare l'entità servizio

### <a name="create-a-key-vault-resource"></a>Creare una risorsa Key Vault

Creare un'istanza di Azure Key Vault con il [portale di Azure](../key-vault/general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../key-vault/general/quick-create-cli.md) o [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

Per le proprietà dell'insieme di credenziali delle chiavi, usare le linee guida seguenti: 
* Name: è necessario un nome univoco. 
* Sottoscrizione Scegliere una sottoscrizione.
* In Gruppo di risorse scegliere un gruppo di risorse esistente oppure crearne uno nuovo e immettere il nome di un gruppo di risorse.
* Scegliere un percorso nel menu a discesa Percorso.
* È possibile lasciare le altre opzioni ai valori predefiniti o scegliere in base a requisiti aggiuntivi.

> [!IMPORTANT]
> Quando si usano chiavi gestite dal cliente per crittografare un modello di distribuzione ACI, è consigliabile impostare le due proprietà seguenti nell'insieme di credenziali delle chiavi, Eliminazione software e Non ripulire. Queste proprietà non sono abilitate per impostazione predefinita, ma possono essere abilitate usando PowerShell o l'interfaccia della riga di comando di Azure in un insieme di credenziali delle chiavi nuovo o esistente.

### <a name="generate-a-new-key"></a>Generare una nuova chiave 

Dopo aver creato l'insieme di credenziali delle chiavi, passare alla risorsa in portale di Azure. Nel menu di spostamento a sinistra del pannello delle risorse, in Impostazioni, fare clic su **Chiavi**. Nella visualizzazione per "Chiavi" fare clic su "Genera/Importa" per generare una nuova chiave. Usare qualsiasi nome univoco per questa chiave e qualsiasi altra preferenza in base alle esigenze. 

![Generare una nuova chiave](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Impostare i criteri di accesso

Creare un nuovo criterio di accesso per consentire al servizio ACI di accedere alla chiave.

* Dopo aver generato la chiave, nel pannello delle risorse dell'insieme di credenziali delle chiavi fare clic su **Criteri di accesso** in Impostazioni .
* Nella pagina "Criteri di accesso" per l'insieme di credenziali delle chiavi fare clic **su Aggiungi criteri di accesso.**
* Impostare le autorizzazioni *per le chiavi in* modo da includere le autorizzazioni **Ottieni** e Annulla il **wrapping della** chiave del set di ![ chiavi](./media/container-instances-encrypt-data/set-key-permissions.png)
* Per *Select Principal (Seleziona entità)* selezionare Azure Container Instance **Service (Servizio istanza di Azure Container)**
* Fare **clic su** Aggiungi nella parte inferiore 

I criteri di accesso dovrebbero ora essere visualizzati nei criteri di accesso dell'insieme di credenziali delle chiavi.

![Nuovo criterio di accesso](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Modificare il modello di distribuzione JSON

> [!IMPORTANT]
> La crittografia dei dati di distribuzione con una chiave gestita dal cliente è disponibile nell'ultima versione dell'API (12-01-2019) attualmente in fase di implementazione. Specificare questa versione dell'API nel modello di distribuzione. In caso di problemi, contattare il supporto di Azure.

Dopo aver configurato la chiave dell'insieme di credenziali delle chiavi e i criteri di accesso, aggiungere le proprietà seguenti al modello di distribuzione ACI. Per altre informazioni sulla distribuzione di risorse ACI con un modello, vedere Esercitazione: Distribuire un gruppo multi-contenitore usando un [Resource Manager contenitore.](./container-instances-multi-container-group.md) 
* In `resources` impostare `apiVersion` su `2019-12-01` .
* Nella sezione delle proprietà del gruppo di contenitori del modello di distribuzione aggiungere un oggetto `encryptionProperties` che contiene i valori seguenti:
  * `vaultBaseUrl`: il nome DNS dell'insieme di credenziali delle chiavi è disponibile nel pannello Panoramica della risorsa dell'insieme di credenziali delle chiavi nel portale
  * `keyName`: nome della chiave generata in precedenza
  * `keyVersion`: versione corrente della chiave. A tale scopo, fare clic sulla chiave stessa (in "Chiavi" nella sezione Impostazioni della risorsa dell'insieme di credenziali delle chiavi)
* Nelle proprietà del gruppo di contenitori aggiungere una `sku` proprietà con valore `Standard` . La `sku` proprietà è obbligatoria nella versione API 2019-12-01.

Il frammento di modello seguente mostra queste proprietà aggiuntive per crittografare i dati di distribuzione:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Di seguito è riportato un modello completo, adattato dal modello in Esercitazione: Distribuire un gruppo [multi-contenitore usando](./container-instances-multi-container-group.md)un modello Resource Manager contenitore . 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Distribuire le risorse

Se il file modello è stato creato e modificato sul desktop, è possibile caricarlo nella directory Cloud Shell trascinando il file al suo interno. 

Creare un gruppo di risorse con il comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuire il modello con il [comando az deployment group create.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Entro pochi secondi si dovrebbe ricevere una risposta iniziale da Azure. Al termine della distribuzione, tutti i dati correlati a esso resi persistenti dal servizio ACI verranno crittografati con la chiave specificata.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group/#az_deployment_group_create
