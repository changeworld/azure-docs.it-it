---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure-configurazione con ServiceNow
description: Questo articolo illustra come connettere i prodotti/servizi ITSM con ServiceNow nell'esportazione sicura in monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841192"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Connettere ServiceNow a monitoraggio di Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto ServiceNow ed esportare in modo sicuro in Azure.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver soddisfatto i prerequisiti seguenti:

* Azure AD è registrato.
* Si dispone della versione supportata di ServiceNow Event Management-ITOM (versione Orlando o successiva).

## <a name="configure-the-servicenow-connection"></a>Configurare la connessione ServiceNow

1. Usare il collegamento https://(nome istanza). Service-Now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor l'URI per la definizione di esportazione protetta.

2. Seguire le istruzioni in base alla versione:
   * [Parigi](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)