---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0e487b3ab3663c765c052f2064201865508ef57f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510725"
---
## <a name="trusted-microsoft-services"></a>Servizi Microsoft attendibili
Quando si Abilita l'impostazione **Consenti ai servizi Microsoft attendibili di ignorare questo firewall** , ai servizi seguenti viene concesso l'accesso alle risorse di hub eventi.

| Servizio attendibile | Scenari di utilizzo supportati | 
| --------------- | ------------------------- | 
| Griglia di eventi di Azure | Consente a griglia di eventi di Azure di inviare eventi a hub eventi nello spazio dei nomi di hub eventi. È anche necessario eseguire i passaggi seguenti: <ul><li>Abilitare l'identità assegnata dal sistema per un argomento o un dominio</li><li>Aggiungere l'identità al ruolo di mittente dei dati di hub eventi di Azure nello spazio dei nomi di hub eventi</li><li>Configurare quindi la sottoscrizione dell'evento che usa un hub eventi come endpoint per usare l'identità assegnata dal sistema.</li></ul> <p>Per altre informazioni, vedere [recapito di eventi con un'identità gestita](../articles/event-grid/managed-service-identity.md)</p>|
| Monitoraggio di Azure (impostazioni di diagnostica) | Consente a monitoraggio di Azure di inviare informazioni di diagnostica a hub eventi nello spazio dei nomi di hub eventi. Monitoraggio di Azure può leggere dall'hub eventi e anche scrivere dati nell'hub eventi. |
| Analisi di flusso di Azure | Consente a un processo di analisi di flusso di Azure di leggere dati ([input](../articles/stream-analytics/stream-analytics-add-inputs.md)) o scrivere dati in hub eventi ([output](../articles/stream-analytics/event-hubs-output.md)) nello spazio dei nomi di hub eventi. <p>**Importante**: il processo di analisi di flusso deve essere configurato in modo da usare un' **identità gestita** per accedere all'hub eventi. Per altre informazioni, vedere [usare identità gestite per accedere a hub eventi da un processo di analisi di flusso di Azure (anteprima)](../articles/stream-analytics/event-hubs-managed-identity.md). </p>|
| Hub IoT di Azure | Consente all'hub Internet per inviare messaggi a hub eventi nello spazio dei nomi dell'hub eventi. È anche necessario eseguire i passaggi seguenti: <ul><li>Abilitare l'identità assegnata dal sistema per l'hub Internet delle cose</li><li>Aggiungere l'identità al ruolo di mittente dei dati di hub eventi di Azure nello spazio dei nomi di hub eventi.</li><li>Configurare quindi l'hub delle cose che usa un hub eventi come endpoint personalizzato per usare l'autenticazione basata sull'identità.</li></ul>
| Gestione API di Azure | <p>Il servizio gestione API consente di inviare eventi a un hub eventi nello spazio dei nomi di hub eventi.</p> <ul><li>È possibile attivare flussi di lavoro personalizzati inviando eventi all'hub eventi quando viene richiamata un'API usando i [criteri Send-request](../articles/api-management/api-management-sample-send-request.md).</li><li>È anche possibile considerare un hub eventi come back-end in un'API. Per un criterio di esempio, vedere [eseguire l'autenticazione con un'identità gestita per accedere a un hub eventi](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml). È anche necessario eseguire i passaggi seguenti:<ol><li>Abilitare l'identità assegnata dal sistema nell'istanza di gestione API. Per istruzioni, vedere [usare identità gestite in gestione API di Azure](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Aggiungere l'identità al ruolo di **mittente dei dati di hub eventi di Azure** nello spazio dei nomi di hub eventi</li></ol></li></ul> | 
