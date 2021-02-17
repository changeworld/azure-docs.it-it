---
title: Recapitare gli eventi tramite il servizio di collegamento privato
description: Questo articolo descrive come ovviare alla limitazione di non poter recapitare gli eventi usando il servizio di collegamento privato.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 4343740ea6c34c9ae282723b79007f7035785b04
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548616"
---
# <a name="deliver-events-using-private-link-service"></a>Recapitare gli eventi tramite il servizio di collegamento privato
Attualmente non è possibile recapitare gli eventi usando [endpoint privati](../private-link/private-endpoint-overview.md). In altre condizioni, non è previsto alcun supporto se si dispone di requisiti di isolamento di rete rigorosi in cui il traffico degli eventi recapitati non deve lasciare lo spazio IP privato. 

## <a name="use-managed-identity"></a>Usare l'identità gestita
Tuttavia, se i requisiti richiedono un modo sicuro per inviare eventi usando un canale crittografato e un'identità nota del mittente (in questo caso, griglia di eventi) usando lo spazio IP pubblico, è possibile recapitare gli eventi a hub eventi, bus di servizio o servizio di archiviazione di Azure usando un argomento personalizzato di griglia di eventi di Azure o un dominio con identità gestita dal sistema. Per informazioni dettagliate su come recapitare gli eventi usando l'identità gestita, vedere [recapito di eventi usando un'identità gestita](managed-service-identity.md). 

Quindi, è possibile usare un collegamento privato configurato in funzioni di Azure o il webhook distribuito nella rete virtuale per eseguire il pull degli eventi. Vedere l'esempio: [connettersi agli endpoint privati con funzioni di Azure](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Distribuzione tramite il servizio di collegamento privato":::


Con questa configurazione, il traffico passa attraverso l'IP pubblico/Internet da griglia di eventi a hub eventi, bus di servizio o archiviazione di Azure, ma il canale può essere crittografato e viene usata un'identità gestita di griglia di eventi. Se si configurano le funzioni di Azure o il webhook distribuito nella rete virtuale per l'uso di hub eventi, bus di servizio o archiviazione di Azure tramite un collegamento privato, la sezione del traffico sarà chiaramente in Azure.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Recapitare gli eventi a hub eventi usando l'identità gestita
Per recapitare gli eventi a hub eventi nello spazio dei nomi di hub eventi usando l'identità gestita, seguire questa procedura:

1. [Abilitare l'identità assegnata dal sistema per un argomento o un dominio](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. [Aggiungere l'identità al ruolo di **mittente dei dati di hub eventi di Azure** nello spazio dei nomi di hub eventi](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Abilitare l'impostazione **Consenti a servizi Microsoft attendibili di ignorare questo firewall** sullo spazio dei nomi di hub eventi](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Configurare la sottoscrizione di eventi](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) che usa un hub eventi come endpoint per usare l'identità assegnata dal sistema.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Recapitare gli eventi al bus di servizio usando l'identità gestita
Per recapitare gli eventi alle code o agli argomenti del bus di servizio nello spazio dei nomi del bus di servizio usando l'identità gestita, seguire questa procedura:

1. [Abilitare l'identità assegnata dal sistema per un argomento o un dominio](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity). 
1. Aggiungere l'identità al ruolo [mittente dati del bus di servizio di Azure](/service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) nello spazio dei nomi del bus di servizio
1. [Abilitare l'impostazione **Consenti a servizi Microsoft attendibili di ignorare questo firewall** sullo spazio dei nomi del bus di servizio](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. [Configurare la sottoscrizione di eventi](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) che usa una coda o un argomento del bus di servizio come endpoint per usare l'identità assegnata dal sistema.

## <a name="deliver-events-to-storage"></a>Recapitare gli eventi all'archiviazione 
Per recapitare gli eventi alle code di archiviazione usando l'identità gestita, seguire questa procedura:

1. [Abilitare l'identità assegnata dal sistema per un argomento o un dominio](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity).
1. Aggiungere l'identità al ruolo [mittente del messaggio dati della coda di archiviazione](../storage/common/storage-auth-aad-rbac-portal.md) nella coda di archiviazione di Azure.
1. [Configurare la sottoscrizione di eventi](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) che usa una coda o un argomento del bus di servizio come endpoint per usare l'identità assegnata dal sistema.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come recapitare gli eventi usando un'identità gestita, vedere [recapito di eventi usando un'identità gestita](managed-service-identity.md). 
