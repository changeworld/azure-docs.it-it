---
title: Ridimensionare un cluster di Analisi di flusso di Azure
description: Informazioni su come aumentare e ridurre le dimensioni di un cluster di Analisi di flusso di Azure.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e469e9bfd13bf8d89084b66e954cb51b27c2ebc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020027"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Ridimensionare un cluster di Analisi di flusso di Azure

La capacità di un cluster di Analisi di flusso di Azure viene misurata in unità di streaming. Possono essere eseguiti più processi in parallelo nello stesso cluster, a condizione che la somma delle unità di streaming assegnate a tutti i processi in esecuzione non superi la capacità del cluster.

È possibile aumentare e ridurre la capacità del cluster per adattarlo alle dimensioni dei carichi di lavoro di streaming. Il ridimensionamento di un cluster richiede tempo e non è previsto che venga eseguito frequentemente. È consigliabile pianificare un cluster ed effettuarne il provisioning con un numero preciso di unità di streaming che si prevede di usare.

## <a name="change-the-scale-of-your-cluster"></a>Modificare la scala di un cluster

1. Nel portale di Azure individuare e selezionare il cluster di Analisi di flusso di Azure.

1. Nella sezione **Panoramica** selezionare **Ridimensiona**. È possibile visualizzare il numero di unità di streaming assegnate al cluster. Usare il selettore per aumentare o ridurre le unità di streaming in base alle esigenze.

   ![ridimensionare il cluster](./media/scale-cluster/scale-cluster.png)

L'operazione di ridimensionamento non ha effetto sui processi attualmente in esecuzione.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come aumentare e ridurre i cluster di Analisi di flusso di Azure. Successivamente sarà possibile ottenere informazioni sulla gestione degli endpoint privati e sulla scalabilità automatica dei processi:

* [Gestire gli endpoint privati in un cluster di Analisi di flusso di Azure](private-endpoints.md)
* [Gestire i processi in un cluster di Analisi di flusso di Azure](manage-jobs-cluster.md)
