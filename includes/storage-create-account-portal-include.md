---
title: includere file
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718201"
---
Per creare un account di archiviazione per utilizzo generico v2 nel portale di Azure, eseguire questa procedura:

1. Nel menu del portale di Azure selezionare **Tutti i servizi**. Nell'elenco delle risorse digitare **Account di archiviazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.
1. Nella finestra **Account di** archiviazione visualizzata scegliere **+ Nuovo**.
1. Nel pannello **Informazioni di base** selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare il gruppo di risorse desiderato o crearne uno nuovo.  Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole.
1. Selezionare un'area per l'account di archiviazione o usare l'area predefinita.
1. Selezionare un livello di prestazioni. Il livello predefinito è *Standard*.
1. Specificare la modalità di replica dell'account di archiviazione. L'opzione di ridondanza predefinita è *Archiviazione con ridondanza geografica .* Per altre informazioni sulle opzioni di replica disponibili, vedere [Ridondanza di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).
1. Sono disponibili opzioni aggiuntive nei **pannello Avanzate,** **Rete,** **Protezione dati** **e** Tag. Per usare Azure Data Lake Storage, scegliere il **pannello Avanzate** e quindi impostare Spazio dei **nomi gerarchico** su **Abilitato**. Per altre informazioni, vedere [Introduzione ad Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md).
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Crea**.

L'immagine seguente mostra le impostazioni nel pannello **Informazioni di** base per un nuovo account di archiviazione:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Screenshot che mostra come creare un account di archiviazione nel portale di Azure." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
