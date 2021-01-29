---
author: baanders
description: file di inclusione per le risorse necessarie per creare endpoint di dispositivi gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "99054509"
---
### <a name="prerequisite-create-endpoint-resources"></a>Prerequisito: creare risorse endpoint

Per collegare un endpoint ai dispositivi gemelli digitali di Azure, l'argomento della griglia di eventi, l'hub eventi o l'argomento del bus di servizio usato per l'endpoint deve esistere già.

Usare il grafico seguente per visualizzare le risorse che devono essere configurate prima di creare l'endpoint.

| Tipo di endpoint | Risorse obbligatorie (collegate alle istruzioni per la creazione) |
| --- | --- |
| Endpoint di griglia di eventi | [argomento di griglia di eventi](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Endpoint di hub eventi | [&nbsp; &nbsp; Spazio dei nomi di hub eventi](../articles/event-hubs/event-hubs-create.md)<br/><br/>[Hub eventi](../articles/event-hubs/event-hubs-create.md)<br/><br/>Opzionale [regola di autorizzazione](../articles/event-hubs/authorize-access-shared-access-signature.md) per l'autenticazione basata su chiave | 
| Endpoint del bus di servizio | [Spazio dei nomi del bus di servizio](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Argomento del bus di servizio](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> Opzionale [regola di autorizzazione](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature) per l'autenticazione basata su chiave|
