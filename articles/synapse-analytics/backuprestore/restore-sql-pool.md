---
title: Ripristinare un pool SQL dedicato esistente
description: Guida alle istruzioni per il ripristino di un pool SQL dedicato esistente.
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f2fb6f809794781559683907a806e6d30ca9bed6
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567979"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Ripristinare un pool SQL dedicato esistente

Questo articolo illustra come ripristinare un pool SQL dedicato esistente in Azure Synapse Analytics usando portale di Azure e Synapse Studio. Questo articolo si applica sia ai ripristini che ai ripristini geografici. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Ripristinare un pool SQL dedicato esistente tramite il Synapse Studio

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare all'area di lavoro Synapse. 
3. In Attività iniziali -> Apri Synapse Studio selezionare **Apri**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. Nel riquadro di spostamento a sinistra selezionare **Dati**.
5. Selezionare **Gestisci pool**. 
6. Selezionare **+ Nuovo** per creare un nuovo pool SQL dedicato. 
7. Nella scheda Impostazioni aggiuntive selezionare un punto di ripristino da cui eseguire il ripristino. 

    Se si vuole eseguire un ripristino geografico, selezionare l'area di lavoro e il pool SQL dedicato da ripristinare. 

8. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**. 

    ![Punti di ripristino](../media/sql-pools/restore-point.PNG)

    Se il pool SQL dedicato non dispone di punti di ripristino automatici, attendere alcune ore o creare un punto di ripristino definito dall'utente prima del ripristino. Per User-Defined punti di ripristino, selezionarne uno esistente o crearne uno nuovo.

    Se si ripristina un backup geografico, è sufficiente selezionare l'area di lavoro nell'area di origine e il pool SQL dedicato da ripristinare. 

9. Selezionare **Rivedi e crea**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Ripristinare un pool SQL dedicato esistente tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al pool SQL dedicato da cui si vuole eseguire il ripristino.
3. Nella parte superiore del pannello della panoramica, selezionare **Ripristina**.

    ![ Panoramica del servizio di ripristino](../media/sql-pools/restore-sqlpool-01.png)

4. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**. 

    Se il pool SQL dedicato non dispone di punti di ripristino automatici, attendere alcune ore o creare un punto di ripristino definito dall'utente prima del ripristino. 

    Se si vuole eseguire un ripristino geografico, selezionare l'area di lavoro e il pool SQL dedicato da ripristinare. 

5. Selezionare **Rivedi e crea**.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un punto di ripristino](sqlpool-create-restore-point.md)
