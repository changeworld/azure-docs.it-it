---
title: Criteri di errore di output in Analisi di flusso di Azure
description: Informazioni sui criteri di gestione degli errori di output disponibili in Analisi di flusso di Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 19d762a55127af34e84185b11518aa6584acb5bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012411"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Criteri di errore di output in Analisi di flusso di Azure
Questo articolo descrive i criteri di gestione degli errori per i dati di output che possono essere configurati in Analisi di flusso di Azure.

I criteri di gestione degli errori per i dati di output si applicano solo agli errori di conversione dei dati che si verificano quando l'evento di output prodotto da un processo di Analisi di flusso di Azure non è conforme allo schema del sink di destinazione. È possibile configurare questo criterio scegliendo tra **Riprova** oppure **Rilascia**. Nel portale di Azure, all'interno di un processo di Analisi di flusso, sotto **Configura**, selezionare **Criteri di errore** per effettuare una selezione.

![Posizione dei criteri di errore di output in Analisi di flusso di Azure](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Riprova
Quando si verifica un errore, Analisi di flusso di Azure prova a scrivere l'evento a tempo indeterminato fino a quando la scrittura non ha esito positivo. Non è impostato alcun timeout per i tentativi. Alla fine, l'elaborazione di tutti gli eventi successivi viene bloccata dall'evento in fase di esecuzione. Questa opzione è il criterio di gestione degli errori di output predefinito.

## <a name="drop"></a>Rilascia
Analisi di flusso di Azure rilascerà qualsiasi evento di output che restituisca un errore di conversione dei dati. Gli eventi rilasciati non possono essere recuperati per essere rielaborati in seguito.


Tutti gli errori temporanei (ad esempio, gli errori di rete) vengono ritentati indipendentemente dalla configurazione dei criteri di gestione degli errori di output.


## <a name="next-steps"></a>Passaggi successivi
[Guida per la risoluzione dei problemi di Analisi di flusso di Azure](./stream-analytics-troubleshoot-query.md)