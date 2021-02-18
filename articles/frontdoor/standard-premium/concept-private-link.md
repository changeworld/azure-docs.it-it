---
title: Proteggi la tua origine con un collegamento privato in Azure front door standard/Premium (anteprima)
description: In questa pagina vengono fornite informazioni su come proteggere la connettività all'origine tramite un collegamento privato.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: dead60b9d8e0872f3d46b1f223ccf5e6697cbd90
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100058"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Proteggi la tua origine con un collegamento privato in Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizzare i [documenti di Azure front door](../front-door-overview.md).

## <a name="overview"></a>Panoramica

Il [collegamento privato di Azure](../../private-link/private-link-overview.md) consente di accedere ai servizi PaaS di Azure e ai servizi ospitati di Azure tramite un endpoint privato nella rete virtuale. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Lo SKU di Azure front door Premium può connettersi all'origine usando il servizio di collegamento privato. Le applicazioni possono essere ospitate nella rete virtuale privata o dietro un servizio PaaS, non accessibili da Internet pubblico.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Architettura degli endpoint privati di front door":::

Quando si Abilita il collegamento privato alla propria origine nella configurazione di Azure front door Premium, la porta anteriore crea un endpoint privato per conto dell'utente dalla rete privata regionale della porta anteriore. Questo endpoint è gestito da Azure front door. Si riceverà una richiesta di endpoint privato di Azure front door per il messaggio di approvazione all'origine. Una volta approvata la richiesta, viene assegnato un indirizzo IP privato dalla rete virtuale della porta anteriore, il traffico tra la porta anteriore di Azure e l'origine attraversa il collegamento privato stabilito con backbone di rete di Azure. Il traffico in ingresso verso l'origine è ora protetto quando proviene dal front-end di Azure.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Abilita Endpoint privato":::

Azure front door Premium supporta vari tipi di origine. Se l'origine è ospitata in un set di macchine virtuali nella rete privata, è necessario creare prima un servizio di bilanciamento del carico standard interno, abilitare il servizio di collegamento privato al servizio di bilanciamento del carico standard e quindi selezionare tipo di origine personalizzato. Per configurazione collegamento privato selezionare "Microsoft. Network/PrivateLinkServices come tipo di risorsa. Per i servizi PaaS, ad esempio app Web di Azure e account di archiviazione, è possibile abilitare il servizio di collegamento privato dai servizi corrispondenti e selezionare Microsoft. Web/Sites per l'app Web e Microsoft. storage/StorageAccounts per i tipi di servizio di collegamento privato dell'account di archiviazione.

## <a name="limitations"></a>Limitazioni

Gli endpoint privati di Azure front door sono disponibili nelle aree seguenti durante l'anteprima pubblica: Stati Uniti orientali, Stati Uniti occidentali e Stati Uniti centro-meridionali.

Per la migliore latenza, è consigliabile selezionare sempre un'area di Azure più vicina all'origine quando si sceglie di abilitare l'endpoint di collegamento privato front door.

Gli endpoint privati di Azure front door vengono gestiti dalla piattaforma e sotto la sottoscrizione di Azure front door. Lo sportello anteriore di Azure consente connessioni di collegamento privato alla stessa sottoscrizione cliente usata per creare il profilo di porta anteriore.

## <a name="next-steps"></a>Passaggi successivi

* Per connettere Azure front door Premium alle macchine virtuali usando il servizio di collegamento privato, vedere [creare un endpoint privato](../../private-link/create-private-endpoint-portal.md).
* Per connettere Azure front door Premium all'app Web tramite il servizio di collegamento privato, vedere [connettersi a un'app Web con un endpoint privato](../../private-link/tutorial-private-endpoint-webapp-portal.md).
* Per connettere Azure front door Premium all'account di archiviazione tramite il servizio di collegamento privato, vedere [connettersi a un account di archiviazione usando un endpoint privato](../../private-link/tutorial-private-endpoint-storage-portal.md).
