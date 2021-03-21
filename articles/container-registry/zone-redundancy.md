---
title: Registro con ridondanza della zona per la disponibilità elevata
description: Informazioni sull'abilitazione della ridondanza della zona in Azure Container Registry. Creare un registro contenitori o una replica in una zona di disponibilità di Azure. La ridondanza della zona è una funzionalità del livello di servizio Premium.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: a190ea68f41196fb11c20259b9953f516d6f5370
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203862"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Abilitare la ridondanza della zona in Azure Container Registry per la resilienza e la disponibilità elevata

Oltre alla [replica geografica](container-registry-geo-replication.md), che replica i dati del registro di sistema in una o più aree di Azure per garantire disponibilità e riduzione della latenza per le operazioni internazionali, Azure container Registry supporta la *ridondanza della zona* facoltativa. La [ridondanza della zona](../availability-zones/az-overview.md#availability-zones) garantisce resilienza e disponibilità elevata a un registro di sistema o a una risorsa di replica (replica) in un'area specifica.

Questo articolo illustra come configurare una replica o un registro contenitori con ridondanza della zona usando l'interfaccia della riga di comando di Azure, portale di Azure o Azure Resource Manager modello. 

La ridondanza della zona è una funzionalità di **Anteprima** del livello di servizio del registro contenitori Premium. Per informazioni sui livelli di servizio e sui limiti del registro contenitori, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md).

## <a name="preview-limitations"></a>Limiti di anteprima

* Attualmente supportato nelle aree seguenti: Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale, Giappone orientale.
* Le conversioni dell'area nelle zone di disponibilità non sono attualmente supportate. Per abilitare il supporto per la zona di disponibilità in un'area, è necessario che il registro di sistema venga creato nell'area desiderata, che il supporto per la zona di disponibilità sia abilitato oppure che sia necessario aggiungere un'area replicata con il supporto della zona di disponibilità abilitato.
* La ridondanza della zona non può essere disabilitata in un'area.
* Le [attività ACR](container-registry-tasks-overview.md) non supportano ancora le zone di disponibilità.

## <a name="about-zone-redundancy"></a>Informazioni sulla ridondanza della zona

Usare le [zone di disponibilità](../availability-zones/az-overview.md) di Azure per creare un registro contenitori di Azure resiliente e a disponibilità elevata in un'area di Azure. Ad esempio, le organizzazioni possono configurare un registro contenitori di Azure con ridondanza della zona con altre [risorse di Azure supportate](../availability-zones/az-region.md) per soddisfare la residenza dei dati o altri requisiti di conformità, garantendo al tempo stesso una disponibilità elevata all'interno di un'area.

Azure Container Registry supporta anche la [replica geografica](container-registry-geo-replication.md), che replica il servizio in più aree, consentendo ridondanza e località per il calcolo delle risorse in altre posizioni. La combinazione di zone di disponibilità per la ridondanza all'interno di un'area e la replica geografica in più aree, migliora l'affidabilità e le prestazioni di un registro.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. Ogni zona ha uno o più data center dotati di alimentazione, raffreddamento e rete indipendenti. Quando è configurato per la ridondanza della zona, un registro di sistema (o una replica del registro di sistema in un'area diversa) viene replicato in tutte le zone di disponibilità nell'area, tenendolo disponibile in caso di errori del Data Center.

## <a name="create-a-zone-redundant-registry---cli"></a>Creare un registro di sistema con ridondanza della zona-CLI

Per usare l'interfaccia della riga di comando di Azure per abilitare la ridondanza della zona, è necessaria la versione 2.17.0 o Azure Cloud Shell successiva dell'interfaccia della riga di comando di Azure Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il comando [AZ Group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse per il registro di sistema.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Crea registro di sistema abilitato per la zona

Eseguire il comando [AZ ACR create](/cli/azure/acr#az_acr_create) per creare un registro con ridondanza della zona nel livello di servizio Premium. Scegliere un'area che [supporta le zone di disponibilità](../availability-zones/az-region.md) per container Registry di Azure. Nell'esempio seguente la ridondanza della zona è abilitata nell'area *eastus* . `az acr create`Per altre opzioni del registro di sistema, vedere la guida del comando.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Nell'output del comando prendere nota della `zoneRedundancy` proprietà per il registro di sistema. Se abilitata, il registro di sistema è con ridondanza della zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Creazione della replica con ridondanza della zona

Eseguire il comando [AZ ACR Replication create](/cli/azure/acr/replication#az_acr_replication_create) per creare una replica del registro di sistema con ridondanza della zona in un'area che [supporta le zone di disponibilità](../availability-zones/az-region.md) per container Registry di Azure, ad esempio *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Nell'output del comando prendere nota della `zoneRedundancy` proprietà per la replica. Se abilitata, la replica è con ridondanza della zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Creare un registro di sistema con ridondanza della zona-portale

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
1. Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.
1. Nella scheda **nozioni di base** selezionare o creare un gruppo di risorse e immettere un nome univoco per il registro di sistema. 
1. In **località** selezionare un'area che supporta la ridondanza della zona per container Registry di Azure, ad esempio *Stati Uniti orientali*.
1. In **SKU** selezionare **Premium**.
1. In **zone di disponibilità** selezionare **abilitato**.
1. Facoltativamente, configurare impostazioni aggiuntive del registro di sistema e quindi selezionare **Verifica + crea**.
1. Selezionare **Crea** per distribuire l'istanza del registro.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Abilitare la ridondanza della zona nel portale di Azure":::

Per creare una replica con ridondanza della zona:

1. Passare al registro contenitori del livello Premium e selezionare **repliche**.
1. Nella mappa visualizzata selezionare un esagono verde in un'area che supporta la ridondanza della zona per Container Registry di Azure, ad esempio **Stati Uniti occidentali 2**. Oppure selezionare **+ Aggiungi**.
1. Nella finestra **Crea replica** confermare il **percorso**. In **zone di disponibilità** selezionare **abilitato** e quindi fare clic su **Crea**.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Abilitare la replica con ridondanza della zona in portale di Azure":::

## <a name="create-a-zone-redundant-registry---template"></a>Creare un modello di registro con ridondanza della zona

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il comando [AZ Group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse per il registro di sistema in un'area che [supporta le zone di disponibilità](../availability-zones/az-region.md) per Azure container Registry, ad esempio *eastus*. Questa area viene utilizzata dal modello per impostare il percorso del registro di sistema.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Distribuire il modello 

È possibile usare il modello di Gestione risorse seguente per creare un registro con replica geografica e con ridondanza della zona. Per impostazione predefinita, il modello consente la ridondanza della zona nel registro di sistema e una replica a livello di area. 

Copiare il contenuto seguente in un nuovo file e salvarlo usando un nome file, ad esempio `registryZone.json`.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Eseguire il comando [AZ Deployment Group create](/cli/azure/group/deployment#az_group_deployment_create) seguente per creare il registro di sistema usando il file di modello precedente. Dove indicato, fornire:

* nome univoco del registro di sistema o distribuzione del modello senza parametri. verrà creato un nome univoco per l'utente
* un percorso per la replica che supporta le zone di disponibilità, ad esempio *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Nell'output del comando prendere nota della `zoneRedundancy` proprietà per il registro di sistema e la replica. Se abilitata, ogni risorsa è con ridondanza della zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [aree che supportano le zone di disponibilità](../availability-zones/az-region.md).
* Scopri di più sulla compilazione per l' [affidabilità](/azure/architecture/framework/resiliency/overview) in Azure.
