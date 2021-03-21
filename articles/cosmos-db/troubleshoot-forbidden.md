---
title: Risoluzione dei problemi Azure Cosmos DB eccezioni non consentite
description: Informazioni su come diagnosticare e correggere le eccezioni non consentite.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99971906"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB eccezioni non consentite
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Il codice di stato HTTP 403 indica che non è consentito completare la richiesta.

## <a name="firewall-blocking-requests"></a>Richieste di blocco del firewall
In questo scenario è frequente vedere errori come quelli indicati di seguito:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Soluzione
Verificare che le [impostazioni del firewall](how-to-configure-firewall.md) correnti siano corrette e includano gli indirizzi IP o le reti da cui si sta provando a connettersi.
Se sono stati aggiornati di recente, tenere presente che l'applicazione delle modifiche può richiedere **fino a 15 minuti**.

## <a name="next-steps"></a>Passaggi successivi
* Configurare il [firewall IP](how-to-configure-firewall.md).
* Configurare l'accesso dalle [reti virtuali](how-to-configure-vnet-service-endpoint.md).
* Configurare l'accesso dagli [endpoint privati](how-to-configure-private-endpoints.md).
