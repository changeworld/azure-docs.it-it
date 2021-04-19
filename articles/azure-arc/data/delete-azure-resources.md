---
title: Eliminare le risorse in Azure
description: Eliminare le risorse in Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716080"
---
# <a name="delete-resources-from-azure"></a>Eliminare le risorse in Azure

Questo articolo descrive come eliminare risorse da Azure.

> [!WARNING]
> Quando si eliminano risorse come descritto in questo articolo, queste azioni sono irreversibili.

In modalità di connessione indiretta, l'eliminazione di un'istanza da Kubernetes non la rimuoverà da Azure e l'eliminazione di un'istanza da Azure non la rimuoverà da Kubernetes. Per la modalità di connessione indiretta, l'eliminazione di una risorsa è un processo in due passaggi che verrà migliorato in futuro. Kubernetes sarà l'origine della verità e il portale verrà aggiornato per rifletterlo.

In alcuni casi, potrebbe essere necessario eliminare manualmente servizi dati abilitati per Azure Arc risorse in Azure.  È possibile eliminare queste risorse usando una delle opzioni seguenti.

- [Eliminare le risorse in Azure](#delete-resources-from-azure)
  - [Eliminare un intero gruppo di risorse](#delete-an-entire-resource-group)
  - [Eliminare risorse specifiche nel gruppo di risorse](#delete-specific-resources-in-the-resource-group)
  - [Eliminare risorse usando l'interfaccia della riga di comando di Azure](#delete-resources-using-the-azure-cli)
    - [Eliminare le risorse dell'istanza gestita di SQL usando l'interfaccia della riga di comando di Azure](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Eliminare le risorse del gruppo di server PostgreSQL Hyperscale usando l'interfaccia della riga di comando di Azure](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Eliminare Azure Arc di controller dati usando l'interfaccia della riga di comando di Azure](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Eliminare un gruppo di risorse usando l'interfaccia della riga di comando di Azure](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Eliminare un intero gruppo di risorse

Se si usa un gruppo di risorse specifico e dedicato per  servizi dati abilitati per Azure Arc e si vuole eliminare tutti gli elementi all'interno del gruppo di risorse, è possibile eliminare il gruppo di risorse che eliminerà tutti gli elementi al suo interno.  

È possibile eliminare un gruppo di risorse nel portale di Azure eseguendo le operazioni seguenti:

- Passare al gruppo di risorse nel portale di Azure in cui sono state create servizi dati abilitati per Azure Arc risorse.
- Fare clic **sul pulsante Elimina gruppo di** risorse.
- Confermare l'eliminazione immettendo il nome del gruppo di risorse e fare clic su **Elimina**.

## <a name="delete-specific-resources-in-the-resource-group"></a>Eliminare risorse specifiche nel gruppo di risorse

È possibile eliminare risorse servizi dati abilitati per Azure Arc specifiche in un gruppo di risorse nel portale di Azure seguendo questa procedura:

- Passare al gruppo di risorse nel portale di Azure in cui sono state create servizi dati abilitati per Azure Arc risorse.
- Selezionare tutte le risorse da eliminare.
- Fare clic sul pulsante Elimina.
- Confermare l'eliminazione digitando "sì" e fare clic su **Elimina.**

## <a name="delete-resources-using-the-azure-cli"></a>Eliminare risorse usando l'interfaccia della riga di comando di Azure

È possibile eliminare risorse di servizi dati abilitati per Azure Arc specifiche usando l'interfaccia della riga di comando di Azure.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Eliminare le risorse dell'istanza gestita di SQL usando l'interfaccia della riga di comando di Azure

Per eliminare le risorse dell'istanza gestita di SQL da Azure usando l'interfaccia della riga di comando di Azure, sostituire i valori segnaposto nel comando seguente ed eseguirlo.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Eliminare le risorse del gruppo di server PostgreSQL Hyperscale usando l'interfaccia della riga di comando di Azure

Per eliminare una risorsa del gruppo di server PostgreSQL Hyperscale da Azure usando l'interfaccia della riga di comando di Azure, sostituire i valori segnaposto nel comando seguente ed eseguirlo.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Eliminare le Azure Arc del controller dati usando l'interfaccia della riga di comando di Azure

> [!NOTE]
> Prima di eliminare Azure Arc controller dati di , è necessario eliminare tutte le risorse dell'istanza di database gestite.

Per eliminare un controller Azure Arc dati da Azure usando l'interfaccia della riga di comando di Azure, sostituire i valori segnaposto nel comando seguente ed eseguirlo.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Eliminare un gruppo di risorse usando l'interfaccia della riga di comando di Azure

È anche possibile usare l'interfaccia della riga di comando di Azure [per eliminare un gruppo di risorse](../../azure-resource-manager/management/delete-resource-group.md).