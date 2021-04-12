---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f0d0322f6f5f14b94a67285fe8688d72c941b3a4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105580"
---
<!-- ### Create a storage account -->

Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. Per altre informazioni sull'uso degli account di archiviazione in Servizi multimediali, vedere [Account di archiviazione](../storage-account-concept.md).

È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). Gli account solo BLOB non sono consentiti come **primari**. Per altre informazioni sugli account di archiviazione, vedere [Opzioni di account di archiviazione di Azure](../../../storage/common/storage-account-overview.md). 

In questo esempio viene creato un account di archiviazione con ridondanza locale Standard per utilizzo generico v2. Se si vogliono provare gli account di archiviazione, usare `--sku Standard_LRS`. Tuttavia, quando si sceglie SKU per la produzione, è consigliabile usare `--sku Standard_RAGRS` che offre la replica geografica per la continuità aziendale. Per altre informazioni, vedere [account di archiviazione](/cli/azure/storage/account).

Il comando seguente consente di creare un account di archiviazione che verrà associato all'account di Servizi multimediali. Nello script seguente sostituire `storageaccountforams` con il proprio nome univoco con una lunghezza inferiore a 24 caratteri. `amsResourceGroup` deve corrispondere al valore assegnato per il gruppo di risorse nel passaggio precedente.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
