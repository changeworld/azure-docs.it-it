---
title: Gestire gruppi di risorse-interfaccia della riga di comando di Azure
description: Usare l'interfaccia della riga di comando di Azure per gestire i gruppi di risorse tramite Azure Resource Manager. Viene illustrato come creare, elencare ed eliminare gruppi di risorse.
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e28b66844eaa0b73c2654175dea2e31d3cd75f5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172097"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gestire Azure Resource Manager gruppi di risorse usando l'interfaccia della riga di comando di Azure

Informazioni su come usare l'interfaccia della riga di comando di Azure con [Azure Resource Manager](overview.md) per gestire i gruppi di risorse di Azure. Per gestire le risorse di Azure, vedere [gestire le risorse di Azure con l'interfaccia della riga di comando di Azure](manage-resources-cli.md)

Altri articoli sulla gestione dei gruppi di risorse:

- [Gestire i gruppi di risorse di Azure usando il portale di Azure](manage-resources-portal.md)
- [Gestire i gruppi di risorse di Azure usando Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Che cos'è un gruppo di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica una posizione per il gruppo di risorse, si specifica dove vengono archiviati tali metadati.

## <a name="create-resource-groups"></a>Creare gruppi di risorse

Il comando CLI seguente crea un gruppo di risorse.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Elencare i gruppi di risorse

Il seguente script dell'interfaccia della riga di comando elenca i gruppi di risorse nella sottoscrizione.

```azurecli-interactive
az group list
```

Per ottenere un gruppo di risorse:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminare i gruppi di risorse

Il seguente script dell'interfaccia della riga di comando Elimina un gruppo di risorse:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Per altre informazioni su come Azure Resource Manager Ordina l'eliminazione delle risorse, vedere [Azure Resource Manager eliminazione del gruppo di risorse](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuire le risorse in un gruppo di risorse esistente

Vedere [distribuire le risorse in un gruppo di risorse esistente](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuire un gruppo di risorse e le risorse

È possibile creare un gruppo di risorse e distribuire le risorse nel gruppo usando un modello di Gestione risorse. Per altre informazioni, vedere [Creare un gruppo di risorse e distribuire risorse](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Eseguire nuovamente la distribuzione se non è riuscita

Questa funzionalità è nota anche come *rollback in errore*. Per ulteriori informazioni, vedere [ridistribuzione quando la distribuzione ha esito negativo](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Passare a un altro gruppo di risorse o a una sottoscrizione

È possibile spostare le risorse del gruppo in un altro gruppo di risorse. Per altre informazioni, vedere [spostare le risorse](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Blocca gruppi di risorse

Il blocco impedisce ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche, ad esempio la sottoscrizione di Azure, il gruppo di risorse o la risorsa.

Lo script seguente blocca un gruppo di risorse in modo che il gruppo di risorse non possa essere eliminato.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

Lo script seguente ottiene tutti i blocchi per un gruppo di risorse:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

Lo script seguente elimina un blocco:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Per altre informazioni, vedere [bloccare le risorse con Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Contrassegnare i gruppi di risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni, vedere [uso dei tag per organizzare le risorse di Azure](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Esportare gruppi di risorse nei modelli

Dopo aver configurato correttamente il gruppo di risorse, potrebbe essere necessario visualizzare il modello di Gestione risorse per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

- Automatizzare le distribuzioni future della soluzione perché il modello contiene tutte le infrastrutture complete.
- Per informazioni sulla sintassi del modello, vedere il JavaScript Object Notation (JSON) che rappresenta la soluzione.

Per esportare tutte le risorse in un gruppo di risorse, usare il comando [AZ Group Export](/cli/azure/group#az_group_export) e specificare il nome del gruppo di risorse.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

Lo script Visualizza il modello nella console. Copiare il codice JSON e salvarlo come file.

Invece di esportare tutte le risorse nel gruppo di risorse, è possibile selezionare le risorse da esportare.

Per esportare una risorsa, passare l'ID risorsa.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

Per esportare più di una risorsa, passare gli ID di risorsa separati da spazi. Per esportare tutte le risorse, non specificare questo argomento o fornire "*".

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

Quando si esporta il modello, è possibile specificare se i parametri vengono usati nel modello. Per impostazione predefinita, i parametri per i nomi delle risorse sono inclusi ma non hanno un valore predefinito. È necessario passare il valore del parametro durante la distribuzione.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

Nella risorsa viene usato il parametro per il nome.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Se si usa il `--include-parameter-default-value` parametro durante l'esportazione del modello, il parametro di modello include un valore predefinito impostato sul valore corrente. È possibile utilizzare il valore predefinito oppure sovrascrivere il valore predefinito passando un valore diverso.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Se si usa il `--skip-resource-name-params` parametro durante l'esportazione del modello, i parametri per i nomi delle risorse non sono inclusi nel modello. Al contrario, il nome della risorsa viene impostato direttamente sulla risorsa sul valore corrente. Non è possibile personalizzare il nome durante la distribuzione.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

La funzionalità Esporta modello non supporta l'esportazione di risorse Azure Data Factory. Per informazioni su come è possibile esportare Data Factory risorse, vedere [copiare o clonare una data factory in Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Per esportare le risorse create tramite il modello di distribuzione classica, è necessario [eseguirne la migrazione al modello di distribuzione gestione risorse](../../virtual-machines/migration-classic-resource-manager-overview.md).

Per altre informazioni, vedere [esportazione a più risorse e a modello in portale di Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gestire l'accesso ai gruppi di risorse

Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md) è la modalità di gestione dell'accesso alle risorse in Azure. Per altre informazioni, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure tramite l'interfaccia](../../role-based-access-control/role-assignments-cli.md)della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](overview.md).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../templates/template-syntax.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](../index.yml).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).