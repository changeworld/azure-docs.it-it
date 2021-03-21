---
title: includere file
description: includere file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511138"
---
## <a name="trusted-microsoft-services"></a>Servizi Microsoft attendibili
Quando si Abilita l'impostazione **Consenti ai servizi Microsoft attendibili di ignorare questo firewall** , ai servizi seguenti viene concesso l'accesso alle risorse del bus di servizio.

| Servizio attendibile | Scenari di utilizzo supportati | 
| --------------- | ------------------------- | 
| Griglia di eventi di Azure | Consente a griglia di eventi di Azure di inviare eventi a code o argomenti nello spazio dei nomi del bus di servizio. È anche necessario eseguire i passaggi seguenti: <ul><li>Abilitare l'identità assegnata dal sistema per un argomento o un dominio</li><li>Aggiungere l'identità al ruolo mittente dati del bus di servizio di Azure nello spazio dei nomi del bus di servizio</li><li>Configurare quindi la sottoscrizione dell'evento che usa una coda o un argomento del bus di servizio come endpoint per usare l'identità assegnata dal sistema.</li></ul> <p>Per altre informazioni, vedere [recapito di eventi con un'identità gestita](../articles/event-grid/managed-service-identity.md)</p>|
| Gestione API di Azure | <p>Il servizio gestione API consente di inviare messaggi a una coda o a un argomento del bus di servizio nello spazio dei nomi del bus di servizio.</p><ul><li>È possibile attivare flussi di lavoro personalizzati inviando messaggi alla coda o all'argomento del bus di servizio quando viene richiamata un'API usando i [criteri Send-request](../articles/api-management/api-management-sample-send-request.md).</li><li>È anche possibile considerare una coda o un argomento del bus di servizio come back-end in un'API. Per un criterio di esempio, vedere [eseguire l'autenticazione con un'identità gestita per accedere a una coda o a un argomento del bus di servizio](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml). È anche necessario eseguire i passaggi seguenti:<ol><li>Abilitare l'identità assegnata dal sistema nell'istanza di gestione API. Per istruzioni, vedere [usare identità gestite in gestione API di Azure](../articles/api-management/api-management-howto-use-managed-service-identity.md).</li><li>Aggiungere l'identità al ruolo **mittente dati del bus di servizio di Azure** nello spazio dei nomi del bus di servizio</li></ol></li></ul> | 