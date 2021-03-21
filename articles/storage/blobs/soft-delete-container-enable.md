---
title: Abilitare e gestire l'eliminazione temporanea per i contenitori (anteprima)
titleSuffix: Azure Storage
description: Abilitare l'eliminazione temporanea del contenitore (anteprima) per ripristinare più facilmente i dati quando viene erroneamente modificato o eliminato.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216344"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Abilitare e gestire l'eliminazione temporanea per i contenitori (anteprima)

L'eliminazione temporanea del contenitore (anteprima) impedisce che i dati vengano modificati o eliminati accidentalmente o in modo errato. Quando l'eliminazione temporanea del contenitore è abilitata per un account di archiviazione, è possibile recuperare un contenitore e il relativo contenuto dopo che è stato eliminato, entro un periodo di conservazione specificato.

Se è possibile che i dati vengano accidentalmente modificati o eliminati da un'applicazione o da un altro utente dell'account di archiviazione, Microsoft consiglia di attivare l'eliminazione temporanea del contenitore. Questo articolo illustra come abilitare l'eliminazione temporanea per i contenitori. Per altri dettagli sull'eliminazione temporanea dei contenitori, inclusa la registrazione per l'anteprima, vedere [eliminazione temporanea per i contenitori (anteprima)](soft-delete-container-overview.md).

Per la protezione dei dati end-to-end, Microsoft consiglia di abilitare anche l'eliminazione temporanea per i BLOB e il controllo delle versioni dei BLOB. Per informazioni su come abilitare anche l'eliminazione temporanea per i BLOB, vedere [Enable and Manage soft delete for Blobs](soft-delete-blob-enable.md). Per informazioni su come abilitare il controllo delle versioni dei BLOB, vedere [controllo delle versioni dei BLOB](versioning-overview.md).

> [!IMPORTANT]
>
> L'eliminazione temporanea del contenitore è attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali applicabili alle funzionalità di Azure disponibili in versione beta, di anteprima o non ancora rilasciate a livello generale.

## <a name="enable-container-soft-delete"></a>Abilita eliminazione temporanea del contenitore

È possibile abilitare o disabilitare l'eliminazione temporanea del contenitore per l'account di archiviazione in qualsiasi momento usando il portale di Azure o un modello di Azure Resource Manager.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per abilitare l'eliminazione temporanea del contenitore per l'account di archiviazione usando portale di Azure, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/) passare all'account di archiviazione.
1. Individuare le impostazioni di **protezione dati** in **servizio BLOB**.
1. Impostare la proprietà eliminazione temporanea del **contenitore** su *abilitata*.
1. In **criteri di conservazione** specificare per quanto tempo i contenitori eliminati temporaneamente vengono conservati da archiviazione di Azure.
1. Salvare le modifiche.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Screenshot che illustra come abilitare l'eliminazione temporanea del contenitore in portale di Azure":::

# <a name="template"></a>[Modello](#tab/template)

Per abilitare l'eliminazione temporanea del contenitore con un modello di Azure Resource Manager, creare un modello per impostare la proprietà **containerDeleteRetentionPolicy** . Nei passaggi seguenti viene descritto come creare un modello nel portale di Azure.

1. Nella portale di Azure scegliere **Crea una risorsa**.
1. In **Cerca nel Marketplace** Digitare **distribuzione modello**, quindi premere **invio**.
1. Scegliere **distribuzione modelli**, fare clic su **Crea** e quindi scegliere **Compila modello personalizzato nell'editor**.
1. Nell'editor dei modelli incollare il codice JSON seguente. Sostituire il segnaposto `<account-name>` con il nome del proprio account di archiviazione.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Specificare il periodo di memorizzazione. Il valore predefinito è 7.
1. Salvare il modello.
1. Specificare il gruppo di risorse dell'account, quindi scegliere il pulsante **Verifica + crea** per distribuire il modello e abilitare l'eliminazione temporanea del contenitore.

## <a name="view-soft-deleted-containers"></a>Visualizzare i contenitori eliminati temporaneamente

Quando l'eliminazione temporanea è abilitata, è possibile visualizzare i contenitori eliminati temporaneamente nell'portale di Azure. I contenitori eliminati temporaneamente sono visibili durante il periodo di memorizzazione specificato. Dopo la scadenza del periodo di memorizzazione, un contenitore eliminato temporaneamente viene eliminato definitivamente e non è più visibile.

Per visualizzare i contenitori eliminati temporaneamente nella portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nell'portale di Azure e visualizzare l'elenco dei contenitori.
1. Attivare o disattivare l'opzione Mostra contenitori eliminati per includere i contenitori eliminati nell'elenco.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Screenshot che illustra come visualizzare i contenitori eliminati temporaneamente nel portale di Azure":::

## <a name="restore-a-soft-deleted-container"></a>Ripristinare un contenitore eliminato temporaneamente

È possibile ripristinare un contenitore eliminato temporaneamente e il relativo contenuto entro il periodo di memorizzazione. Per ripristinare un contenitore eliminato temporaneamente nella portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nell'portale di Azure e visualizzare l'elenco dei contenitori.
1. Visualizzare il menu di scelta rapida per il contenitore che si desidera ripristinare e scegliere **Annulla eliminazione** dal menu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Screenshot che illustra come ripristinare un contenitore eliminato temporaneamente in portale di Azure":::

## <a name="next-steps"></a>Passaggi successivi

- [Eliminazione temporanea per i contenitori (anteprima)](soft-delete-container-overview.md)
- [Eliminazione temporanea per i BLOB](soft-delete-blob-overview.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)
