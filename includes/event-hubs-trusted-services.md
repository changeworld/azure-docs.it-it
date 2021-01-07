---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5c0585182fcd6899bdd123607f3b7d46a97253e9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956455"
---
## <a name="trusted-microsoft-services"></a>Servizi Microsoft attendibili
Quando si Abilita l'impostazione **Consenti ai servizi Microsoft attendibili di ignorare questo firewall** , ai servizi seguenti viene concesso l'accesso alle risorse di hub eventi.

| Servizio attendibile | Scenari di utilizzo supportati | 
| --------------- | ------------------------- | 
| Griglia di eventi di Azure | Consente a griglia di eventi di Azure di inviare eventi a hub eventi nello spazio dei nomi di hub eventi. È anche necessario eseguire i passaggi seguenti: <ul><li>Abilitare l'identità assegnata dal sistema per un argomento o un dominio</li><li>Aggiungere l'identità al ruolo di mittente dei dati di hub eventi di Azure nello spazio dei nomi di hub eventi</li><li>Configurare quindi la sottoscrizione dell'evento che usa un hub eventi come endpoint per usare l'identità assegnata dal sistema.</li></ul> <p>Per altre informazioni, vedere [recapito di eventi con un'identità gestita](../articles/event-grid/managed-service-identity.md)</p>|
| Monitoraggio di Azure (impostazioni di diagnostica) | Consente a monitoraggio di Azure di inviare informazioni di diagnostica a hub eventi nello spazio dei nomi di hub eventi. |
| Analisi di flusso di Azure | Consente a un processo di analisi di flusso di Azure di leggere dati ([input](../articles/stream-analytics/stream-analytics-add-inputs.md)) o scrivere dati in hub eventi ([output](../articles/stream-analytics/event-hubs-output.md)) nello spazio dei nomi di hub eventi. <p>**Importante**: il processo di analisi di flusso deve essere configurato in modo da usare un' **identità gestita** per accedere all'hub eventi. Per altre informazioni, vedere [usare identità gestite per accedere a hub eventi da un processo di analisi di flusso di Azure (anteprima)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Hub IoT Azure | Consente all'hub Internet per inviare messaggi a hub eventi nello spazio dei nomi dell'hub eventi. È anche necessario eseguire i passaggi seguenti: <ul><li>Abilitare l'identità assegnata dal sistema per l'hub Internet delle cose</li><li>Aggiungere l'identità al ruolo di mittente dei dati di hub eventi di Azure nello spazio dei nomi di hub eventi.</li><li>Configurare quindi l'hub delle cose che usa un hub eventi come endpoint personalizzato per usare l'autenticazione basata sull'identità.</li></ul>
