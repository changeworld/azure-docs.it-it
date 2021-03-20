---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99569473"
---
Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, ma è possibile aumentare il limite di condivisione a 100 TiB. Per aumentare il limite di condivisione, abilitare una **condivisione file di grandi dimensioni** nell'account di archiviazione. Gli account di archiviazione Premium (account di archiviazione *filestorage* ) non hanno il flag di funzionalità di condivisione file di grandi dimensioni perché tutte le condivisioni file Premium sono già abilitate per il provisioning fino alla capacità 100-TIB completa.

È possibile abilitare le condivisioni file di grandi dimensioni solo in account di archiviazione Standard con ridondanza locale o ridondanza della zona. Dopo aver abilitato il flag di funzionalità della condivisione file di grandi dimensioni, non è possibile modificare il livello di ridondanza per l'archiviazione con ridondanza geografica o con ridondanza geografica della zona.

Per abilitare le condivisioni file di grandi dimensioni in un account di archiviazione esistente, passare a **condivisioni file** nel sommario dell'account di archiviazione.
In questo pannello selezionare **Condividi capacità**, modificare la capacità di condivisione in **100 TIB** e selezionare **Salva**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Screenshot dell'impostazione del consenso esplicito per la condivisione file di grandi dimensioni nella portale di Azure." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

È anche possibile abilitare le condivisioni file 100-TiB tramite il [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) cmdlet di PowerShell e il [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) comando dell'interfaccia della riga di comando di Azure. Per istruzioni dettagliate sull'abilitazione di condivisioni file di grandi dimensioni, vedere [Abilitare e creare condivisioni file di grandi dimensioni](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Per altre informazioni su come creare condivisioni file in nuovi account di archiviazione, vedere [Creazione di una condivisione file di Azure](../articles/storage/files/storage-how-to-create-file-share.md).