---
title: Codici errore di disponibilità esterna - Analisi di flusso di Azure
description: Risolvere i problemi di Analisi di flusso di Azure con i codici errore di disponibilità esterna.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 71625c4b81fc0795c376a89397e98659f4c72ff0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020554"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Codici errore di disponibilità esterna di Analisi di flusso di Azure

È possibile usare log attività e log risorse per facilitare il debug di comportamenti imprevisti del processo di Analisi di flusso di Azure. Questo articolo riporta la descrizione di ogni codice errore di disponibilità esterna. Gli errori di disponibilità esterna si verificano quando un servizio dipendente non è disponibile.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Causa**: Un servizio è temporaneamente non disponibile.
* **Raccomandazione**: Analisi di flusso continuerà a tentare di raggiungere il servizio.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Causa**: Analisi di flusso ha rilevato un errore durante la comunicazione con EventHub. 


## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md)
* [Risolvere i problemi degli output di Analisi di flusso di Azure](stream-analytics-troubleshoot-output.md)
* [Risolvere i problemi delle query di Analisi di flusso di Azure](stream-analytics-troubleshoot-query.md)
* [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md)
* [Errori dei dati di Analisi di flusso di Azure](data-errors.md)
