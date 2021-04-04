---
title: Attività e applicazioni di replica del messaggio-bus di servizio di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della creazione di applicazioni e attività di replica dei messaggi con funzioni di Azure
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657500"
---
# <a name="message-replication-tasks-and-applications"></a>Applicazioni e attività di replica di messaggi

Come illustrato nell'articolo relativo alla [replica dei messaggi e alla Federazione tra aree](service-bus-federation-overview.md) , la replica delle sequenze di messaggi tra coppie di entità del bus di servizio e tra il bus di servizio e altre origini e destinazioni dei messaggi è in genere snella in funzioni di Azure.

[Funzioni di Azure](../azure-functions/functions-overview.md) è un ambiente di esecuzione scalabile e affidabile per la configurazione e l'esecuzione di applicazioni senza server, tra cui la [replica dei messaggi e](service-bus-federation-overview.md) le attività di Federazione.

In questa panoramica vengono illustrate le funzionalità predefinite di funzioni di Azure per tali applicazioni, i blocchi di codice che è possibile adattare e modificare per le attività di trasformazione e la configurazione di un'applicazione di funzioni di Azure in modo che si integri idealmente con il bus di servizio e altri servizi di messaggistica di Azure. Per molti dettagli, questo articolo punterà alla documentazione di funzioni di Azure.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
