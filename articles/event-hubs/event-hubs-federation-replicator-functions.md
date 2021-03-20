---
title: Attività e applicazioni di replica di eventi-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della creazione di applicazioni e attività di replica degli eventi con funzioni di Azure
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97663644"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Attività e applicazioni di replica di eventi con funzioni di Azure

> [!TIP]
> Per tutte le attività di replica con stato in cui è necessario prendere in considerazione i payload degli eventi e trasformarli, aggregarli, arricchirli o ridurli, usare [analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) anziché funzioni di Azure.

Come illustrato nell'articolo relativo alla [replica degli eventi e alla Federazione tra aree](event-hubs-federation-overview.md) , la replica senza stato dei flussi di eventi tra coppie di hub eventi e tra hub eventi e altre origini e destinazioni del flusso di eventi si basa su funzioni di Azure.

[Funzioni di Azure](../azure-functions/functions-overview.md) è un ambiente di esecuzione scalabile e affidabile per la configurazione e l'esecuzione di applicazioni senza server, tra cui la replica di eventi e le attività di Federazione.

In questa panoramica vengono illustrate le funzionalità predefinite di funzioni di Azure per tali applicazioni, i blocchi di codice che è possibile adattare e modificare per le attività di trasformazione e la configurazione di un'applicazione di funzioni di Azure in modo che si integri idealmente con hub eventi e altri servizi di messaggistica di Azure. Per molti dettagli, questo articolo punterà alla documentazione di funzioni di Azure.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









