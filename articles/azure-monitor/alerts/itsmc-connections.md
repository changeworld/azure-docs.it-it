---
title: Connettore di Gestione dei servizi IT in Monitoraggio di Azure
description: Questo articolo offre informazioni su come connettere i prodotti/servizi di Gestione dei servizi IT con Connettore di Gestione dei servizi IT in Monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro di Gestione dei servizi IT.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009318"
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

## <a name="ip-ranges-for-itsm-partners-connections"></a>Intervalli IP per le connessioni partner ITSM
Per elencare gli indirizzi IP di ITSM per consentire le connessioni ITSM da partner ITSM Tools, è consigliabile elencare l'intero intervallo di indirizzi IP pubblici dell'area di Azure in cui appartiene l'area di lavoro di LogAnalytics. [Dettagli qui](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Per le aree EUS/UEO/EUS2/WUS2/Stati Uniti centro-meridionali, il cliente può elencare solo i tag di rete ActionGroup.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)
