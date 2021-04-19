---
title: storage-files-create-storage-account-portal
description: Creare un account di archiviazione per File di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717836"
---
Un account di archiviazione è un pool condiviso di spazio di archiviazione in cui è possibile distribuire una condivisione file di Azure o altre risorse di archiviazione, ad esempio BLOB o code. Un account di archiviazione può contenere un numero illimitato di condivisioni. In una condivisione può essere archiviato un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione.

Per creare un account di archiviazione:

1. Nel menu a sinistra selezionare **+** per creare una risorsa.
1. Selezionare **Account di archiviazione** per creare un account di archiviazione.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Screenshot dell'opzione dell'account di archiviazione nel pannello Crea una risorsa." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. In **Nome** digitare *mystorageacct* seguito da alcuni numeri casuali fino a visualizzare un segno di spunta verde indicante che si tratta di un nome univoco. Un nome di account di archiviazione deve contenere solo caratteri minuscoli ed essere globalmente univoco. Prendere nota del nome dell'account di archiviazione perché sarà necessario più avanti. 
1. In **Prestazioni** mantenere il valore predefinito **Standard**.
1. In **Replica** selezionare **Archiviazione con ridondanza locale**.
1. In **Sottoscrizione** selezionare la sottoscrizione usata per creare l'account di archiviazione. Se è presente una sola sottoscrizione, sarà quella predefinita.
1. Per **Gruppo di risorse** selezionare **Crea nuovo**. Per il nome immettere *myResourceGroup*.
1. In **Località** selezionare **Stati Uniti Orientali**.
1. Al termine, fare clic su **Crea** per avviare la distribuzione.