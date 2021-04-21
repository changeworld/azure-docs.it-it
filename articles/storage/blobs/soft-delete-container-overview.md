---
title: Eliminazione soft per i contenitori (anteprima)
titleSuffix: Azure Storage
description: L'eliminazione soft per i contenitori (anteprima) protegge i dati in modo da poterli ripristinare più facilmente quando vengono modificati o eliminati erroneamente da un'applicazione o da un altro utente dell'account di archiviazione.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 2efd673d26231e83d820f7971a740d06e9b2a1d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785416"
---
# <a name="soft-delete-for-containers-preview"></a>Eliminazione soft per i contenitori (anteprima)

L'eliminazione software per i contenitori (anteprima) protegge i dati dall'eliminazione accidentale o dannosa. Quando l'eliminazione soft del contenitore è abilitata per un account di archiviazione, un contenitore eliminato e il relativo contenuto vengono mantenuti in Archiviazione di Azure per il periodo specificato. Durante il periodo di conservazione è possibile ripristinare i contenitori eliminati in precedenza. Il ripristino di un contenitore ripristina tutti i BLOB che erano all'interno del contenitore al momento dell'eliminazione.

Per la protezione end-to-end per i dati BLOB, Microsoft consiglia di abilitare le funzionalità di protezione dei dati seguenti:

- Eliminazione soft del contenitore, per ripristinare un contenitore che è stato eliminato. Per informazioni su come abilitare l'eliminazione soft del contenitore, vedere [Abilitare e gestire l'eliminazione soft per i contenitori](soft-delete-container-enable.md).
- Controllo delle versioni dei BLOB, per mantenere automaticamente le versioni precedenti di un BLOB. Quando il controllo delle versioni dei BLOB è abilitato, è possibile ripristinare una versione precedente di un BLOB per ripristinare i dati se vengono modificati o eliminati erroneamente. Per informazioni su come abilitare il controllo delle versioni dei BLOB, vedere [Abilitare e gestire il controllo delle versioni dei BLOB.](versioning-enable.md)
- Eliminazione software del BLOB, per ripristinare un BLOB o una versione eliminata. Per informazioni su come abilitare l'eliminazione blob soft, vedere [Abilitare e gestire l'eliminazione soft per i BLOB](soft-delete-blob-enable.md).

> [!IMPORTANT]
> L'eliminazione del contenitore è attualmente disponibile in **ANTEPRIMA.** Per le condizioni legali applicabili alle funzionalità di Azure in versione beta, in anteprima o altrimenti non ancora rilasciate nella disponibilità [generale, vedere](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Le condizioni per l'utilizzo supplementari per Microsoft Azure preview.

## <a name="how-container-soft-delete-works"></a>Funzionamento dell'eliminazione soft del contenitore

Quando si abilita l'eliminazione soft del contenitore, è possibile specificare un periodo di conservazione per i contenitori eliminati compreso tra 1 e 365 giorni. Il periodo di conservazione predefinito è di 7 giorni. Durante il periodo di conservazione, è possibile ripristinare un contenitore eliminato chiamando **l'operazione Ripristina contenitore.**

Quando si ripristina un contenitore, vengono ripristinati anche i BLOB del contenitore e tutte le versioni del BLOB. Tuttavia, è possibile usare l'eliminazione soft del contenitore solo per ripristinare i BLOB se il contenitore stesso è stato eliminato. Per ripristinare un BLOB eliminato quando il contenitore padre non è stato eliminato, è necessario usare l'eliminazione software o il controllo delle versioni dei BLOB.

> [!WARNING]
> L'eliminazione automatica dei contenitori può ripristinare solo interi contenitori e il relativo contenuto al momento dell'eliminazione. Non è possibile ripristinare un BLOB eliminato all'interno di un contenitore usando l'eliminazione soft del contenitore. Microsoft consiglia anche di abilitare l'eliminazione soft dei BLOB e il controllo delle versioni dei BLOB per proteggere i singoli BLOB in un contenitore.

Il diagramma seguente illustra come ripristinare un contenitore eliminato quando è abilitata l'eliminazione soft del contenitore:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagramma che illustra come ripristinare un contenitore eliminato automaticamente":::

Quando si ripristina un contenitore, è possibile ripristinarne il nome originale se tale nome non è stato riutilizzato. Se è stato usato il nome del contenitore originale, è possibile ripristinare il contenitore con un nuovo nome.

Dopo la scadenza del periodo di conservazione, il contenitore viene eliminato definitivamente Archiviazione di Azure e non può essere recuperato. L'orologio inizia nel periodo di conservazione nel punto in cui il contenitore viene eliminato. È possibile modificare il periodo di conservazione in qualsiasi momento, ma tenere presente che un periodo di conservazione aggiornato si applica solo ai nuovi contenitori eliminati. I contenitori eliminati in precedenza verranno eliminati definitivamente in base al periodo di conservazione attivo al momento dell'eliminazione del contenitore.

La disabilitazione dell'eliminazione temporanea del contenitore non comporta l'eliminazione definitiva dei contenitori precedentemente eliminati temporaneamente. Tutti i contenitori eliminati temporaneamente verranno eliminati definitivamente alla scadenza del periodo di conservazione attivo al momento dell'eliminazione del contenitore.

> [!IMPORTANT]
> L'eliminazione soft del contenitore non protegge dall'eliminazione di un account di archiviazione. Protegge solo dall'eliminazione di contenitori in tale account. Per proteggere un account di archiviazione dall'eliminazione, configurare un blocco sulla risorsa dell'account di archiviazione. Per altre informazioni sul blocco di un account di archiviazione, [vedere Applicare un Azure Resource Manager a un account di archiviazione.](../common/lock-account-resource.md)

## <a name="about-the-preview"></a>Informazioni sulla versione di anteprima

L'eliminazione automatica dei contenitori è disponibile in anteprima in tutte le aree di Azure.

La versione 2019-12-12 o successiva dell'API REST Archiviazione di Azure supporta l'eliminazione software del contenitore.

### <a name="storage-account-support"></a>Supporto dell'account di archiviazione

L'eliminazione soft del contenitore è disponibile per i tipi di account di archiviazione seguenti:

- Account di archiviazione per utilizzo generico v2 e v1
- Account di archiviazione BLOB in blocchi
- Account di archiviazione BLOB

Sono supportati anche gli account di archiviazione con uno spazio dei nomi gerarchico abilitato per Azure Data Lake Storage Gen2 archiviazione.

### <a name="register-for-the-preview"></a>Registrarsi per l'anteprima

Per registrarsi nell'anteprima per l'eliminazione automatica del contenitore, usare PowerShell o l'interfaccia della riga di comando di Azure per inviare una richiesta di registrazione della funzionalità con la sottoscrizione. Dopo l'approvazione della richiesta, è possibile abilitare l'eliminazione soft del contenitore con qualsiasi account di archiviazione BLOB in blocchi, archiviazione BLOB o per utilizzo generico v2 nuovo o esistente.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire la registrazione con PowerShell, chiamare [il comando Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la registrazione con l'interfaccia della riga di comando di Azure, [chiamare il comando az feature register.](/cli/azure/feature#az_feature_register)

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Controllare lo stato della registrazione

Per controllare lo stato della registrazione, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per controllare lo stato della registrazione con PowerShell, chiamare il [comando Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per controllare lo stato della registrazione con l'interfaccia della riga di comando di Azure, chiamare [il comando az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Non è necessario alcun addebito aggiuntivo per abilitare l'eliminazione software del contenitore. I dati nei contenitori eliminati in modo fisso vengono fatturati alla stessa tariffa dei dati attivi.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'eliminazione soft del contenitore](soft-delete-container-enable.md)
- [Eliminazione temporanea per i BLOB](soft-delete-blob-overview.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)
