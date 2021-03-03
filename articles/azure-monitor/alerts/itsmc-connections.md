---
title: Connettore di Gestione dei servizi IT in Monitoraggio di Azure
description: Questo articolo offre informazioni su come connettere i prodotti/servizi di Gestione dei servizi IT con Connettore di Gestione dei servizi IT in Monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro di Gestione dei servizi IT.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734775"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Connettere prodotti e servizi di Gestione dei servizi IT con Connettore di Gestione dei servizi IT
Questo articolo fornisce informazioni su come configurare la connessione tra un prodotto o servizio di Gestione dei servizi IT e Connettore di Gestione dei servizi IT in Log Analytics per gestire da una posizione centrale gli elementi di lavoro. Per altre informazioni su Connettore di Gestione dei servizi IT, vedere [Panoramica](./itsmc-overview.md).

Sono supportati i prodotti/servizi ITSM seguenti. Selezionare il prodotto per visualizzare informazioni dettagliate su come connettere il prodotto a ITSMC.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Microsoft propone ai clienti Cherwell e Provance di usare l' [azione webhook](./action-groups.md#webhook) per Cherwell e l'endpoint di Provance come altra soluzione per l'integrazione.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)