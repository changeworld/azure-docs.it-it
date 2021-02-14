---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure-configurazione con ServiceNow
description: Questo articolo illustra come connettere i prodotti/servizi ITSM con ServiceNow nell'esportazione sicura in monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 8ca77c1fa1232f7aed88b0d6942d8a0085caf0d5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380220"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Connettere ServiceNow a monitoraggio di Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto ServiceNow ed esportare in modo sicuro in Azure.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver soddisfatto i prerequisiti seguenti:

* Azure AD è registrato.
* Si dispone della versione supportata di ServiceNow Event Management-ITOM (versione New York o successiva).
* [Applicazione](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.2.0?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3Devent%2520management%2520connectors&sl=sh) installata nell'istanza di ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Configurare la connessione ServiceNow

1. Usare il collegamento https://(nome istanza). Service-Now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor l'URI per la definizione di esportazione protetta.

2. Seguire le istruzioni in base alla versione:
   * [Parigi](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
