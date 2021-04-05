---
title: includere file
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98109512"
---
Per creare un account di archiviazione per utilizzo generico v2 nel portale di Azure, eseguire questa procedura:

1. Nel menu del portale di Azure selezionare **Tutti i servizi**. Nell'elenco delle risorse digitare **Account di archiviazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di archiviazione**.
1. Nella finestra **Account di archiviazione** visualizzata scegliere **Aggiungi**.
1. Nella scheda **Informazioni di base** selezionare la sottoscrizione in cui creare l'account di archiviazione.
1. Nel campo **Gruppo di risorse** selezionare il gruppo di risorse desiderato o crearne uno nuovo.  Per altre informazioni sui gruppi di risorse di Azure, vedere [Panoramica di Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Immettere quindi un nome per l'account di archiviazione. Il nome scelto deve essere univoco in Azure. Deve avere inoltre una lunghezza compresa tra 3 e 24 caratteri e contenere solo numeri e lettere minuscole.
1. Selezionare la località per l'account di archiviazione o usare la località predefinita.
1. Selezionare un livello di prestazioni. Il livello predefinito è *Standard*.
1. Impostare il campo **Tipologia account** su *Archiviazione V2 (utilizzo generico v2)* .
1. Specificare la modalità di replica dell'account di archiviazione. L'opzione di replica predefinita è *Archiviazione con ridondanza geografica e accesso in lettura*. Per altre informazioni sulle opzioni di replica disponibili, vedere [Ridondanza di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).
1. Altre opzioni sono disponibili nelle schede **Rete**, **Protezione dei dati**, **Avanzate** e **Tag**. Per usare Azure Data Lake Storage, scegliere la scheda **Avanzate** e quindi impostare **Spazio dei nomi gerarchico** su **Abilitato**. Per altre informazioni, vedere [Introduzione ad Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md).
1. Selezionare **Rivedi e crea** per esaminare le impostazioni dell'account di archiviazione e creare l'account.
1. Selezionare **Crea**.

La figura seguente mostra le impostazioni nella scheda **Informazioni di base** per un nuovo account di archiviazione:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Screenshot che illustra come creare un account di archiviazione nel portale di Azure":::
