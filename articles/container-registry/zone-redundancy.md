---
title: Registro di sistema con ridondanza della zona per la disponibilità elevata
description: Informazioni sull'abilitazione della ridondanza della zona Registro Azure Container. Creare un registro contenitori o una replica in una zona di disponibilità di Azure. La ridondanza della zona è una funzionalità del livello di servizio Premium.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8c1ab42aa505448bd81ff42eba54727b24773c60
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479016"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Abilitare la ridondanza della zona Registro Azure Container per resilienza e disponibilità elevata

Oltre alla [replica](container-registry-geo-replication.md)geografica, che replica i dati del registro in una o più aree di Azure per garantire disponibilità e ridurre la latenza per le operazioni a livello di area, Registro Azure Container supporta la ridondanza della *zona facoltativa.* [La ridondanza della](../availability-zones/az-overview.md#availability-zones) zona offre resilienza e disponibilità elevata per un registro o una risorsa di replica (replica) in un'area specifica.

Questo articolo illustra come configurare un registro contenitori o una replica con ridondanza della zona usando l'interfaccia della riga di comando di Azure, portale di Azure o Azure Resource Manager servizio. 

La ridondanza della zona è **una funzionalità** di anteprima del livello di servizio del registro contenitori Premium. Per informazioni sui livelli di servizio e sui limiti del registro contenitori, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md).

## <a name="preview-limitations"></a>Limiti di anteprima

* Attualmente supportato nelle aree seguenti: Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Europa settentrionale, Europa occidentale, Giappone orientale.
* Le conversioni di aree in zone di disponibilità non sono attualmente supportate. Per abilitare il supporto della zona di disponibilità in un'area, il Registro di sistema deve essere creato nell'area desiderata, con il supporto della zona di disponibilità abilitato oppure è necessario aggiungere un'area replicata con il supporto della zona di disponibilità abilitato.
* La ridondanza della zona non può essere disabilitata in un'area.
* [ACR Tasks](container-registry-tasks-overview.md) non supporta ancora le zone di disponibilità.

## <a name="about-zone-redundancy"></a>Informazioni sulla ridondanza della zona

Usare le zone [di disponibilità di](../availability-zones/az-overview.md) Azure per creare un Registro Azure Container resiliente e a disponibilità elevata all'interno di un'area di Azure. Ad esempio, le organizzazioni possono configurare un Registro Azure Container con ridondanza della zona con altre risorse di [Azure](../availability-zones/az-region.md) supportate per soddisfare la residenza dei dati o altri requisiti di conformità, garantendo al tempo stesso una disponibilità elevata all'interno di un'area.

Registro Azure Container supporta anche la [replica geografica,](container-registry-geo-replication.md)che replica il servizio tra più aree, consentendo la ridondanza e la località per il calcolo delle risorse in altre posizioni. La combinazione di zone di disponibilità per la ridondanza all'interno di un'area e la replica geografica tra più aree migliora sia l'affidabilità che le prestazioni di un registro.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. Ogni zona ha uno o più data center dotati di alimentazione, raffreddamento e rete indipendenti. Se configurata per la ridondanza della zona, un Registro di sistema (o una replica del Registro di sistema in un'area diversa) viene replicato in tutte le zone di disponibilità nell'area, mantenendola disponibile in caso di errori del data center.

## <a name="create-a-zone-redundant-registry---cli"></a>Creare un registro con ridondanza della zona - Interfaccia della riga di comando

Per usare l'interfaccia della riga di comando di Azure per abilitare la ridondanza della zona, è necessaria la versione 2.17.0 o successiva dell'interfaccia della riga di comando di Azure o Azure Cloud Shell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il [comando az group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse per il Registro di sistema.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Creare un Registro di sistema abilitato per la zona

Eseguire il [comando az acr create](/cli/azure/acr#az_acr_create) per creare un registro con ridondanza della zona nel livello di servizio Premium. Scegliere un'area che [supporti le zone di disponibilità](../availability-zones/az-region.md) per Registro Azure Container. Nell'esempio seguente la ridondanza della zona è abilitata *nell'area eastus.* Per altre opzioni `az acr create` del Registro di sistema, vedere la Guida dei comandi.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Nell'output del comando prendere nota della `zoneRedundancy` proprietà per il Registro di sistema. Se abilitata, il Registro di sistema è con ridondanza della zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Creare la replica con ridondanza della zona

Eseguire il [comando az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) per creare una [](../availability-zones/az-region.md) replica del Registro di sistema con ridondanza della zona in un'area che supporta le zone di disponibilità per Registro Azure Container, ad esempio *westus2.* 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Nell'output del comando prendere nota `zoneRedundancy` della proprietà per la replica. Se abilitata, la replica è con ridondanza della zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Creare un registro con ridondanza della zona - Portale

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
1. Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.
1. Nella scheda **Informazioni di base** selezionare o creare un gruppo di risorse e immettere un nome univoco del Registro di sistema. 
1. In **Località** selezionare un'area che supporti la ridondanza della zona per Registro Azure Container, ad esempio *Stati Uniti orientali.*
1. In **SKU** selezionare **Premium**.
1. In **Zone di disponibilità** selezionare **Abilitato.**
1. Facoltativamente, configurare impostazioni aggiuntive del Registro di sistema e quindi selezionare **Rivedi e crea.**
1. Selezionare **Crea** per distribuire l'istanza del registro.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Abilitare la ridondanza della zona portale di Azure":::

Per creare una replica con ridondanza della zona:

1. Passare al registro contenitori di livello Premium e selezionare **Repliche.**
1. Nella mappa visualizzata selezionare un esagono verde in un'area che supporta la ridondanza della zona per Registro Azure Container, ad esempio Stati Uniti occidentali **2.** In altro **modo, selezionare + Aggiungi.**
1. Nella finestra **Crea replica** confermare il valore di **Percorso**. In **Zone di** disponibilità selezionare **Abilitato** e quindi selezionare **Crea.**

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Abilitare la replica con ridondanza della zona in portale di Azure":::

## <a name="create-a-zone-redundant-registry---template"></a>Creare un registro con ridondanza della zona - Modello

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il [comando az group create](/cli/azure/group#az_group_create) per creare [](../availability-zones/az-region.md) un gruppo di risorse per il Registro di sistema in un'area che supporta le zone di disponibilità per Registro Azure Container, ad esempio *eastus.* Questa area viene usata dal modello per impostare il percorso del Registro di sistema.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Distribuire il modello 

È possibile usare il modello di Resource Manager seguente per creare un registro con replica geografica con ridondanza della zona. Per impostazione predefinita, il modello abilita la ridondanza della zona nel Registro di sistema e in una replica a livello di area. 

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

Eseguire il comando [az deployment group create seguente](/cli/azure/group/deployment#az_group_deployment_create) per creare il registro usando il file modello precedente. Dove indicato, specificare:

* un nome univoco del registro o distribuire il modello senza parametri e verrà creato automaticamente un nome univoco
* una posizione per la replica che supporta le zone di disponibilità, ad esempio *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Nell'output del comando prendere nota `zoneRedundancy` della proprietà per il Registro di sistema e la replica. Se abilitata, ogni risorsa è con ridondanza della zona:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [aree che supportano le zone di disponibilità](../availability-zones/az-region.md).
* Altre informazioni sulla creazione per [l'affidabilità](/azure/architecture/framework/resiliency/overview) in Azure.
