---
title: Piani di servizio e quote per il cloud Spring di Azure
description: Informazioni sulle quote di servizio e sui piani di servizio per Azure Spring cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878695"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quote e piani di servizio per Azure Spring cloud

**Questo articolo si applica a:** ✔️ Java ✔️ C#

Tutti i servizi di Azure impostano i limiti e le quote predefiniti per le risorse e le funzionalità.   Azure Spring cloud offre due piani tariffari: Basic e standard. In questo articolo vengono illustrati i limiti dettagliati per entrambi i livelli.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Livelli di servizio e limiti di Azure Spring cloud

| Risorsa | Ambito | Basic | Standard
------- | ------- | -------
vCPU | per istanza di app | 1 | 4
Memoria | per istanza di app | 2 GB | 8 GB
Istanze del servizio cloud Spring di Azure | per area per sottoscrizione | 10 | 10
Totale istanze app | per istanza del servizio cloud di Azure Spring | 25 | 500
Domini personalizzati | per istanza del servizio cloud di Azure Spring | 0 | 25 
Volumi permanenti | per istanza del servizio cloud di Azure Spring | app da 1 GB/app x 10 | App 50 GB/app x 10

> [!TIP]
> I limiti elencati per le istanze totali dell'app per ogni istanza del servizio si applicano alle app e alle distribuzioni in qualsiasi stato, incluso lo stato interrotto. Eliminare le app o le distribuzioni che non sono in uso.

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare i limiti predefiniti. Se l'installazione richiede un aumento, [creare una richiesta di supporto](../azure-portal/supportability/how-to-create-azure-support-request.md).
