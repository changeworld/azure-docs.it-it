---
title: Creare un punto di ripristino definito dall'utente per un pool SQL dedicato
description: Informazioni su come usare il portale di Azure per creare un punto di ripristino definito dall'utente per un pool SQL dedicato in Azure Synapse Analytics.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567928"
---
# <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente

In questo articolo si apprenderà come creare un nuovo punto di ripristino definito dall'utente per un pool SQL dedicato in Azure Synapse Analytics usando il portale di Azure.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Creare punti di ripristino definiti dall'utente tramite il portale di Azure

I punti di ripristino definiti dall'utente possono essere creati anche tramite portale di Azure.

1. Accedere all'account [portale di Azure](https://portal.azure.com/) personale.

2. Passare al pool SQL dedicato per cui si vuole creare un punto di ripristino.

3. Selezionare **Panoramica** nel riquadro sinistro e quindi **+ Nuovo punto di ripristino.** Se il pulsante Nuovo punto di ripristino non è abilitato, assicurarsi che il pool SQL dedicato non sia in pausa.

    ![Nuovo punto di ripristino](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Specificare un nome per il punto di ripristino definito dall'utente e fare clic su **Applica.** I punti di ripristino definiti dall'utente hanno un periodo di conservazione predefinito di sette giorni.

    ![Nome del punto di ripristino](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL dedicato esistente](restore-sql-pool.md)

