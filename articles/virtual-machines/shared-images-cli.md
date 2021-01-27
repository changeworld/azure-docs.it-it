---
title: Creare raccolte di immagini condivise con l'interfaccia della riga di comando di Azure
description: Questo articolo descrive come usare l'interfaccia della riga di comando di Azure per creare immagini di macchine virtuali condivise in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ee7f7b524225845dc68100ee8ec9292eef111232
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882349"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Creare una raccolta di immagini condivise con l'interfaccia della riga di comando di Azure

La [raccolta di immagini condivise](./shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

La raccolta di immagini condivise consente di condividere le immagini di VM personalizzate con altri utenti. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Passaggi successivi

Creare una versione di immagine da una [macchina virtuale](image-version-vm-cli.md)o da un' [immagine gestita](image-version-managed-image-cli.md) usando l'interfaccia della riga di comando di Azure.

Per altre informazioni sulle raccolte di immagini condivise, vedere [Panoramica](./shared-image-galleries.md). Se si verificano problemi, vedere [Risoluzione dei problemi delle raccolte di immagini condivise](troubleshooting-shared-images.md).