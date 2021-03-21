---
title: Ottenere la ridondanza geografica per i processi di analisi di flusso di Azure
description: Questo articolo descrive come ottenere la ridondanza geografica dei processi di analisi di flusso di Azure anziché il failover geografico.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015522"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Ottenere la ridondanza geografica per i processi di analisi di flusso di Azure

Analisi di flusso di Azure non fornisce il failover geografico automatico, ma è possibile ottenere la ridondanza geografica distribuendo processi di analisi di flusso identici in più aree di Azure. Ogni processo si connette a un input locale e a origini di output locali. È responsabilità dell'applicazione inviare i dati di input nei due input di area e riconciliare tra i due output regionali. I processi di analisi di flusso sono due entità separate.

Il diagramma seguente illustra una distribuzione del processo di analisi di flusso con ridondanza geografica di esempio con l'input dell'hub eventi e l'output del database di Azure.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="diagramma dei processi di analisi di flusso con ridondanza geografica":::

## <a name="primarysecondary-strategy"></a>Strategia primaria/secondaria

L'applicazione deve gestire il database di output dell'area considerata come primaria e che viene considerato secondario. In caso di errore dell'area primaria, l'applicazione passa al database secondario e avvia la lettura degli aggiornamenti da tale database. Il meccanismo effettivo che consente di ridurre al minimo le letture duplicate dipende dall'applicazione.Per semplificare questo processo, è possibile scrivere informazioni aggiuntive nell'output. Ad esempio, è possibile aggiungere un timestamp o un ID di sequenza a ogni output per evitare che le righe duplicate vengano ignorate un'operazione semplice. Una volta ripristinata l'area primaria, viene rilevata con il database secondario usando meccanismi analoghi.

Sebbene i diversi tipi di input e output consentano diverse opzioni di replica geografica, è consigliabile usare il modello descritto in questo articolo per ottenere la ridondanza geografica, in quanto fornisce flessibilità e controllo per i produttori di eventi e i consumer di eventi.

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare e gestire processi di Analisi di flusso di Azure con PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Debug guidato dai dati in Analisi di flusso di Azure](stream-analytics-job-diagram-with-metrics.md)