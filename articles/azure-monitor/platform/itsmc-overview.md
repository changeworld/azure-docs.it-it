---
title: Panoramica di IT Service Management Connector
description: Questo articolo fornisce una panoramica di IT Service Management Connector (connettore).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93b6160b495b609e23e10b3f709d130792067423
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803783"
---
# <a name="it-service-management-connector-overview"></a>Panoramica di IT Service Management Connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT Service Management Connector (connettore) consente di connettere Azure a un prodotto o servizio ITSM (IT Service Management) supportato.

I servizi di Azure come Azure Log Analytics e monitoraggio di Azure offrono strumenti per rilevare, analizzare e risolvere i problemi relativi alle risorse di Azure e non Azure. Gli elementi di lavoro correlati a un problema in genere risiedono in un prodotto o servizio ITSM. CONNETTORE offre una connessione bidirezionale tra Azure e gli strumenti di ITSM per facilitare la risoluzione dei problemi più velocemente.

## <a name="configuration-steps"></a>Passaggi di configurazione

Connettore di Gestione dei servizi IT supporta le connessioni con gli strumenti di Gestione dei servizi IT seguenti:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Microsoft propone ai clienti Cherwell e Provance di usare l' [azione webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) per Cherwell e l'endpoint di Provance come altra soluzione per l'integrazione.

Con Gestione dei servizi IT è possibile:

-  Creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure (avvisi sulle metriche, avvisi del log attività e avvisi di Log Analytics).
-  Facoltativamente, è possibile sincronizzare i dati degli eventi imprevisti e delle richieste di modifica dallo strumento di Gestione dei servizi IT a un'area di lavoro Azure Log Analytics.

Per informazioni sulle condizioni legali e sull'informativa sulla privacy, vedere [informativa sulla privacy Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

È possibile iniziare a usare connettore completando i passaggi seguenti:

1. [Configurare l'ambiente ITSM per accettare gli avvisi da Azure.](./itsmc-connections.md)
1. [Configurare la soluzione Azure ITSM](./itsmc-definition.md#add-it-service-management-connector)
1. [Configurare Azure ITSM Connector per l'ambiente ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Configurare il gruppo di azioni per sfruttare il connettore ITSM.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)