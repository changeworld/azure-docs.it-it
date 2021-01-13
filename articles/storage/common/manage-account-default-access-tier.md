---
title: Gestire il livello di accesso predefinito di un account di archiviazione di Azure
description: Informazioni su come modificare il livello di accesso predefinito di un account di archiviazione BLOB o GPv2
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 10d4e99d7bbebb6bc7d7def308e233507ed99ce9
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166496"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Gestire il livello di accesso predefinito di un account di archiviazione di Azure

Ogni account di archiviazione di Azure ha un livello di accesso predefinito, ad accesso frequente, sporadico o archivio. Il livello di accesso viene assegnato quando si crea un account di archiviazione. Il livello di accesso predefinito è caldo.

È possibile modificare il livello di account predefinito impostando l'attributo **livello di accesso** nell'account di archiviazione. La modifica del livello dell'account si applica a tutti gli oggetti archiviati nell'account che non dispongono di un set di livelli esplicito. L'attivazione/disattivazione del livello di account da frequente a sporadico comporta operazioni di scrittura (per 10.000) per tutti i BLOB senza un livello di impostazione solo per gli account GPv2 e l'attivazione/disattivazione da sporadico a frequente comporta sia le operazioni di lettura (per 10.000) che i costi di recupero dati (per GB) per tutti i BLOB negli account GPv2

Per i BLOB con il livello impostato a livello di oggetto, il livello di account non è applicabile. Il livello archivio è applicabile solo a livello di oggetto. È possibile passare tra i livelli di accesso in qualsiasi momento.

È possibile modificare il livello di accesso predefinito dopo che è stato creato un account di archiviazione attenendosi alla procedura descritta di seguito.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Modificare il livello di accesso dell'account predefinito di un account di archiviazione BLOB o GPv2

Negli scenari seguenti viene usato il portale di Azure o PowerShell:

# <a name="portal"></a>[Portale](#tab/portal)

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel portale di Azure cercare e selezionare **Tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. In **Impostazioni** selezionare **configurazione** per visualizzare e modificare la configurazione dell'account.

1. Selezionare il livello di accesso appropriato per le proprie esigenze: impostare il **livello di accesso** su sporadico **o frequente** .

1. Fare clic su **Salva** nella parte superiore.

![Modificare il livello di account predefinito in portale di Azure](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per modificare il livello di account, è possibile usare lo script di PowerShell seguente. La variabile `$rgName` deve essere inizializzata con il nome del gruppo di risorse. La variabile `$accountName` deve essere inizializzata con il nome dell'account di archiviazione.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Come gestire il livello di un BLOB in un account di archiviazione di Azure](../blobs/manage-access-tier.md)
- [Determinare se le prestazioni Premium potrebbero trarre vantaggio dall'app](../blobs/storage-blob-performance-tiers.md)
- [Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../blobs/monitor-blob-storage.md)
