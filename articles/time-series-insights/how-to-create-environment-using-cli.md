---
title: Creare un ambiente Azure Time Series Insights Gen2 usando l'interfaccia della riga di comando di Azure - Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni su come configurare un ambiente in Azure Time Series Insights Gen2 usando l'interfaccia della riga di comando di Azure.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 07e7f21bd706d9f83d2813b0ab491b01fbc53672
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867566"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Creare un ambiente Azure Time Series Insights Gen2 usando l'interfaccia della riga di comando di Azure

Questo documento illustra la creazione di un nuovo Time Series Insights Gen2.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisiti

* Creare un account di archiviazione di Azure per l'[archiviazione offline sicura](./concepts-storage.md#cold-store) dell'ambiente. Questo account è progettato per la conservazione a lungo termine e l'analisi dei dati cronologici.

> [!NOTE]
> Nel codice, sostituire `mytsicoldstore` con un nome univoco per l'account di archiviazione offline sicura.

Creare prima di tutto l'account di archiviazione:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Creazione dell'ambiente

Ora che l'account di archiviazione è stato creato e il nome e la chiave di gestione sono stati assegnati alle variabili, eseguire il comando seguente per creare l'Azure Time Series Insights ambiente:

> [!NOTE]
> Nel codice sostituire quanto segue con nomi univoci per lo scenario:
>
> * `my-tsi-env` con il nome dell'ambiente.
> * `my-ts-id-prop` con il nome della proprietà ID della serie temporale.

> [!IMPORTANT]
> L'ID serie temporale dell'ambiente è simile a una chiave di partizione del database. L'ID serie temporale funge anche da chiave primaria per il modello Time Series.
>
> Per altre informazioni, vedere [Procedure consigliate per la scelta di un ID serie temporale.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Rimuovere un Azure Time Series Insights di lavoro

È possibile usare l'interfaccia della riga di comando di Azure per eliminare una singola risorsa, ad esempio un ambiente Time Series Insights, oppure eliminare un gruppo di risorse e tutte le relative risorse, inclusi tutti gli Time Series Insights virtuali.

Per [eliminare un Time Series Insights,](/cli/azure/tsi/environment#az_tsi_environment_delete)eseguire il comando seguente:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Per [eliminare l'account di](/cli/azure/storage/account#az_storage_account_delete)archiviazione, eseguire il comando seguente:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Per [eliminare un gruppo di risorse](/cli/azure/group#az_group_delete) e tutte le relative risorse, eseguire il comando seguente:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [origini eventi di inserimento in streaming](./concepts-streaming-ingestion-event-sources.md) per l'Azure Time Series Insights Gen2.
* Informazioni su come connettersi a un [hub IoT](./how-to-ingest-data-iot-hub.md)
