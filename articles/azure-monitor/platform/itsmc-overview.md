---
title: Panoramica di IT Service Management Connector
description: Questo articolo fornisce una panoramica di IT Service Management Connector (connettore).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: aaaeb23ef24f0d8a0fa4b38139fed57cda6fa63d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657040"
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

1. [Connettere i prodotti/servizi ITSM con IT Service Management Connector.](./itsmc-connections.md)
1. [Aggiungere connettore.](/.itsmc-definition.md#add-it-service-management-connector)
1. [Creare una connessione ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Utilizzare la connessione.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere prodotti/servizi ITSM a IT Service Management Connector](./itsmc-connections.md) 
 [Aggiungi connettore ITSM](./itsmc-definition.md) 
 [Risoluzione dei problemi nel connettore ITSM](./itsmc-resync-servicenow.md)